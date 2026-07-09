У тебя есть `List<Employee>`, и ты хочешь его отсортировать:

```java
List<Employee> staff = loadEmployees();
Collections.sort(staff); // НЕ КОМПИЛИРУЕТСЯ
```

Компилятор отказывается: `Employee` не реализует `Comparable`. И это не придирка — это честный вопрос: «а в каком порядке сортировать-то?» По имени? По зарплате? По возрасту? У `int` ответ очевиден, у `String` — лексикографический, а у `Employee` естественного порядка просто нет, и Java не собирается его выдумывать.

Из этого тупика два выхода, и они и есть вся тема. Либо ты **вшиваешь порядок в сам тип** — `Employee` реализует `Comparable<Employee>`, и тогда у него появляется ровно один, канонический порядок. Либо ты **подаёшь порядок снаружи** — отдельным объектом `Comparator<Employee>`, и таких объектов можешь налепить сколько хочешь: по имени, по зарплате, по отделу-потом-возрасту.

**Mental model.** Это не две технологии, а два ответа на два разных вопроса. «У типа ЕСТЬ один естественный порядок?» → `Comparable`. «Мне нужен КАКОЙ-ТО порядок, возможно один из многих?» → `Comparator`. Вся стандартная библиотека — `sort`, `TreeMap`, `PriorityQueue`, `Stream.sorted`, `Collections.max` — везде, где нужен порядок, принимает любой из двух.

> [!tip] 💡 Из C#  
> Раскол тебе знаком один в один — `Comparable<T>` ↔ `IComparable<T>`, `Comparator<T>` ↔ `IComparer<T>`. Ментальная модель переносится без потерь, расхождения начнутся в деталях API и в стабильности сортировок — дойдём.

#### Две сигнатуры — два взгляда на сравнение

```java
public interface Comparable<T> {
    int compareTo(T other);      // сравниваю СЕБЯ с другим
}

public interface Comparator<T> {
    int compare(T a, T b);       // сравниваю ДВУХ чужих
    // + куча default/static методов, о них ниже
}
```

`compareTo` — метод объекта: `this` против `other`, один вход. `compare` — внешний судья: ему подают двоих, сам он ни одним из них не является. Из этой разницы вытекает всё остальное: `Comparable` физически живёт внутри класса, поэтому он один; `Comparator` — отдельный объект, поэтому их сколько угодно.

И ещё одно следствие: у `Comparator` ровно один абстрактный метод → это функциональный интерфейс → его можно писать лямбдой:

```java
Comparator<Employee> bySalary = (a, b) -> Integer.compare(a.salary(), b.salary());
```

> [!tip] 💡 Из C#  
> Лямбда-`Comparator` ≈ делегат `Comparison<T>`, который ты передавал в `List<T>.Sort`. Только в Java это не отдельный тип-делегат, а тот же интерфейс `Comparator` — лямбда просто реализует его SAM-метод.

#### Контракт возврата

Оба метода возвращают `int`, и контракт у них общий (и идентичный C#-ному `CompareTo`): отрицательное → первый меньше, ноль → равны по порядку, положительное → первый больше. Плюс три свойства, без которых сортировка математически бессмысленна: антисимметрия, транзитивность, согласованность.

![[Pasted image 20260709155022.png]]

Разберём знак и свойства по отдельности.

> [!warning] ⚠️ Подводные камни контракта
> 
> 1. **Контракт говорит «отрицательное», а не «−1».** `("b").compareTo("a")` у `String` вернёт `1` (проверено), но разница `char`-кодов в других случаях даст и `25`, и `−7`. Код вида `if (cmp == -1)` — бомба замедленного действия. Проверяй знак: `cmp < 0`.
> 2. **Антисимметрия:** `sgn(cmp(a,b)) == −sgn(cmp(b,a))` — если a < b, то b > a.
> 3. **Транзитивность:** a < b и b < c → a < c.
> 4. **Согласованность:** если `cmp(a,b) == 0`, то a и b одинаково сравниваются со всеми остальными.
> 
> Это не «рекомендации» — TimSort реально опирается на эти свойства и, поймав нарушение, кидает исключение в рантайме. Покажу в граничных случаях.

#### Comparable: вшиваем естественный порядок

```java
public record Employee(String name, String dept, int age, int salary)
        implements Comparable<Employee> {

    @Override
    public int compareTo(Employee other) {
        return this.name.compareTo(other.name); // естественный порядок — по имени
    }
}
```

Теперь `Collections.sort(staff)` компилируется, `TreeSet<Employee>` работает без конструктора-компаратора, `Collections.max(staff)` находит максимум. Все они под капотом зовут твой `compareTo`.

Обрати внимание на дженерик: `Comparable<Employee>`, не сырой `Comparable`. Без параметра ты получишь `compareTo(Object)` с кастом внутри — legacy-стиль до Java 5, в новом коде так не пишут. И сигнатуры библиотечных методов это закрепляют: `Collections.sort` объявлен как `<T extends Comparable<? super T>> void sort(List<T> list)` — bounded type из урока 7.2 в боевом применении. Про `? super` — отдельно в граничных случаях, там спрятан красивый PECS.

> [!tip] 💡 Из C#  
> Один в один `IComparable<T>` с `CompareTo`. Даже совет тот же: реализуешь — делай порядок согласованным с `Equals`, иначе сортированные структуры начнут чудить. В Java это чудение покажу живьём на `BigDecimal`.

#### Comparator: эволюция от боли к комфорту

Вот как выглядел компаратор «по зарплате, по убыванию» до Java 8 — анонимным классом:

```java
Comparator<Employee> bySalaryDesc = new Comparator<Employee>() {
    @Override
    public int compare(Employee a, Employee b) {
        return Integer.compare(b.salary(), a.salary());
    }
};
```

Шесть строк церемонии ради одной строки смысла. Java 8 ударила по этому с двух сторон: лямбды убрали церемонию, а `default`/`static`-методы в интерфейсах позволили навесить на `Comparator` целый комбинаторный API. И вот это — место, где Java сегодня заметно богаче C#.

Сквозной пример. Хочу отсортировать сотрудников по отделу, внутри отдела — по возрасту по убыванию, при равном возрасте — по зарплате:

```java
import static java.util.Comparator.*;

staff.sort(comparing(Employee::dept)
        .thenComparing(Employee::age, reverseOrder())
        .thenComparingInt(Employee::salary));
```

Запускал на JDK 21, для четырёх сотрудников (`Anna/Payments/29/5200`, `Boris/Search/34/6100`, `Clara/Payments/29/5800`, `Dmitry/Search/41/6100`) вывод такой (перепроверено):

```
Anna   Payments 29 5200
Clara  Payments 29 5800
Dmitry Search   41 6100
Boris  Search   34 6100
```

Payments раньше Search (по алфавиту), внутри Payments Анна и Клара одного возраста — решила зарплата, внутри Search Дмитрий старше — пошёл первым. Разбираем кирпичики:

- `comparing(Employee::dept)` — «извлеки ключ и сравни ключи». Принимает функцию-экстрактор, возвращает готовый `Comparator<Employee>`. Ключ должен быть `Comparable` сам по себе.
- `.thenComparing(...)` — тай-брейкер: применяется только когда предыдущий уровень дал ноль. Цепочка любой длины.
- `reverseOrder()` / `.reversed()` — первый это статический «обратный естественный порядок» (удобен как второй аргумент `thenComparing`), второй — `default`-метод, разворачивающий уже собранный компаратор целиком.
- `naturalOrder()` — статический компаратор, делегирующий в `compareTo`. Полезен, когда API требует `Comparator`, а тебе нужен именно естественный порядок: `nullsFirst(naturalOrder())`.
- `comparingInt` / `comparingLong` / `comparingDouble` — то же, что `comparing`, но для примитивных ключей без боксинга. Почему это не косметика — через минуту, в перформансе.
- `nullsFirst(cmp)` / `nullsLast(cmp)` — обёртки, которые сначала разруливают `null` (считают его меньше/больше всего), а не-null пары отдают внутреннему компаратору. Проверено: `[Boris, null, Anna]` после `sort(nullsFirst(naturalOrder()))` даёт `[null, Anna, Boris]`.

И ключевое свойство всей этой машинерии: результат — **самостоятельный объект**. Его можно положить в константу, переиспользовать, передать в три разных места:

```java
public static final Comparator<Employee> DEFAULT_ORDER =
        comparing(Employee::dept).thenComparingInt(Employee::salary);
```

> [!tip] 💡 Из C#  
> `comparing().thenComparing()` концептуально ≈ LINQ `OrderBy().ThenBy()`, но с важным сдвигом. LINQ-упорядочивание — инлайновая операция над последовательностью: `ThenBy` живёт только в цепочке запроса, отдельно его в руки не возьмёшь. Java-цепочка собирает переиспользуемый объект — ближе к тому, как если бы ты руками собирал `IComparer<T>`, только без класса-обёртки. Один раз собрал — подаёшь в `sort`, в `TreeMap`, в `PriorityQueue`.

#### Кто и что принимает в библиотеке

Везде, где библиотеке нужен порядок, она берёт либо естественный, либо внешний:

```java
Collections.sort(staff);                  // естественный (требует Comparable)
staff.sort(DEFAULT_ORDER);                // внешний; List.sort — Java 8
Collections.sort(staff, DEFAULT_ORDER);   // то же самое, старый вход

new TreeSet<Employee>();                  // естественный
new TreeSet<>(DEFAULT_ORDER);             // внешний — из конструктора
new PriorityQueue<>(comparingInt(Employee::salary)); // куча по зарплате

staff.stream().sorted();                  // естественный
staff.stream().sorted(DEFAULT_ORDER);     // внешний
Collections.max(staff, comparing(Employee::salary)); // max тоже про порядок
```

`TreeMap`/`TreeSet`/`PriorityQueue` принимают компаратор **только в конструкторе** — порядок это часть структуры дерева/кучи, его нельзя поменять на лету. Если ни компаратора нет, ни `Comparable` элемент не реализует — получишь `ClassCastException` при первой же вставке второго элемента, в рантайме, не на компиляции. Stream здесь только как точка использования — сам Streams API впереди отдельным блоком (глава 10).

#### Перформанс

Первое: `compareTo`/`compare` вызывается O(n log n) раз за сортировку. Миллион элементов — около двадцати миллионов сравнений. Любая дрянь внутри компаратора — аллокация, форматирование строки, поход в map — умножается на двадцать миллионов. Держи его дешёвым.

Второе — прямой привет из урока про автобоксинг (7.1.4). `comparing(Employee::salary)`, где `salary()` возвращает `int`, обязан подогнать ключ под `Comparable` — то есть **боксит `int` в `Integer` на каждом сравнении**. `comparingInt(Employee::salary)` использует `ToIntFunction` и сравнивает примитивы напрямую, без единой аллокации. Правило: примитивный ключ → примитивная версия комбинатора, всегда.

Третье — стабильность:

- `Collections.sort`, `list.sort`, `Arrays.sort(Object[])` — **TimSort, стабильна** (это контракт в Javadoc, а не деталь реализации);
- `Arrays.sort(int[])` и прочие примитивные перегрузки — **dual-pivot quicksort, НЕ стабильна** (для примитивов это ненаблюдаемо: два равных `int` неразличимы).

Зачем тебе это: стабильность позволяет сортировать **последовательно**. Отсортировал по зарплате, потом по отделу — внутри каждого отдела порядок по зарплате сохранился. С нестабильной сортировкой второй проход перемешал бы первый.

> [!tip] 💡 Из C#  
> Здесь расхождение, которое стоит запомнить явно. В C# `List<T>.Sort` и `Array.Sort` — introsort, **нестабильны**, стабилен только LINQ `OrderBy`. В Java наоборот: основной путь (`list.sort`) стабилен из коробки. Если ты по C#-привычке избегал `Sort` ради стабильности — в Java можно расслабиться.

#### Граничные случаи — здесь живут собес-ловушки

> [!warning] ⚠️ Ловушка №1 (топовая): вычитание  
> Выглядит как элегантный трюк — и молча ломается на переполнении:
> 
> 
> 
> ```java
> Comparator<Integer> broken = (a, b) -> a - b; // НЕ ДЕЛАЙ ТАК
> ```
> 
> Проверено на JDK 21:
> 
> 
> 
> ```java
> broken.compare(-2_000_000_000, 2_000_000_000)  // → 294967296, ПОЛОЖИТЕЛЬНОЕ
> // то есть −2 млрд якобы БОЛЬШЕ +2 млрд
> 
> List<Integer> list = new ArrayList<>(List.of(2_000_000_000, -2_000_000_000));
> list.sort(broken);           // → [2000000000, -2000000000] — порядок сломан
> list.sort(Integer::compare); // → [-2000000000, 2000000000] — корректно
> ```
> 
> `-2_000_000_000 - 2_000_000_000 = -4_000_000_000` не влезает в `int` и заворачивается в `294_967_296`. Правильный инструмент — `Integer.compare(a, b)` (и `Long.compare`, и т.д.): внутри `(x < y) ? -1 : ((x == y) ? 0 : 1)`, переполнения нет в принципе. Effective Java, Item 14: Bloch прямо запрещает вычитание в компараторах.

> [!warning] ⚠️ Ловушка №2: compareTo, несогласованный с equals  
> `TreeSet`/`TreeMap` определяют членство **по compareTo, не по equals** (Javadoc `TreeSet`: «the set uses compareTo... to perform all element comparisons»). Канон — `BigDecimal`, у которого `equals` учитывает scale, а `compareTo` — нет. Проверено:
> 
> java
> 
> ```java
> BigDecimal one = new BigDecimal("1.0"), oneOO = new BigDecimal("1.00");
> one.equals(oneOO);    // false — разный scale
> one.compareTo(oneOO); // 0 — численно равны
> 
> Set<BigDecimal> hashSet = new HashSet<>(); hashSet.add(one); hashSet.add(oneOO);
> // size 2 → [1.0, 1.00]   — верит equals/hashCode
> Set<BigDecimal> treeSet = new TreeSet<>(); treeSet.add(one); treeSet.add(oneOO);
> // size 1 → [1.0]          — верит compareTo, второй "потерялся"
> 
> treeSet.contains(new BigDecimal("1.000")); // true  (!)
> hashSet.contains(new BigDecimal("1.000")); // false
> ```
> 
> Один и тот же интерфейс `Set`, противоположные ответы на одни данные. Контракт `Comparable` рекомендует `(x.compareTo(y) == 0) == x.equals(y)`. Это не жёсткое требование (`BigDecimal` его легально нарушает и документирует), но нарушил — тип ведёт себя по-разному в hash- и tree-коллекциях.

> [!warning] ⚠️ Ловушка №3: double и сырое `<`  
> `NaN` несравним ни с чем: `NaN < x`, `NaN > x`, `NaN == NaN` — всё `false`. Компаратор на сырых сравнениях вернёт для пары с `NaN` ноль («равны»), что рвёт транзитивность. Проверено: список `[3.0, NaN, 1.0, 2.0]` через сырой `< / >`-компаратор так и остаётся `[3.0, NaN, 1.0, 2.0]` — `NaN` «заморозил» соседей. `Double.compare` даёт `[1.0, 2.0, 3.0, NaN]` — у него тотальный порядок: `NaN` больше всего, и `Double.compare(-0.0, 0.0)` возвращает `−1`, хотя `-0.0 == 0.0` это `true`. Для сортировки нужна именно тотальность.

> [!warning] ⚠️ Ловушка №4: нарушенный контракт = рантайм-исключение в проде  
> TimSort использует транзитивность для оптимизаций; нарушишь (классика — компаратор, зависящий от мутабельного состояния, или «умная» логика с противоречиями) — на достаточно большом входе вылетит:
> 
> ```
> java.lang.IllegalArgumentException: Comparison method violates its general contract!
> ```
> 
> Воспроизведено «хаотичным» компаратором: на 1000 элементов вылетает стабильно, на 16 — молчит. Подлость в том, что исключение **зависит от данных**: на маленьких кусках (< 32) TimSort работает бинарной вставкой и нарушения не замечает, поэтому тесты зелёные, а падает на проде на большом массиве.

> [!warning] ⚠️ Ловушка №5: мутация ключа после вставки  
> Положил `Employee` в `TreeSet`, потом поменял поле, по которому он сравнивается — дерево об этом не узнало, элемент сидит не в своём месте, `contains`/`remove` идут бинарным поиском мимо него. Ровно та же история, что мутация ключа с изменившимся `hashCode` в `HashMap` — там теряется бакет, здесь позиция в дереве. Лечение то же: ключи сравнения — иммутабельны.

> [!example] 🔬 Глубже / на синьора — обещанный PECS  
> Смотри на реальную сигнатуру:
> 
> 
> 
> ```java
> static <T extends Comparable<? super T>> T max(Collection<? extends T> coll)
> ```
> 
> Почему `Comparable<? super T>`, а не `Comparable<T>`? Представь `class Manager extends Employee`, где `Comparable<Employee>` реализован на уровне `Employee`. `Manager` умеет сравнивать себя с `Employee` — то есть он `Comparable<Employee>`, но НЕ `Comparable<Manager>`. С жёстким `Comparable<T>` вызов `Collections.max(managers)` не скомпилировался бы, хотя сравнивать менеджеров мы прекрасно умеем унаследованным методом. `? super T` говорит: «достаточно, чтобы T умел сравниваться хотя бы со своим супертипом». Компаратор — потребитель (consumer) сравниваемых значений, отсюда `super`. Это PECS из урока про wildcards (7.3), пойманный в дикой природе.

#### Почему обоим языкам мало `Comparable`

Тут стоит развернуть C#-мост целиком, потому что типичная посылка «в .NET я обходился одним `IComparable`» — **неверна**. Ты пользовался внешним компаратором постоянно, просто не звал его этим словом. Каждый раз, когда ты писал `OrderBy(e => e.Salary).ThenBy(...)`, или `.Sort((a,b) => ...)`, или `new SortedSet<>(Comparer<T>.Create(...))` — ты задавал порядок снаружи типа. `OrderBy(keySelector)` существует ровно потому, что `IComparable` одного не хватает.

> [!tip] 💡 Из C# — раскол один в один
> 
> |.NET|Java|что это|
> |---|---|---|
> |`IComparable<T>.CompareTo`|`Comparable.compareTo`|порядок вшит в тип, один|
> |`IComparer<T>.Compare`|`Comparator.compare`|внешний объект-судья|
> |`Comparison<T>` (делегат)|лямбда-`Comparator`|компаратор одной строкой|
> |`OrderBy(e => e.Key)`|`comparing(E::getKey)`|порядок по извлечённому ключу|
> 
> Единственное реальное расхождение, которое Java добавила поверх знакомого раскола — **комбинаторный API** (`comparing().thenComparing().reversed()`), собирающий компаратор как переиспользуемый объект. В C# многоуровневую сортировку ты выражаешь через `OrderBy().ThenBy()` _внутри_ LINQ-запроса — это инлайн над последовательностью, отдельно в переменную не положишь. Java-цепочка даёт объект `Comparator`, который сохраняешь в константу и подаёшь в `sort`, в `TreeMap` и в `PriorityQueue` — три места, один объект. В .NET аналога «из коробки» нет; там ты бы собирал `Comparer<T>.Create(...)` руками.

Так что вопрос не «зачем Java добавила Comparator», а «почему **обоим** языкам мало `Comparable`». Три фундаментальные причины, не про синтаксис:

**Причина 1: у типа порядков больше одного.** `Employee` сортируют по имени, по зарплате, по возрасту, по отделу-потом-стажу. `compareTo` физически один метод — отдаёт ровно один порядок. Остальные — только наружу. В C# та же боль: ты не реализуешь `IComparable` пять раз, ты пишешь пять `OrderBy`.

**Причина 2: тип не твой.** `String` уже реализует `Comparable` — лексикографически. А тебе надо по длине, или case-insensitive, или по последней букве. Добавить `String` свой `compareTo` нельзя — класс чужой и `final`. Единственный выход — внешний компаратор:

![[Pasted image 20260709155107.png]]

Один тип `String`, три разных порядка. Встроенный `compareTo` ставит `Cherry` и `Fig` перед `apple` — заглавные в ASCII идут раньше строчных. Это «естественный» порядок, но почти никогда не тот, что нужен в UI; нужный задаётся снаружи — и в C# ты ровно так же тянулся к `StringComparer.OrdinalIgnoreCase` или `OrderBy(s => s.Length)`.

**Причина 3: порядок — свойство ситуации, а не типа.** Сортировать товары по релевантности или по цене решает пользователь в рантайме, а не автор класса `Product`. Локаль-чувствительная сортировка строк (немецкое `ä` рядом с `a` или после `z`?) зависит от региона, а не от типа `String`. Зашить такое в `compareTo` нельзя в принципе — на момент написания класса ты не знаешь, какой порядок понадобится. В Java это `Collator`, в C# — `CultureInfo`-компараторы. Оба языка вынесли это наружу по одной причине.

Когда тогда вообще нужен `Comparable`? Когда у типа есть _один бесспорный, контекстно-независимый_ порядок: числа, даты, деньги, версии, перечисления по приоритету. Тогда вшиваешь — и `TreeSet`, `Collections.max`, `sorted()` работают без лишних аргументов. Для всего остального — где порядков много, или тип чужой, или порядок зависит от ситуации — внешний `Comparator`.

### На собесе

> [!question]- В чём разница между Comparable и Comparator?  
> **RU:** `Comparable` — естественный порядок, вшитый в тип: метод `compareTo`, сравнивает `this` с другим, порядок один. `Comparator` — внешний объект-судья: `compare(a, b)`, порядков сколько угодно, с Java 8 это функциональный интерфейс с комбинаторами (`comparing`, `thenComparing`, `reversed`, `nullsFirst`). Вся библиотека принимает любой из двух.
> 
> **EN:** `Comparable` is a type's natural, built-in order: the `compareTo` method compares `this` against another, and there's exactly one order. `Comparator` is an external judge object: `compare(a, b)`, as many orders as you like, and since Java 8 it's a functional interface with combinators (`comparing`, `thenComparing`, `reversed`, `nullsFirst`). Every ordering point in the library accepts either one.
> 
> _Trap:_ «в .NET хватало `IComparable`?» → нет, `OrderBy`/`IComparer` — это и есть внешний компаратор, просто под другим именем.

> [!question]- Comparator через `(a, b) -> a.getId() - b.getId()` — код-ревью пройдёт?  
> **RU:** Нет: вычитание `int` переполняется на парах с разными знаками у больших значений, знак результата врёт, порядок ломается молча. Правильно — `Integer.compare` или `comparingInt(Entity::getId)`.
> 
> **EN:** No: `int` subtraction overflows on pairs with large opposite-sign values, the result's sign lies, and the order breaks silently. Use `Integer.compare` or `comparingInt(Entity::getId)`.
> 
> _Follow-up:_ «а если id гарантированно маленькие и положительные?» → формально работает, но это знание о данных, нигде не закреплённое, сломается при первом отрицательном id; ревью не пройдёт, исправление бесплатное. _Второй:_ «а для `long`?» → та же проблема плюс сужение `long`-разности в `int`; `Long.compare`.

> [!question]- Кладу в TreeSet два неравных по equals объекта — размер 1. Как так?  
> **RU:** `TreeSet` определяет дубликаты по `compareTo`/компаратору, не по `equals`. Если `compareTo` вернул 0 — это дубликат, `equals` никто не спросит. Канон — `BigDecimal("1.0")` и `BigDecimal("1.00")`.
> 
> **EN:** `TreeSet` decides duplicates by `compareTo`/the comparator, not `equals`. If `compareTo` returns 0, it's a duplicate and `equals` is never consulted. The canonical case is `BigDecimal("1.0")` vs `BigDecimal("1.00")`.
> 
> _Follow-up:_ «как заставить их различать?» → добить компаратор тай-брейкером, согласованным с equals, например `.thenComparing(BigDecimal::scale)`; или признать, что численное равенство — и есть нужная семантика.

> [!question]- Что за `IllegalArgumentException: Comparison method violates its general contract` и почему не ловится тестами?  
> **RU:** TimSort валидирует следствия транзитивности/антисимметрии при слиянии ранов; нарушающий контракт компаратор (нетранзитивный, недетерминированный, читающий мутабельное состояние) триггерит проверку. Не ловится тестами, потому что на входах < 32 элементов TimSort деградирует в бинарную вставку и нарушений не видит — нужен большой и «неудачный» вход.
> 
> **EN:** TimSort validates consequences of transitivity/antisymmetry while merging runs; a contract-violating comparator (non-transitive, non-deterministic, reading mutable state) trips the check. Tests miss it because below 32 elements TimSort falls back to binary insertion and never notices — you need a large, unlucky input.
> 
> _Follow-up:_ «типичный баг?» → компаратор с противоречивыми ветками: например `null` обрабатывается как «равен всему» — тогда `a == null == b`, но `a` и `b` между собой не равны, транзитивность рвётся. Лечится `nullsFirst`/`nullsLast`.

> [!question]- Почему `Collections.max` объявлен с `Comparable<? super T>`?  
> **RU:** Чтобы работали наследники: `Manager extends Employee implements Comparable<Employee>` — менеджер сравним через супертип. `? super` = PECS: сравнение потребляет значения.
> 
> **EN:** So subtypes work: `Manager extends Employee implements Comparable<Employee>` — a manager is comparable via its supertype. `? super` = PECS: comparison consumes values.
> 
> _Follow-up:_ «а `Comparator<? super T>` в перегрузке `max` зачем?» → то же с другой стороны: `Comparator<Employee>` обязан годиться для `Collection<Manager>` — компаратор супертипа умеет сравнивать подтипы.

> [!question]- `list.sort(comparing(Employee::getSalary))` тормозит на больших объёмах. Куда смотреть?  
> **RU:** Два кандидата: боксинг — если `getSalary` возвращает примитив, `comparing` боксит ключ на каждом из O(n log n) сравнений (лечится `comparingInt`); и дорогой экстрактор — если ключ вычисляется (парсинг, конкатенация), он пересчитывается при каждом сравнении, а не один раз на элемент.
> 
> **EN:** Two suspects: boxing — if `getSalary` returns a primitive, `comparing` boxes the key on each of O(n log n) comparisons (fix with `comparingInt`); and an expensive extractor — if the key is computed (parsing, concatenation), it's recomputed on every comparison rather than once per element.
> 
> _Follow-up:_ «как пересчитывать ключ раз на элемент?» → предвычислить: смапить в пары (ключ, элемент), отсортировать по ключу, развернуть — schwartzian transform; в простых случаях достаточно кешировать ключ в объекте.

> [!question]- Стабильна ли сортировка в Java? Сортирую дважды: по зарплате, потом по отделу — что получу?  
> **RU:** `Collections.sort` и `list.sort` — TimSort, стабильна по контракту; `Arrays.sort` для примитивов — dual-pivot quicksort, нестабильна, но для примитивов это ненаблюдаемо. Поэтому двойная сортировка даёт группировку по отделам с сохранённым внутри порядком по зарплате.
> 
> **EN:** `Collections.sort` and `list.sort` are TimSort, stable by contract; `Arrays.sort` for primitives is dual-pivot quicksort, unstable, but unobservably so for primitives. So sorting twice groups by department while preserving the salary order within each group.
> 
> _Follow-up:_ «а в C# с `List.Sort` так можно?» → нет, introsort нестабилен, нужен `OrderBy().ThenBy()`. _Второй:_ «зачем тогда `thenComparing`?» → один проход вместо двух и явное намерение; двойная сортировка — трюк для случаев, когда второй критерий появляется позже.

---

Готово. Что сделал против сырья: наложил разметку (`[!tip]`/`[!warning]`/`[!example]`/`[!question]-`), поднял «два ответа на два вопроса» в явный **Mental model**, свёл шесть RU-вопросов и финальный RU-конспект в единые bilingual `[!question]-` блоки (добавил EN-ответы — по конвенции для EU-собесов), убрал «Практику» и «Замок темы» (глава закрывается на «На собесе»), перенёс большой C#-разбор перед собес-блоком.

Две потерянные картинки восстановил как SVG: контракт `compareTo` (знак + три свойства) и `String` с одним встроенным порядком против трёх нужных. Обе self-contained под Notion-экспорт.

Весь код с пометками «запускал на JDK 21» перепроверил на JDK 21 — все 16 числовых/строковых результатов и падение TimSort сошлись до символа, ничего править не пришлось.