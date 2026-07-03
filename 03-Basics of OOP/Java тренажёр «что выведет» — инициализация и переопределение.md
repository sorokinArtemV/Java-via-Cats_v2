37 задач с разворачиваемыми ответами. Все выводы проверены запуском на JDK 21.

**Как работать:** предскажи вывод _целиком, построчно_, и только потом раскрывай ответ. Не пытайся вычислить в голове — бери листок и гоняй процедуру:

1. **Проход static** (если класс используется впервые): от верхнего родителя к потомку, выписываешь вывод каждого `static`-поля и `static {}`-блока сверху вниз.
2. **Проход instance** (на каждый `new`): от родителя к потомку, на каждом уровне — поля и `{}`-блоки сверху вниз, затем тело конструктора.

Склеиваешь два списка — это ответ.

---

## Часть 1. Инициализация — основы

### 1. Четыре вида инициализаторов в одном классе

```java
class Box {
    static int a = log("static field a");
    static {
        log("static block");
    }
    int b = log("instance field b");
    {
        log("instance block");
    }
    Box() {
        log("constructor");
    }
    static int log(String s) {
        System.out.println(s);
        return 0;
    }
}

new Box();
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
static field a
static block
instance field b
instance block
constructor
```

Фаза класса (static-поле и static-блок по порядку, один раз), затем фаза объекта (instance-поле, instance-блок, тело конструктора). Поля и блоки одного уровня — единая последовательность по тексту.

</details>

### 2. `new Box()` дважды


```java
new Box();
new Box();
```

**Что добавится при втором вызове? (класс `Box` из задачи 1)**

<details> <summary>Ответ</summary>

```
static field a
static block
instance field b
instance block
constructor
instance field b
instance block
constructor
```

Инициализация класса — один раз. Второй `new` повторяет только instance-часть и конструктор; static не выполняется.

</details>

### 3. Порядок с наследованием

```java
class Animal {
    static {
        System.out.println("Animal static");
    }
    {
        System.out.println("Animal instance");
    }
    Animal() {
        System.out.println("Animal ctor");
    }
}

class Dog extends Animal {
    static {
        System.out.println("Dog static");
    }
    {
        System.out.println("Dog instance");
    }
    Dog() {
        System.out.println("Dog ctor");
    }
}

new Dog();
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
Animal static
Dog static
Animal instance
Animal ctor
Dog instance
Dog ctor
```

static родителя → static потомка. Затем `super()` полностью отрабатывает родителя (instance + ctor), и только потом instance и ctor потомка.

</details>

### 4. Виртуальный вызов из конструктора

```java
class Base {
    Base() {
        System.out.println("value = " + value());
    }
    int value() {
        return 1;
    }
}

class Derived extends Base {
    int x = 42;

    @Override
    int value() {
        return x;
    }
}

new Derived();
```

**Что напечатает — `1`, `42` или что-то ещё?**

<details> <summary>Ответ</summary>

```
value = 0
```

Метод виртуальный → вызывается `Derived.value()`, но поле `x` присваивается уже после возврата из `super()`. В момент вызова `x` держит дефолт `0`.

</details>

### 5. `static`-метод и тип ссылки

```java
class Shape {
    static String name() {
        return "Shape";
    }
}

class Circle extends Shape {
    static String name() {
        return "Circle";
    }
}

Shape s = new Circle();
System.out.println(s.name());
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
Shape
```

`static`-методы не виртуальны (method hiding). Резолвятся по статическому типу ссылки (`Shape`), а не по реальному объекту.

</details>

### 6. Ленивая инициализация: константа vs обёртка

```java
class A {
    static final int LIMIT = 100;
    static {
        System.out.println("A init");
    }
}

class B {
    static final Integer BOXED = 100;
    static {
        System.out.println("B init");
    }
}

System.out.println(A.LIMIT);
System.out.println(B.BOXED);
```

**Сколько строк `init` и в каком порядке?**

<details> <summary>Ответ</summary>

```
100
B init
100
```

`A.LIMIT` — compile-time константа примитива, инлайнится компилятором, класс `A` не инициализируется. `B.BOXED` типа `Integer` — не константа, обращение будит класс `B` (поэтому «B init» раньше значения).

</details>

### 7. Цепочка `this()` / `super()`

java

```java
class Base {
    Base() {
        System.out.println("Base()");
    }
    Base(int x) {
        System.out.println("Base(int)");
    }
}

class Sub extends Base {
    Sub() {
        this(1);
        System.out.println("Sub()");
    }
    Sub(int x) {
        super(x);
        System.out.println("Sub(int)");
    }
}

new Sub();
```

**Что выведет? Сработает ли `Base()`?**

<details> <summary>Ответ</summary>

```
Base(int)
Sub(int)
Sub()
```

`Sub()` → `this(1)` → `Sub(int)` → `super(x)` → `Base(int)`; затем тела доигрывают в обратном порядке. `Base()` не вызывается: `Sub(int)` явно зовёт `super(x)`, а `Sub()` ушёл через `this(1)`.

</details>

### 8. Instance-блок видит ещё не присвоенное поле

```java
class Order {
    int total = compute("total", 10);
    {
        System.out.println("block: total=" + total + ", tax=" + this.tax);
    }
    int tax = compute("tax", 2);

    static int compute(String n, int v) {
        System.out.println("compute " + n);
        return v;
    }
}

new Order();
```

**Что напечатает блок для `tax`?**

<details> <summary>Ответ</summary>

```
compute total
block: total=10, tax=0
compute tax
```

Инициализаторы идут сверху вниз. К моменту блока `total` уже `10`, а `tax` объявлен ниже и держит дефолт `0`. Написано `this.tax` намеренно: голое `tax` (ссылка вперёд) не скомпилировалось бы.

</details>

### 9. Скомпилируется?

```java
class Account {
    Account(String owner) {
    }
}

Account a = new Account();
```

**Скомпилируется ли последняя строка?**

<details> <summary>Ответ</summary>

```
ошибка компиляции:
required: String, found: no arguments
```

Как только есть хоть один явный конструктор, default-конструктор без аргументов не генерируется. Подходящего `Account()` нет.

</details>

---

## Часть 2. Наследование и переопределение

### 10. `static` method hiding + каст

```java
class P {
    static String s() {
        return "P.s";
    }
}

class Ch extends P {
    static String s() {
        return "Ch.s";
    }
}

P p = new Ch();
System.out.println(p.s());
System.out.println(((Ch) p).s());
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
P.s
Ch.s
```

`static`-методы по типу ссылки. `p` объявлена как `P` → `P.s`. Каст к `Ch` меняет статический тип → `Ch.s`.

</details>

### 11. Field hiding + виртуальный геттер

```java
class Pa {
    int x = 1;
    int getX() {
        return x;
    }
}

class Ci extends Pa {
    int x = 2;
    int getX() {
        return x;
    }
}

Pa o = new Ci();
System.out.println(o.x);
System.out.println(o.getX());
System.out.println(((Ci) o).x);
```

**Один объект — три обращения. Что выведет?**

<details> <summary>Ответ</summary>

```
1
2
2
```

`o.x` — поле по типу ссылки `Pa` → 1. `o.getX()` — виртуальный метод → `Ci.getX()` → `Ci.x` → 2. Каст влияет на поле (2), но не повлиял бы на метод.

</details>

### 12. Мнимый override (другая сигнатура)

```java
class Animal {
    String d(Animal a) {
        return "Animal.d(Animal)";
    }
}

class Dog extends Animal {
    String d(Dog g) {
        return "Dog.d(Dog)";
    }
}

Animal a = new Dog();
System.out.println(a.d(new Dog()));
Dog dg = new Dog();
System.out.println(dg.d(new Dog()));
```

**Обоим передаём `new Dog()`. Что выведет?**

<details> <summary>Ответ</summary>

```
Animal.d(Animal)
Dog.d(Dog)
```

`d(Dog)` — перегрузка, не переопределение. Через ссылку `Animal` виден только `d(Animal)` (выбор перегрузки в compile-time). Через ссылку `Dog` видны обе, специфичнее `d(Dog)`.

</details>

### 13. `private` не переопределяется

```java
class Base {
    String run() {
        return helper();
    }
    private String helper() {
        return "Base.helper";
    }
}

class Derived extends Base {
    String helper() {
        return "Derived.helper";
    }
}

System.out.println(new Derived().run());
```

**Объект — `Derived`. Что выведет?**

<details> <summary>Ответ</summary>

```
Base.helper
```

`Base.helper` — `private`, связан статически на свою реализацию. `Derived.helper` — отдельный метод, который `Base.run()` не видит (переопределить `private` нельзя).

</details>

### 14. `super` — невиртуальный вызов

```java
class L1 {
    String m() {
        return "L1";
    }
}

class L2 extends L1 {
    String m() {
        return "L2->" + super.m();
    }
}

class L3 extends L2 {
    String m() {
        return "L3->" + super.m();
    }
}

System.out.println(new L3().m());
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
L3->L2->L1
```

`super.m()` — единственный невиртуальный вызов instance-метода, статически уходит в непосредственного родителя. `super.super` в Java нет.

</details>

### 15. `static`-вызов внутри instance-метода

```java
class A {
    static String type() {
        return "A.type";
    }
    String describe() {
        return "describe() sees " + type();
    }
}

class B extends A {
    static String type() {
        return "B.type";
    }
}

A a = new B();
System.out.println(a.describe());
```

**Объект `B`. Какой `type()` увидит `describe()`?**

<details> <summary>Ответ</summary>

```
describe() sees A.type
```

`describe()` не переопределён в `B`, выполняется `A.describe()`. Неквалифицированный `type()` — `static`-вызов, связан в compile-time в классе `A`. Реальный тип объекта не смотрится.

</details>

### 16. Виртуальный вызов из ctor + field hiding

java

```java
class Base {
    Base() {
        System.out.println("ctor: getName() = " + getName());
    }
    String getName() {
        return "Base";
    }
}

class Sub extends Base {
    String name = "Sub";

    @Override
    String getName() {
        return name;
    }
}

new Sub();
```

**Что напечатает конструктор?**

<details> <summary>Ответ</summary>

```
ctor: getName() = null
```

`getName()` виртуально уходит в `Sub.getName()`, но поле `name` ещё не присвоено (после `super()`) → дефолт `null`.

</details>

### 17. Скомпилируется с `@Override`?

```java
class Animal {
    String d(Animal a) {
        return "A";
    }
}

class Dog extends Animal {
    @Override
    String d(Dog g) {
        return "D";
    }
}
```

**Скомпилируется ли с аннотацией `@Override`?**

<details> <summary>Ответ</summary>

```
ошибка компиляции:
method does not override or implement
a method from a supertype
```

`d(Dog)` — перегрузка, не переопределение. `@Override` требует реального переопределения и ловит мнимый override на компиляции.

</details>

---

## Часть 3. Инициализация — продвинутое

### 18. static-поля зависят друг от друга

```java
class Config {
    static int base = init("base", 10);
    static int derived = init("derived", base + 5);

    static int init(String s, int v) {
        System.out.println(s + "=" + v);
        return v;
    }
}

int u = Config.derived;
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
base=10
derived=15
```

static-поля сверху вниз. К моменту `derived` поле `base` уже `10`, значит `derived = 15`.

</details>

### 19. Ссылка вперёд у static

```java
class A {
    static int a = A.b + 1;
    static int b = 5;
    static {
        System.out.println("a=" + a + ", b=" + b);
    }
}

int u = A.a;
```

**Чему равны `a` и `b`?**

<details> <summary>Ответ</summary>

```
a=1, b=5
```

`a = A.b + 1` выполняется первым; `b` ещё не инициализирован → дефолт `0` → `a = 1`. Голое `b` (без `A.`) не скомпилировалось бы.

</details>

### 20. Что будит породу

```java
class Parent {
    static {
        System.out.println("Parent static");
    }
}

class Child extends Parent {
    static int VALUE = 42;
    static {
        System.out.println("Child static");
    }
}

System.out.println(Child.VALUE);
```

**Что и в каком порядке?**

<details> <summary>Ответ</summary>

```
Parent static
Child static
42
```

`VALUE` объявлено в `Child`, обращение будит `Child`, а инициализация потомка сперва будит родителя.

</details>

### 21. Унаследованное static-поле (ловушка)

```java
class Parent {
    static int P = 1;
    static {
        System.out.println("Parent init");
    }
}

class Child extends Parent {
    static {
        System.out.println("Child init");
    }
}

System.out.println(Child.P);
```

**Напечатается ли `Child init`?**

<details> <summary>Ответ</summary>

```
Parent init
1
```

Нет, `Child init` не печатается. Обращение к static-полю будит только тот класс, который его _реально объявляет_ (`P` объявлено в `Parent`), даже через имя потомка. JLS §12.4.1.

</details>

### 22. Ловушка из ctor + `final` (с подвохом)

```java
class Base {
    Base() {
        System.out.println("getV()=" + getV());
    }
    int getV() {
        return -1;
    }
}

class Derived extends Base {
    final int v = 99;

    int getV() {
        return v;
    }
}

new Derived();
```

**Будет `0` (как в обычной ловушке) или `99`?**

<details> <summary>Ответ</summary>

```
getV()=99
```

Сюрприз: `99`. `final int v = 99` — compile-time константа, компилятор подставляет литерал `99` прямо в тело `getV()`. Значение «запечено» в байткод, порядок инициализации тут ни при чём. Убери `final` (или сделай инициализатор не-константой) — вернётся `0`.

</details>

### 23. Поле vs тело конструктора

```java
class Base {
    Base() {
        System.out.println("Base ctor");
    }
}

class Sub extends Base {
    int x = log("field x");

    Sub() {
        System.out.println("Sub ctor");
    }

    static int log(String s) {
        System.out.println(s);
        return 0;
    }
}

new Sub();
```

**В каком порядке три строки?**

<details> <summary>Ответ</summary>

```
Base ctor
field x
Sub ctor
```

Инициализатор поля `x` — после возврата из `super()` (отсюда `Base ctor` первым), но до тела конструктора `Sub`.

</details>

### 24. Несколько static-блоков

```java
class A {
    static {
        System.out.println("block 1");
    }
    static int x = log("field");
    static {
        System.out.println("block 2");
    }

    static int log(String s) {
        System.out.println(s);
        return 0;
    }
}

new A();
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
block 1
field
block 2
```

static-блоки и инициализаторы static-полей — одна сплошная последовательность сверху вниз.

</details>

### 25. Массив будит класс?

```java
class A {
    static {
        System.out.println("A init");
    }
}

A[] arr = new A[10];
System.out.println("len=" + arr.length);
```

**Напечатается ли `A init`?**

<details> <summary>Ответ</summary>

```
len=10
```

Нет. `new A[10]` создаёт массив из 10 ссылок (`null`), но ни одного объекта `A` — класс не инициализируется.

</details>

### 26. Полный порядок с наследованием

```java
class Base {
    int b = log("Base.b");

    Base() {
        log("Base()");
    }

    static int log(String s) {
        System.out.println(s);
        return 0;
    }
}

class Sub extends Base {
    int s = log("Sub.s");

    Sub() {
        log("Sub()");
    }
}

new Sub();
```

**Все четыре строки по порядку?**

<details> <summary>Ответ</summary>

```
Base.b
Base()
Sub.s
Sub()
```

`super()` полностью отрабатывает родителя: поле `Base.b`, потом тело `Base()`. Затем поле `Sub.s`, потом тело `Sub()`.

</details>

### 27. Скомпилируется?

```java
class Base {
    Base(int x) {
    }
}

class Sub extends Base {
}

new Sub();
```

**Скомпилируется ли это?**

<details> <summary>Ответ</summary>

```
ошибка компиляции:
constructor Base cannot be applied
required: int, found: no arguments
```

`Sub` получает неявный default-конструктор → неявный `super()` без аргументов → но у `Base` только `Base(int)`. Чинится `Sub() { super(0); }` или добавлением `Base()`.

</details>

---

## Часть 4. Инициализация — добивка

### 28. Три уровня наследования

java

```java
class A {
    static {
        System.out.println("A static");
    }
    A() {
        System.out.println("A ctor");
    }
}

class B extends A {
    static {
        System.out.println("B static");
    }
    B() {
        System.out.println("B ctor");
    }
}

class C extends B {
    static {
        System.out.println("C static");
    }
    C() {
        System.out.println("C ctor");
    }
}

new C();
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
A static
B static
C static
A ctor
B ctor
C ctor
```

Тот же принцип, что и для двух уровней, просто длиннее: весь static сверху вниз, потом все конструкторы сверху вниз.

</details>

### 29. Instance-блок при цепочке `this()`

java

```java
class Widget {
    {
        System.out.println("instance block");
    }

    Widget() {
        System.out.println("Widget()");
    }

    Widget(int x) {
        this();
        System.out.println("Widget(int)");
    }
}

new Widget(5);
```

**Сколько раз выполнится `instance block` и где?**

<details> <summary>Ответ</summary>

```
instance block
Widget()
Widget(int)
```

Instance-блок выполняется один раз — в том конструкторе, который реально зовёт `super()` (это `Widget()`), а не в каждом звене `this()`-цепочки.

</details>

### 30. Исключение в static-инициализаторе

java

```java
class Bomb {
    static int x = boom();

    static int boom() {
        throw new RuntimeException("boom");
    }
}

// первое обращение:
try {
    new Bomb();
} catch (Throwable t) {
    System.out.println(t.getClass().getSimpleName());
}

// второе обращение:
try {
    new Bomb();
} catch (Throwable t) {
    System.out.println(t.getClass().getSimpleName());
}
```

**Какие два исключения?**

<details> <summary>Ответ</summary>

```
ExceptionInInitializerError
NoClassDefFoundError
```

Исключение из static-инициализатора оборачивается в `ExceptionInInitializerError`, и класс навсегда помечается «битым». Любое следующее обращение даёт `NoClassDefFoundError`. Это реальный прод-баг при загрузке конфигов в static.

</details>

### 31. static-поле создаёт экземпляр само

java

```java
class Counter {
    static Counter FIRST = new Counter();
    static int count = 0;

    Counter() {
        count++;
        System.out.println("ctor, count=" + count);
    }
}

System.out.println("final=" + Counter.count);
```

**Чему равно `final`?**

<details> <summary>Ответ</summary>

```
ctor, count=1
final=0
```

`FIRST = new Counter()` выполняется ПЕРВЫМ (по порядку объявления): ctor делает `count++` → 1. Но затем строка `count = 0` выполняется и сбрасывает счётчик в `0`. Классический баг порядка static-инициализации.

</details>

### 32. static-метод через `null`

java

```java
class Helper {
    static String greet() {
        return "hi";
    }
}

Helper h = null;
System.out.println(h.greet());
```

**Будет NPE?**

<details> <summary>Ответ</summary>

```
hi
```

NPE нет. `static`-метод резолвится по типу ссылки в compile-time, значение объекта (`null`) не разыменовывается. Компилятор выдаёт warning, но код работает.

</details>

### 33. Ковариантный тип возврата

java

```java
class Animal {
    Animal reproduce() {
        return new Animal();
    }
}

class Cat extends Animal {
    @Override
    Cat reproduce() {
        return new Cat();
    }
}

Animal a = new Cat();
System.out.println(a.reproduce().getClass().getSimpleName());
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
Cat
```

Ковариантный тип возврата легален (Java 5+): override может сузить тип возврата до подтипа. Вызов виртуальный → `Cat.reproduce()` → возвращает `Cat`.

</details>

### 34. Скрытое поле через `super`

java

```java
class Parent {
    String tag = "parent";
}

class Child extends Parent {
    String tag = "child";

    String show() {
        return tag + " / " + super.tag;
    }
}

System.out.println(new Child().show());
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
child / parent
```

`tag` — поле `Child` (скрывает родительское), `super.tag` — скрытое поле `Parent`. Поля не виртуальны, но оба доступны через `this`/`super`.

</details>

### 35. `instanceof` будит класс?

java

```java
class A {
    static {
        System.out.println("A init");
    }
}

Object o = "hello";
System.out.println(o instanceof A);
```

**Напечатается ли `A init`?**

<details> <summary>Ответ</summary>

```
false
```

`A init` не печатается. `instanceof` не инициализирует класс — это не активное использование. Класс загружается, но static-блок не выполняется.

</details>

### 36. Исключение из инициализатора поля

java

```java
class Risky {
    int x = boom();

    static int boom() {
        throw new IllegalStateException("nope");
    }

    Risky() {
        System.out.println("ctor body");
    }
}

try {
    new Risky();
} catch (Exception e) {
    System.out.println(e.getClass().getSimpleName() + ": " + e.getMessage());
}
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
IllegalStateException: nope
```

Инициализатор поля выполняется до тела конструктора и кидает исключение → `ctor body` не достигается. В отличие от static-инициализатора, instance-исключение НЕ оборачивается — летит как есть.

</details>

### 37. static-блок потомка читает поле родителя

java

```java
class Parent {
    static int value = 10;
    static {
        System.out.println("Parent block, value=" + value);
    }
}

class Child extends Parent {
    static {
        System.out.println("Child block, value=" + value);
    }
}

new Child();
```

**Что выведет?**

<details> <summary>Ответ</summary>

```
Parent block, value=10
Child block, value=10
```

`Parent` инициализируется полностью (поле `value=10`, затем его блок) до блока `Child`. Блок потомка читает унаследованное `value`, уже равное `10`.

</details>

