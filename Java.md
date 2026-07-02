# Часть 1: Java

## 1. Java Fundamentals

1.1. Запуск Java-программы (java → main → JVM, classpath)
1.2. Примитивные типы (int, long, short, byte, char, float, double, boolean — размеры, диапазоны, дефолтные значения)
1.3. Reference types и работа с ними (объекты в heap, ссылки в stack)
1.4. Автобоксинг и unboxing (когда происходит, Integer cache, ловушки производительности)
1.5. Операторы == vs .equals (для примитивов, для reference, для String)
1.6. Управление потоком (if, классический switch, switch expression Java 14+, циклы, break/continue с метками)
1.7. Массивы (одномерные, многомерные, covariance массивов, Arrays.asList vs List.of)
1.8. Методы (сигнатура, перегрузка, varargs, передача аргументов pass-by-value)
1.9. Область видимости (block scope, instance vs local, this)
1.10. final для локальных, параметров, полей, классов
1.11. Пакеты и базовые import (java.lang без import, статические импорты)

## 2. IntelliJ IDEA + Tooling (компактно — для перехода с Rider)

2.1. Что отличается от Rider (project structure, modules, sources/test roots)
2.2. Run Configurations и дебаггер (главное — conditional breakpoints, evaluate expression)
2.3. Code generation (getters/setters/equals/hashCode — то, чего язык сам не даёт)
2.4. Maven/Gradle integration (импорт, reload, dependencies view)
2.5. Productivity-хоткеи (отличия от Rider)

## 3. Basics of OOP

3.1. Класс и объект (declaration, instantiation, конструкторы)
3.2. Поля (instance, static, final, инициализация)
3.3. Методы (instance, static, доступ к this)
3.4. Конструкторы (default, перегрузка, this(), super())
3.5. Модификаторы доступа (private, package-private, protected, public — package-private как Java-специфичная штука)
3.6. Геттеры/сеттеры (зачем, конвенции, JavaBeans)
3.7. Иммутабельность (как сделать immutable класс, требования)
3.8. Equals и hashCode контракт (полный разбор, последствия нарушения)
3.9. toString (зачем, как переопределять)
3.10. Object class и его методы (equals, hashCode, toString, getClass, clone)
3.11. Static члены (static fields, static methods, static initializer)
3.12. Inner classes (static nested, inner, local, anonymous — где какие применяются)

## 4. Inheritance, Interfaces, Polymorphism

4.1. Наследование (extends, единичное наследование, Object как корень)
4.2. Перегрузка vs переопределение (overloading vs overriding, @Override)
4.3. Виртуальные вызовы (все методы виртуальны по умолчанию, отличие от C# с virtual/override)
4.4. super (вызов методов и конструкторов родителя)
4.5. Полиморфизм (runtime dispatch)
4.6. Abstract classes vs interfaces (когда что выбирать)
4.7. Interface (declaration, abstract methods, default methods Java 8+, static methods, private methods Java 9+)
4.8. Default methods и проблема diamond inheritance (как разрешается, отличие от C#)
4.9. Casting и instanceof (down/upcast, pattern matching for instanceof Java 16+)
4.10. Final classes и методы (когда запрещать наследование)
4.11. sealed классы и интерфейсы (отсылка к главе 17)
4.12. Композиция vs наследование

## 5. Enums

5.1. Базовый синтаксис (enum как специальный класс)
5.2. Конструктор и поля у enum (что enum может ещё хранить)
5.3. Методы у enum (values, valueOf, ordinal, name)
5.4. Abstract methods в enum (per-constant поведение)
5.5. Реализация интерфейсов через enum
5.6. EnumSet и EnumMap (зачем существуют, перформанс)
5.7. Singleton через enum (паттерн, преимущества)
5.8. Switch с enum (включая switch expression Java 14+)
5.9. Java enum vs C# enum (огромная разница — Java enum это полноценный класс)
5.10. Enum и сериализация (что происходит, ordinal vs name)

## 6. Exceptions

6.1. Иерархия Throwable (Throwable, Error, Exception, RuntimeException)
6.2. Checked vs unchecked (контракт компилятора, дизайн-рационал)
6.3. try / catch / finally (синтаксис, multi-catch, порядок catch-блоков)
6.4. finally — детали (return в finally, throw в finally, когда не выполняется)
6.5. try-with-resources и AutoCloseable (как разворачивается, suppressed exceptions)
6.6. Цепочка причин (cause, getCause, initCause)
6.7. Wrap-and-rethrow паттерн (exception translation)
6.8. Дизайн собственных исключений (extends Exception vs RuntimeException, четыре стандартных конструктора)
6.9. Stack trace (как формируется, при создании а не при throw, перформанс)
6.10. InterruptedException и контракт прерывания (введение, детали в главе 23)
6.11. Антипаттерны (пустой catch, catch Throwable, ловля Exception без уточнения)

## 7. Generics

7.1. Generic типы и generic методы (синтаксис, type parameter)
7.2. Bounded type parameters (extends, super)
7.3. Wildcards и PECS (? extends T, ? super T, когда какое применяется)
7.4. Type erasure (что происходит при компиляции, последствия в рантайме)
7.5. Невозможные операции (new T(), instanceof T, массивы T[])
7.6. Bridge methods (что генерит компилятор для совместимости)
7.7. Raw types (что это, почему не надо использовать, когда встретишь в legacy)
7.8. Unchecked warnings и @SuppressWarnings (когда оправдано, как минимизировать)
7.9. Generic constructors и type inference (диамонд оператор, инференс в Java 8+)
7.10. Generic methods отдельно от generic классов (синтаксис, типичные кейсы)
7.11. Сравнение с C# (reified vs erased, конкретные последствия)

## 8. Project Structure, Build, Modules

8.1. Структура Maven-проекта (src/main, src/test, target, pom.xml)
8.2. Maven основы (groupId/artifactId/version, dependencies, scopes)
8.3. Maven lifecycle (clean, compile, test, package, install — что когда вызывается)
8.4. Maven plugins (surefire, compiler, jar — что обычно нужно знать)
8.5. Gradle краткое введение (что отличается, build.gradle vs pom.xml)
8.6. Multi-module проекты (родительский pom, наследование)
8.7. Dependency management (transitive deps, dependency conflicts, exclusions, BOM)
8.8. JPMS — что есть, и почему в обычных проектах почти не используется

## 9. Lambdas and Functional Interfaces

9.1. Functional interfaces (SAM — single abstract method, @FunctionalInterface)
9.2. Базовые встроенные функциональные интерфейсы (Function, Consumer, Supplier, Predicate, BiFunction)
9.3. Синтаксис лямбды (полная форма, сокращения, type inference)
9.4. Method references (Class::method, instance::method, Class::new — четыре формы)
9.5. Захват переменных (effectively final, разница с C# closures)
9.6. Лямбды и checked exceptions (главная боль, sneaky throws, workarounds)
9.7. Composition (Function.andThen, Function.compose, Predicate.and/or/negate)
9.8. Как работает лямбда внутри (invokedynamic, LambdaMetafactory, отличие от анонимного класса)
9.9. Когда лямбда, а когда обычный класс (читаемость, переиспользование)

## 10. Streams API

10.1. Зачем Streams (мотивация, отличие от обычных циклов и от Collections)
10.2. Создание стрима (Collection.stream, Stream.of, Stream.generate, Arrays.stream)
10.3. Intermediate operations (filter, map, flatMap, distinct, sorted, peek, limit, skip)
10.4. Terminal operations (forEach, collect, reduce, count, anyMatch/allMatch/noneMatch, findFirst/findAny)
10.5. Ленивость стрима (короткое замыкание, что когда выполняется)
10.6. Collectors (toList, toSet, toMap, groupingBy, partitioningBy, joining, counting)
10.7. Primitive streams (IntStream, LongStream, DoubleStream — зачем, mapToInt/mapToObj)
10.8. Parallel streams (когда применять, ловушки, ForkJoinPool)
10.9. Optional и его использование со streams (findFirst, reduce, orElse, ifPresent)
10.10. Best practices и антипаттерны (избегать side effects в map, не использовать parallel везде)

## 11. JVM Under the Hood

11.1. Что такое JVM (спецификация vs реализации — HotSpot, OpenJ9, GraalVM)
11.2. Загрузка класса (ClassLoader hierarchy: bootstrap, platform, app)
11.3. Структура памяти JVM (Heap, Stack, Metaspace, Code Cache, Direct Memory)
11.4. Heap detail (Young/Old generations, Eden, Survivor spaces)
11.5. Garbage Collection основы (mark-sweep, copying, generational hypothesis)
11.6. GC алгоритмы (Serial, Parallel, G1, ZGC, Shenandoah — когда какой)
11.7. Stop-the-world паузы (что это, как минимизировать)
11.8. JIT compilation (C1, C2, tiered compilation, inline, deoptimization)
11.9. Escape analysis и scalar replacement
11.10. Полезные JVM-флаги (-Xms, -Xmx, -XX:+UseG1GC, -verbose:gc)
11.11. Memory leaks в managed languages (как возможно с GC, типичные причины)
11.12. Heap dumps и анализ (jmap, jhat, VisualVM, MAT)
11.13. Профилирование в проде (JFR — Java Flight Recorder, async-profiler — современный стандарт)

## 12. Metaprogramming: Annotations, Reflection, Classloaders

12.1. Аннотации — зачем (метаданные для рантайма и компилятора)
12.2. Стандартные аннотации (@Override, @Deprecated, @SuppressWarnings, @FunctionalInterface)
12.3. Создание собственных аннотаций (@Retention, @Target, @Inherited, элементы)
12.4. Reflection — что это и зачем (доступ к структуре класса в рантайме)
12.5. Class, Method, Field, Constructor — основные API
12.6. Доступ через рефлексию (getField vs getDeclaredField, setAccessible)
12.7. Reflection — перформанс (медленно, как ускорять — кеширование, MethodHandles)
12.8. MethodHandles и VarHandles (современная альтернатива классическому reflection)
12.9. ClassLoader — иерархия (повтор из главы 11 + детали)
12.10. Загрузка класса вручную (Class.forName, classloader.loadClass)
12.11. Dynamic proxies (Proxy.newProxyInstance, InvocationHandler)
12.12. Что используют фреймворки (как DI/AOP строится на этом — обзорно, без Spring-специфики)

## 13. Collections Framework

13.1. Иерархия (Collection, List, Set, Queue, Deque, Map — Map отдельно)
13.2. ArrayList (внутреннее устройство, рост capacity, амортизированная сложность)
13.3. LinkedList (когда применять, когда избегать)
13.4. HashMap (полное устройство: buckets, hash function, collision, treeification от 8 элементов)
13.5. equals/hashCode контракт в контексте HashMap (что ломается)
13.6. LinkedHashMap (insertion order, access order, LRU-кеш)
13.7. TreeMap и TreeSet (red-black tree, упорядоченность, Comparator)
13.8. HashSet под капотом (HashMap внутри)
13.9. ConcurrentHashMap (отсылка к главе 24)
13.10. Iterator и ConcurrentModificationException (fail-fast vs fail-safe)
13.11. Immutable collections (List.of, Set.of, Map.of, Collections.unmodifiableList — разница)
13.12. Queue и Deque (LinkedList vs ArrayDeque, PriorityQueue)
13.13. Big-O сравнительная таблица (когда какую коллекцию выбирать)

## 14. Strings

14.1. String как объект, immutability и её причины
14.2. String pool (constant pool, intern, отличия от new String)
14.3. Конкатенация строк (под капотом StringBuilder, до и после Java 9 — JEP 280)
14.4. StringBuilder vs StringBuffer (когда что — последний почти не нужен)
14.5. Сравнение строк (== vs equals vs equalsIgnoreCase)
14.6. Полезные методы String (split, join, format, strip vs trim, chars)
14.7. Text Blocks Java 15+ (multi-line strings, escape rules)
14.8. Regex (Pattern, Matcher, compile once, Pattern.compile)
14.9. Кодировки и Charset (UTF-8 как дефолт с Java 18 по JEP 400, getBytes)
14.10. Compact Strings (Java 9, latin1 vs UTF-16 представление)

## 15. Numeric Types

15.1. Целочисленные (byte, short, int, long — размеры, диапазоны)
15.2. Целочисленное переполнение (что происходит, Math.addExact для проверки)
15.3. Float и double (IEEE 754, точность, ошибки округления)
15.4. BigInteger и BigDecimal (когда нужны, перформанс)
15.5. BigDecimal для денег (главное правило: не использовать double для денег)
15.6. Битовые операции (&, |, ^, ~, <<, >>, >>>)
15.7. Числовые литералы (underscores Java 7+, hex/binary/octal)
15.8. Преобразования (implicit widening, explicit narrowing, потеря данных)
15.9. Math класс (полезные методы — pow, sqrt, abs, min, max, random)
15.10. Random vs SecureRandom vs ThreadLocalRandom

## 16. Date and Time (java.time)

16.1. Почему старый Date/Calendar — это боль (mutability, плохой API, thread-safety)
16.2. Instant — момент времени (UTC, machine time)
16.3. LocalDate, LocalTime, LocalDateTime — без таймзоны (human time)
16.4. ZonedDateTime vs OffsetDateTime — когда какое применяется
16.5. ZoneId, ZoneOffset, ZoneRules (как Java узнаёт о таймзонах, tzdata)
16.6. Duration vs Period (machine vs human разница)
16.7. Форматирование и парсинг (DateTimeFormatter, ISO стандарты)
16.8. Арифметика дат (plus, minus, with, TemporalAdjusters)
16.9. Конвертация между Date/Calendar и java.time (интероп с legacy)
16.10. Типичные ошибки (LocalDateTime для timestamp, игнор таймзон в распределённых системах)

## 17. Records, Sealed Classes, Pattern Matching

17.1. Records — мотивация (data carrier без boilerplate)
17.2. Records синтаксис (canonical constructor, compact constructor, кастомные методы)
17.3. Records — что генерится автоматически (equals, hashCode, toString, accessors)
17.4. Records ограничения (final, не наследуется, нет setters)
17.5. Records vs обычный class — когда что (deep)
17.6. Sealed classes — мотивация (ограниченная иерархия)
17.7. Sealed синтаксис (sealed, permits, non-sealed, final)
17.8. Pattern matching for instanceof (Java 16+)
17.9. Pattern matching for switch (Java 21+)
17.10. Record patterns (Java 21+)
17.11. Algebraic data types на Java (sealed + records — функциональный подход)

## 18. I/O and NIO

18.1. Streams старого I/O (InputStream/OutputStream, Reader/Writer, byte vs char)
18.2. Декораторы потоков (BufferedInputStream, DataInputStream — паттерн Decorator живьём)
18.3. java.nio.file — современный API (Path, Paths, Files)
18.4. Чтение и запись файлов (Files.readAllLines, Files.lines, Files.write)
18.5. Files.walk и обход директорий
18.6. Channels и Buffers (концептуально, ByteBuffer, direct vs heap buffer)
18.7. Blocking vs non-blocking I/O (зачем нужно для серверов)
18.8. Memory-mapped files (когда применяется)
18.9. Закрытие ресурсов (try-with-resources как стандарт)
18.10. Кодировки при чтении файлов (всегда указывать Charset явно)

## 19. Serialization

19.1. Что такое сериализация и зачем нужна
19.2. Java Serializable — как работает (readObject/writeObject, serialVersionUID)
19.3. Проблемы Serializable (security — RCE через десериализацию, versioning, перформанс)
19.4. Почему индустрия отказывается от Serializable (Effective Java — "избегайте")
19.5. JSON-сериализация — Jackson обзорно (ObjectMapper, аннотации, polymorphic types)
19.6. JSON vs binary форматы (Protobuf, Avro — на уровне "знать что есть")
19.7. Сериализация записей (Records и сериализация — отличия)
19.8. Externalizable — старая альтернатива Serializable (не использовать, но знать)

## 20. Software Engineering in Java

20.1. SOLID применительно к Java (с примерами кода)
20.2. Effective Java — ключевые items (Bloch's rules: builder, immutable, equals/hashCode, наследование, exceptions)
20.3. Composition over inheritance — паттерны delegation
20.4. Defensive copying (зачем, как, для immutability)
20.5. Контракты методов (preconditions, postconditions, invariants)
20.6. Когда Optional, когда null (детальные правила использования Optional)
20.7. Code style — Java-специфичный (Javadoc, naming, package layout)
20.8. Lombok — что это и почему индустрия использует
20.9. Lombok — плюсы и минусы (компромиссы, IDE support, debugging issues)
20.10. Best practices при работе с коллекциями (defensive copies, return immutable views)
20.11. Null safety в Java (нет нативной как в Kotlin, что использовать — @Nullable, @NonNull)

## 21. Logging

21.1. Зачем нужно логирование (что логи дают в продакшене)
21.2. Уровни логирования (TRACE, DEBUG, INFO, WARN, ERROR)
21.3. SLF4J — фасад (зачем нужен, как использовать)
21.4. Logback и Log4j2 — реализации (когда что выбирать)
21.5. Конфигурация (logback.xml — appenders, encoders, loggers)
21.6. Параметризованные сообщения (log.info("user {} did {}", user, action) — не конкатенация)
21.7. Логирование исключений (правильный способ: log.error("msg", exception) — последним аргументом)
21.8. MDC — Mapped Diagnostic Context (request-id, user-id для трассировки)
21.9. Structured logging (JSON output, что даёт)
21.10. Performance logging (lazy evaluation, log.isDebugEnabled когда нужно)
21.11. java.util.logging — почему обычно не используют

## 22. Unit Testing

22.1. JUnit 5 основы (@Test, @BeforeEach/@AfterEach, @BeforeAll/@AfterAll)
22.2. Assertions (assertEquals, assertTrue, assertThrows, assertAll, assertTimeout)
22.3. AssertJ (плавный API, читаемые ошибки, почему лучше JUnit assertions)
22.4. Параметризованные тесты (@ParameterizedTest, @ValueSource, @CsvSource, @MethodSource)
22.5. Mockito основы (mock, when/thenReturn, verify, ArgumentCaptor)
22.6. Mockito продвинутое (spy, doReturn vs thenReturn, @Mock + @InjectMocks)
22.7. Тестовая пирамида (unit vs integration vs e2e — что куда)
22.8. Структура теста (Arrange-Act-Assert / Given-When-Then)
22.9. Что НЕ надо мокать (value objects, simple builders, типичные ошибки)
22.10. Тестирование исключений (assertThrows vs ожидание)
22.11. Test doubles — терминология (mock, stub, fake, spy, dummy — разница)
22.12. Coverage (line, branch, не цель сама по себе, инструменты — JaCoCo)

## 23. Concurrency Basics (JMM, threads, synchronization)

23.1. Thread — создание (extends Thread, implements Runnable, лямбда)
23.2. Жизненный цикл потока (NEW, RUNNABLE, BLOCKED, WAITING, TIMED_WAITING, TERMINATED)
23.3. Базовые методы Thread (start, run, sleep, join, interrupt, isInterrupted)
23.4. InterruptedException и контракт прерывания (детальный разбор)
23.5. Java Memory Model (зачем нужна, что описывает)
23.6. Happens-before отношение (формальное определение, примеры)
23.7. volatile (что гарантирует, что не гарантирует, когда применяется)
23.8. synchronized методы и блоки (intrinsic lock, что блокируется)
23.9. Локи на разных уровнях (this, Class, отдельный private final Object lock)
23.10. Race conditions и data races (разница)
23.11. Deadlock, livelock, starvation (определения, причины, диагностика через thread dump)
23.12. wait/notify/notifyAll (классический механизм координации, ловушки)
23.13. ThreadLocal (зачем, как, ловушка с memory leak в thread pool)

## 24. Concurrent API (Executors, CompletableFuture, virtual threads, atomic)

24.1. ExecutorService — мотивация (зачем не создавать Thread напрямую)
24.2. Создание пулов (newFixedThreadPool, newCachedThreadPool, newSingleThreadExecutor — и почему обычно НЕ они)
24.3. ThreadPoolExecutor напрямую (полная конфигурация, queue, RejectedExecutionHandler)
24.4. Future и FutureTask (базовая абстракция, get блокирует)
24.5. CompletableFuture основы (supplyAsync, thenApply, thenAccept, thenCompose)
24.6. CompletableFuture — комбинирование (allOf, anyOf, exceptionally, handle)
24.7. Atomic classes (AtomicInteger, AtomicLong, AtomicReference — CAS под капотом)
24.8. Lock API (ReentrantLock, ReadWriteLock — отличия от synchronized)
24.9. Condition — замена wait/notify
24.10. ConcurrentHashMap внутри (отличие от Hashtable и от synchronized Map)
24.11. CopyOnWriteArrayList и когда применяется
24.12. BlockingQueue и producer-consumer (ArrayBlockingQueue, LinkedBlockingQueue)
24.13. Virtual threads Java 21+ (мотивация, отличие от platform threads, когда применять)
24.14. Structured concurrency Java 21+ preview (упомянуть, к чему идёт язык)
24.15. Fork/Join framework (когда применяется, parallel streams под капотом)

## 25. Design Patterns in Java (★ — приоритетные для собесов)

25.1. Категории паттернов (Creational, Structural, Behavioral — кратко)
25.2. ★ Singleton — все варианты (eager, lazy, double-checked locking, enum-based, Holder pattern)
25.3. ★ Factory Method и Abstract Factory
25.4. ★ Builder (с примером на сложном объекте, fluent API)
25.5. Prototype (clone и его проблемы) — обзорно
25.6. ★ Adapter (когда применяется, пример из JDK — Arrays.asList)
25.7. ★ Decorator (пример из JDK — InputStream/BufferedInputStream)
25.8. Facade (упрощение сложных подсистем)
25.9. Proxy (включая dynamic proxy из главы 12)
25.10. ★ Strategy (через интерфейс или лямбду в современной Java)
25.11. ★ Observer (на чём строится event-driven, отношение к Listener)
25.12. ★ Template Method (классический случай для abstract class)
25.13. Iterator (паттерн встроен в язык — Iterable/Iterator)
25.14. Chain of Responsibility — обзорно
25.15. Command (Runnable как простейший случай) — обзорно
25.16. Какие паттерны устарели или встроены в язык (Visitor частично заменяется sealed + pattern matching)

## 26. Java Evolution

26.1. Краткая хронология LTS (8, 11, 17, 21, 25)
26.2. Java 8 — переломный (lambdas, streams, Optional, default methods, java.time)
26.3. Java 9 — модули, jshell, factory методы коллекций (List.of, Map.of)
26.4. Java 10 — var
26.5. Java 11 LTS — HTTP client, String методы, removed JEE modules
26.6. Java 12-16 — switch expression, records (preview), sealed (preview), pattern matching (preview)
26.7. Java 17 LTS — sealed classes final, records final, pattern matching for instanceof
26.8. Java 21 LTS — virtual threads, pattern matching for switch, record patterns, sequenced collections
26.9. Java 22-25 — что добавилось/добавляется (Foreign Function API, scoped values, statement-before-super)
26.10. Что обычно используется в production в 2026 (11 и 17 — массово; 21 — growing)
26.11. Текущие preview и incubator (что находится "в дороге", чтобы понимать траекторию)
