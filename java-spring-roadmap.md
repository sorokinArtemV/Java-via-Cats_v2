# Java + Spring Roadmap для перехода с .NET на mid+ позиции (EU/US)

## Контекст и подход

**Бэкграунд:** ~1 год коммерческого опыта в .NET/ASP.NET + React. Уверенное владение бэкенд-концепциями (DI, middleware, ORM/EF Core, миграции, auth/JWT, валидация, тесты, observability). Основы Java на базовом уровне.

**Цель:** проходить mid / mid+ собесы на Java-позиции в EU/US.

**Реалистичный срок:** 4-5 месяцев активного обучения при 2-3 часах в день. Большая часть концепций уже знакома по .NET — учится терминология, идиомы и нюансы реализации в JVM/Spring экосистеме.

**Параллельный подход — три трека одновременно:**

1. **Java-теория** — по карте ниже, чередуя главы
2. **Project 1 (чистая Java, без Spring)** — для набивания языка
3. **Project 2 (Spring Boot)** — портфельный проект для собесов

**Распределение времени (2-3 часа в день):**

- 30-40 мин — теория (главы из карт)
- 30-40 мин — Project 1 или упражнения по только что прочитанной теме
- 60-90 мин — Project 2 (главная инвестиция)

**Правило про AI:**

- В Project 1 — AI запрещён для написания кода (можно для объяснений концепций). Это антидот к over-reliance.
- В Project 2 — AI разрешён для конфигурации и аннотаций; логику сервисов и тесты писать руками.

---

## Идеи для Project 1 (чистая Java)

Выбрать одно или сделать несколько мелких:

- **Парсер логов с агрегацией** — закроет I/O, streams, collectors, java.time, regex
- **Свой thread pool с планировщиком задач** — закроет concurrency, ExecutorService, CompletableFuture
- **CLI для todo/заметок в JSON-файле** — закроет Jackson, file I/O, command parsing
- **Реализация структуры данных с нуля** (свой HashMap, LRU-cache) — закроет глубокое понимание коллекций, generics, equals/hashCode

## Идеи для Project 2 (Spring Boot)

Что-то с реальной доменной логикой, не todo-приложение:

- Система бронирования (комнаты, билеты, слоты) — с конфликтами и транзакциями
- Лёгкий маркетплейс / каталог с заказами
- Issue tracker / kanban с пользователями, ролями, фильтрами
- API для трекинга задач/привычек с уведомлениями и фоновыми задачами

**Обязательные компоненты в проекте:**
JWT auth, JPA с парой связанных entities (для встречи с N+1), валидация, exception handling, миграции (Flyway), интеграционные тесты с Testcontainers, Actuator.

## Порядок развития Project 2 (по слоям)

Не пытаться собрать всё сразу. Каждый слой = пара глав теории:

1. Spring Boot Hello World + один контроллер → главы Spring 1-5
2. Добавить JPA + одну entity + Flyway → Spring 16-18, 21, 24
3. Связанные entities + разобрать N+1 → Spring 19-20, 23
4. Транзакции, валидация, exception handling → Spring 11, 22
5. Spring Security + JWT → Spring 13-15
6. Тесты (MockMvc + Testcontainers) → Spring 27
7. Actuator + structured logging → Spring 28
8. Async, scheduled tasks, кеш → Spring 25-26

---

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
4.11. sealed классы и интерфейсы (отсылка к главе 16)
4.12. Композиция vs наследование

## 5. Exceptions

5.1. Иерархия Throwable (Throwable, Error, Exception, RuntimeException)
5.2. Checked vs unchecked (контракт компилятора, дизайн-рационал)
5.3. try / catch / finally (синтаксис, multi-catch, порядок catch-блоков)
5.4. finally — детали (return в finally, throw в finally, когда не выполняется)
5.5. try-with-resources и AutoCloseable (как разворачивается, suppressed exceptions)
5.6. Цепочка причин (cause, getCause, initCause)
5.7. Wrap-and-rethrow паттерн (exception translation)
5.8. Дизайн собственных исключений (extends Exception vs RuntimeException, четыре стандартных конструктора)
5.9. Stack trace (как формируется, при создании а не при throw, перформанс)
5.10. InterruptedException и контракт прерывания (введение, детали в главе 23)
5.11. Антипаттерны (пустой catch, catch Throwable, ловля Exception без уточнения)

## 6. Generics

6.1. Generic типы и generic методы (синтаксис, type parameter)
6.2. Bounded type parameters (extends, super)
6.3. Wildcards и PECS (? extends T, ? super T, когда какое применяется)
6.4. Type erasure (что происходит при компиляции, последствия в рантайме)
6.5. Невозможные операции (new T(), instanceof T, массивы T[])
6.6. Bridge methods (что генерит компилятор для совместимости)
6.7. Raw types (что это, почему не надо использовать, когда встретишь в legacy)
6.8. Unchecked warnings и @SuppressWarnings (когда оправдано, как минимизировать)
6.9. Generic constructors и type inference (диамонд оператор, инференс в Java 8+)
6.10. Generic methods отдельно от generic классов (синтаксис, типичные кейсы)
6.11. Сравнение с C# (reified vs erased, конкретные последствия)

## 7. Streams API

7.1. Зачем Streams (мотивация, отличие от обычных циклов и от Collections)
7.2. Создание стрима (Collection.stream, Stream.of, Stream.generate, Arrays.stream)
7.3. Intermediate operations (filter, map, flatMap, distinct, sorted, peek, limit, skip)
7.4. Terminal operations (forEach, collect, reduce, count, anyMatch/allMatch/noneMatch, findFirst/findAny)
7.5. Ленивость стрима (короткое замыкание, что когда выполняется)
7.6. Collectors (toList, toSet, toMap, groupingBy, partitioningBy, joining, counting)
7.7. Primitive streams (IntStream, LongStream, DoubleStream — зачем, mapToInt/mapToObj)
7.8. Parallel streams (когда применять, ловушки, ForkJoinPool)
7.9. Optional и его использование со streams (findFirst, reduce, orElse, ifPresent)
7.10. Best practices и антипаттерны (избегать side effects в map, не использовать parallel везде)

## 8. Lambdas and Functional Interfaces

8.1. Functional interfaces (SAM — single abstract method, @FunctionalInterface)
8.2. Базовые встроенные функциональные интерфейсы (Function, Consumer, Supplier, Predicate, BiFunction)
8.3. Синтаксис лямбды (полная форма, сокращения, type inference)
8.4. Method references (Class::method, instance::method, Class::new — четыре формы)
8.5. Захват переменных (effectively final, разница с C# closures)
8.6. Лямбды и checked exceptions (главная боль, sneaky throws, workarounds)
8.7. Composition (Function.andThen, Function.compose, Predicate.and/or/negate)
8.8. Как работает лямбда внутри (invokedynamic, LambdaMetafactory, отличие от анонимного класса)
8.9. Когда лямбда, а когда обычный класс (читаемость, переиспользование)

## 9. JVM Under the Hood

9.1. Что такое JVM (спецификация vs реализации — HotSpot, OpenJ9, GraalVM)
9.2. Загрузка класса (ClassLoader hierarchy: bootstrap, platform, app)
9.3. Структура памяти JVM (Heap, Stack, Metaspace, Code Cache, Direct Memory)
9.4. Heap detail (Young/Old generations, Eden, Survivor spaces)
9.5. Garbage Collection основы (mark-sweep, copying, generational hypothesis)
9.6. GC алгоритмы (Serial, Parallel, G1, ZGC, Shenandoah — когда какой)
9.7. Stop-the-world паузы (что это, как минимизировать)
9.8. JIT compilation (C1, C2, tiered compilation, inline, deoptimization)
9.9. Escape analysis и scalar replacement
9.10. Полезные JVM-флаги (-Xms, -Xmx, -XX:+UseG1GC, -verbose:gc)
9.11. Memory leaks в managed languages (как возможно с GC, типичные причины)
9.12. Heap dumps и анализ (jmap, jhat, VisualVM, MAT)
9.13. Профилирование в проде (JFR — Java Flight Recorder, async-profiler — современный стандарт)

## 10. Collections Framework

10.1. Иерархия (Collection, List, Set, Queue, Deque, Map — Map отдельно)
10.2. ArrayList (внутреннее устройство, рост capacity, амортизированная сложность)
10.3. LinkedList (когда применять, когда избегать)
10.4. HashMap (полное устройство: buckets, hash function, collision, treeification от 8 элементов)
10.5. equals/hashCode контракт в контексте HashMap (что ломается)
10.6. LinkedHashMap (insertion order, access order, LRU-кеш)
10.7. TreeMap и TreeSet (red-black tree, упорядоченность, Comparator)
10.8. HashSet под капотом (HashMap внутри)
10.9. ConcurrentHashMap (отсылка к главе 24)
10.10. Iterator и ConcurrentModificationException (fail-fast vs fail-safe)
10.11. Immutable collections (List.of, Set.of, Map.of, Collections.unmodifiableList — разница)
10.12. Queue и Deque (LinkedList vs ArrayDeque, PriorityQueue)
10.13. Big-O сравнительная таблица (когда какую коллекцию выбирать)

## 11. Project Structure, Build, Modules

11.1. Структура Maven-проекта (src/main, src/test, target, pom.xml)
11.2. Maven основы (groupId/artifactId/version, dependencies, scopes)
11.3. Maven lifecycle (clean, compile, test, package, install — что когда вызывается)
11.4. Maven plugins (surefire, compiler, jar — что обычно нужно знать)
11.5. Gradle краткое введение (что отличается, build.gradle vs pom.xml)
11.6. Multi-module проекты (родительский pom, наследование)
11.7. Dependency management (transitive deps, dependency conflicts, exclusions, BOM)
11.8. JPMS — что есть, и почему в обычных проектах почти не используется

## 12. Strings

12.1. String как объект, immutability и её причины
12.2. String pool (constant pool, intern, отличия от new String)
12.3. Конкатенация строк (под капотом StringBuilder, до и после Java 9 — JEP 280)
12.4. StringBuilder vs StringBuffer (когда что — последний почти не нужен)
12.5. Сравнение строк (== vs equals vs equalsIgnoreCase)
12.6. Полезные методы String (split, join, format, strip vs trim, chars)
12.7. Text Blocks Java 15+ (multi-line strings, escape rules)
12.8. Regex (Pattern, Matcher, compile once, Pattern.compile)
12.9. Кодировки и Charset (UTF-8 как дефолт с Java 18 по JEP 400, getBytes)
12.10. Compact Strings (Java 9, latin1 vs UTF-16 представление)

## 13. Numeric Types

13.1. Целочисленные (byte, short, int, long — размеры, диапазоны)
13.2. Целочисленное переполнение (что происходит, Math.addExact для проверки)
13.3. Float и double (IEEE 754, точность, ошибки округления)
13.4. BigInteger и BigDecimal (когда нужны, перформанс)
13.5. BigDecimal для денег (главное правило: не использовать double для денег)
13.6. Битовые операции (&, |, ^, ~, <<, >>, >>>)
13.7. Числовые литералы (underscores Java 7+, hex/binary/octal)
13.8. Преобразования (implicit widening, explicit narrowing, потеря данных)
13.9. Math класс (полезные методы — pow, sqrt, abs, min, max, random)
13.10. Random vs SecureRandom vs ThreadLocalRandom

## 14. Date and Time (java.time)

14.1. Почему старый Date/Calendar — это боль (mutability, плохой API, thread-safety)
14.2. Instant — момент времени (UTC, machine time)
14.3. LocalDate, LocalTime, LocalDateTime — без таймзоны (human time)
14.4. ZonedDateTime vs OffsetDateTime — когда какое применяется
14.5. ZoneId, ZoneOffset, ZoneRules (как Java узнаёт о таймзонах, tzdata)
14.6. Duration vs Period (machine vs human разница)
14.7. Форматирование и парсинг (DateTimeFormatter, ISO стандарты)
14.8. Арифметика дат (plus, minus, with, TemporalAdjusters)
14.9. Конвертация между Date/Calendar и java.time (интероп с legacy)
14.10. Типичные ошибки (LocalDateTime для timestamp, игнор таймзон в распределённых системах)

## 15. Enums

15.1. Базовый синтаксис (enum как специальный класс)
15.2. Конструктор и поля у enum (что enum может ещё хранить)
15.3. Методы у enum (values, valueOf, ordinal, name)
15.4. Abstract methods в enum (per-constant поведение)
15.5. Реализация интерфейсов через enum
15.6. EnumSet и EnumMap (зачем существуют, перформанс)
15.7. Singleton через enum (паттерн, преимущества)
15.8. Switch с enum (включая switch expression Java 14+)
15.9. Java enum vs C# enum (огромная разница — Java enum это полноценный класс)
15.10. Enum и сериализация (что происходит, ordinal vs name)

## 16. Records, Sealed Classes, Pattern Matching

16.1. Records — мотивация (data carrier без boilerplate)
16.2. Records синтаксис (canonical constructor, compact constructor, кастомные методы)
16.3. Records — что генерится автоматически (equals, hashCode, toString, accessors)
16.4. Records ограничения (final, не наследуется, нет setters)
16.5. Records vs обычный class — когда что (deep)
16.6. Sealed classes — мотивация (ограниченная иерархия)
16.7. Sealed синтаксис (sealed, permits, non-sealed, final)
16.8. Pattern matching for instanceof (Java 16+)
16.9. Pattern matching for switch (Java 21+)
16.10. Record patterns (Java 21+)
16.11. Algebraic data types на Java (sealed + records — функциональный подход)

## 17. I/O and NIO

17.1. Streams старого I/O (InputStream/OutputStream, Reader/Writer, byte vs char)
17.2. Декораторы потоков (BufferedInputStream, DataInputStream — паттерн Decorator живьём)
17.3. java.nio.file — современный API (Path, Paths, Files)
17.4. Чтение и запись файлов (Files.readAllLines, Files.lines, Files.write)
17.5. Files.walk и обход директорий
17.6. Channels и Buffers (концептуально, ByteBuffer, direct vs heap buffer)
17.7. Blocking vs non-blocking I/O (зачем нужно для серверов)
17.8. Memory-mapped files (когда применяется)
17.9. Закрытие ресурсов (try-with-resources как стандарт)
17.10. Кодировки при чтении файлов (всегда указывать Charset явно)

## 18. Serialization

18.1. Что такое сериализация и зачем нужна
18.2. Java Serializable — как работает (readObject/writeObject, serialVersionUID)
18.3. Проблемы Serializable (security — RCE через десериализацию, versioning, перформанс)
18.4. Почему индустрия отказывается от Serializable (Effective Java — "избегайте")
18.5. JSON-сериализация — Jackson обзорно (ObjectMapper, аннотации, polymorphic types)
18.6. JSON vs binary форматы (Protobuf, Avro — на уровне "знать что есть")
18.7. Сериализация записей (Records и сериализация — отличия)
18.8. Externalizable — старая альтернатива Serializable (не использовать, но знать)

## 19. Software Engineering in Java

19.1. SOLID применительно к Java (с примерами кода)
19.2. Effective Java — ключевые items (Bloch's rules: builder, immutable, equals/hashCode, наследование, exceptions)
19.3. Composition over inheritance — паттерны delegation
19.4. Defensive copying (зачем, как, для immutability)
19.5. Контракты методов (preconditions, postconditions, invariants)
19.6. Когда Optional, когда null (детальные правила использования Optional)
19.7. Code style — Java-специфичный (Javadoc, naming, package layout)
19.8. Lombok — что это и почему индустрия использует
19.9. Lombok — плюсы и минусы (компромиссы, IDE support, debugging issues)
19.10. Best practices при работе с коллекциями (defensive copies, return immutable views)
19.11. Null safety в Java (нет нативной как в Kotlin, что использовать — @Nullable, @NonNull)

## 20. Logging

20.1. Зачем нужно логирование (что логи дают в продакшене)
20.2. Уровни логирования (TRACE, DEBUG, INFO, WARN, ERROR)
20.3. SLF4J — фасад (зачем нужен, как использовать)
20.4. Logback и Log4j2 — реализации (когда что выбирать)
20.5. Конфигурация (logback.xml — appenders, encoders, loggers)
20.6. Параметризованные сообщения (log.info("user {} did {}", user, action) — не конкатенация)
20.7. Логирование исключений (правильный способ: log.error("msg", exception) — последним аргументом)
20.8. MDC — Mapped Diagnostic Context (request-id, user-id для трассировки)
20.9. Structured logging (JSON output, что даёт)
20.10. Performance logging (lazy evaluation, log.isDebugEnabled когда нужно)
20.11. java.util.logging — почему обычно не используют

## 21. Unit Testing

21.1. JUnit 5 основы (@Test, @BeforeEach/@AfterEach, @BeforeAll/@AfterAll)
21.2. Assertions (assertEquals, assertTrue, assertThrows, assertAll, assertTimeout)
21.3. AssertJ (плавный API, читаемые ошибки, почему лучше JUnit assertions)
21.4. Параметризованные тесты (@ParameterizedTest, @ValueSource, @CsvSource, @MethodSource)
21.5. Mockito основы (mock, when/thenReturn, verify, ArgumentCaptor)
21.6. Mockito продвинутое (spy, doReturn vs thenReturn, @Mock + @InjectMocks)
21.7. Тестовая пирамида (unit vs integration vs e2e — что куда)
21.8. Структура теста (Arrange-Act-Assert / Given-When-Then)
21.9. Что НЕ надо мокать (value objects, simple builders, типичные ошибки)
21.10. Тестирование исключений (assertThrows vs ожидание)
21.11. Test doubles — терминология (mock, stub, fake, spy, dummy — разница)
21.12. Coverage (line, branch, не цель сама по себе, инструменты — JaCoCo)

## 22. Metaprogramming: Annotations, Reflection, Classloaders

22.1. Аннотации — зачем (метаданные для рантайма и компилятора)
22.2. Стандартные аннотации (@Override, @Deprecated, @SuppressWarnings, @FunctionalInterface)
22.3. Создание собственных аннотаций (@Retention, @Target, @Inherited, элементы)
22.4. Reflection — что это и зачем (доступ к структуре класса в рантайме)
22.5. Class, Method, Field, Constructor — основные API
22.6. Доступ через рефлексию (getField vs getDeclaredField, setAccessible)
22.7. Reflection — перформанс (медленно, как ускорять — кеширование, MethodHandles)
22.8. MethodHandles и VarHandles (современная альтернатива классическому reflection)
22.9. ClassLoader — иерархия (повтор из главы 9 + детали)
22.10. Загрузка класса вручную (Class.forName, classloader.loadClass)
22.11. Dynamic proxies (Proxy.newProxyInstance, InvocationHandler)
22.12. Что используют фреймворки (как DI/AOP строится на этом — обзорно, без Spring-специфики)

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
25.9. Proxy (включая dynamic proxy из главы 22)
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

---

# Часть 2: Spring и Hibernate

## 1. Spring Ecosystem Overview

1.1. Что такое Spring Framework vs Spring Boot vs Spring Cloud (разделение слоёв)
1.2. Spring Modules (Core, Beans, Context, AOP, JDBC, ORM, Web, Test — что для чего)
1.3. История Spring (от XML к Java Config к Boot — почему именно так эволюционировало)
1.4. Spring vs Jakarta EE (раньше J2EE/JEE) — что Spring заменил и что нет
1.5. Текущие версии (Spring Framework 6, Spring Boot 3, Java 17+ baseline, Jakarta namespace)
1.6. Spring и .NET аналогии (DI как в ASP.NET, AOP как фильтры/middleware, и где аналогий нет)

## 2. Spring Core: IoC and Dependency Injection

2.1. IoC и DI — определения, разница (IoC шире, DI один из способов)
2.2. ApplicationContext — главный объект, что это такое
2.3. BeanFactory vs ApplicationContext (разница, когда что используется)
2.4. Bean — определение (managed object, не каждый объект — bean)
2.5. Способы объявления бинов (@Component, @Service, @Repository, @Controller — стереотипы)
2.6. @Configuration и @Bean — программное объявление бинов
2.7. XML config — упомянуть, что было раньше (legacy)
2.8. Component scanning (@ComponentScan, basePackages, как Spring находит бины)
2.9. Constructor injection vs setter injection vs field injection (что использовать и почему)
2.10. @Autowired — как работает, byType vs byName
2.11. @Qualifier и @Primary (разрешение конфликтов при нескольких бинах одного типа)
2.12. @Value — инъекция значений из properties, SpEL выражения
2.13. Циклические зависимости — что происходит, как чинить (constructor vs setter различие)
2.14. Optional dependencies (@Autowired(required=false), Optional<Bean>)
2.15. Инъекция коллекций (List<MyInterface> — все бины этого типа)

## 3. Bean Lifecycle and Scopes

3.1. Полный lifecycle бина (instantiation → populate → aware → init → ready → destroy)
3.2. @PostConstruct и @PreDestroy
3.3. InitializingBean и DisposableBean (legacy, но встречается)
3.4. initMethod и destroyMethod в @Bean
3.5. Aware-интерфейсы (BeanNameAware, ApplicationContextAware и т.д. — обзорно)
3.6. BeanPostProcessor — точка расширения, на чём построены аннотации
3.7. BeanFactoryPostProcessor — отличие от BeanPostProcessor
3.8. Singleton scope (дефолт, один экземпляр на context)
3.9. Prototype scope (новый экземпляр на каждый запрос — ловушки)
3.10. Web scopes (request, session, application)
3.11. Проблема инъекции prototype в singleton (ScopedProxyMode, ObjectFactory, Provider)
3.12. Lazy initialization (@Lazy, последствия)
3.13. Conditional beans (@ConditionalOnProperty, @ConditionalOnClass, @ConditionalOnMissingBean)
3.14. Profiles (@Profile, активация через spring.profiles.active)

## 4. Spring AOP

4.1. Что такое AOP и зачем (cross-cutting concerns — logging, transactions, security)
4.2. Терминология (aspect, advice, pointcut, join point, target, proxy)
4.3. Типы advice (@Before, @After, @AfterReturning, @AfterThrowing, @Around)
4.4. Pointcut expressions (execution, within, @annotation, args, this/target)
4.5. @Aspect и @EnableAspectJAutoProxy
4.6. Как работают Spring proxies (JDK dynamic proxy vs CGLIB)
4.7. Когда JDK proxy, когда CGLIB (есть интерфейс или нет)
4.8. Self-invocation problem (вызов this.method() не проходит через proxy — главная ловушка)
4.9. Что нельзя проксировать (private, final, static методы)
4.10. AOP vs AspectJ (Spring AOP — proxy-based, AspectJ — byte-code weaving, обзорно)
4.11. Реальные применения AOP в Spring (@Transactional, @Async, @Cacheable, Security — всё на AOP)

## 5. Spring Boot Fundamentals

5.1. Что Boot добавляет к Spring Framework (autoconfiguration, starters, embedded server, opinionated defaults)
5.2. @SpringBootApplication — что внутри (@Configuration + @EnableAutoConfiguration + @ComponentScan)
5.3. Starter dependencies (spring-boot-starter-*, как они устроены)
5.4. Autoconfiguration — как работает (META-INF/spring/.imports, conditional beans)
5.5. Как посмотреть, что Spring Boot настроил (--debug, ConditionEvaluationReport)
5.6. Переопределение autoconfiguration (свой бин = autoconfig отступает)
5.7. Исключение autoconfiguration (@SpringBootApplication(exclude = ...))
5.8. Embedded server (Tomcat по умолчанию, как заменить на Jetty/Undertow)
5.9. SpringApplication.run и что происходит при старте
5.10. ApplicationRunner и CommandLineRunner — выполнение кода после старта
5.11. Spring Boot vs классический Spring — что под капотом одинаковое

## 6. Configuration and Profiles

6.1. application.properties vs application.yml
6.2. Иерархия источников конфигурации (command line > env > application.properties > defaults)
6.3. Полный список property sources и их приоритет
6.4. @ConfigurationProperties — типобезопасная конфигурация (vs @Value)
6.5. Валидация @ConfigurationProperties (@Validated, JSR-303 аннотации)
6.6. Relaxed binding (kebab-case в yml = camelCase в Java)
6.7. Profiles — активация (spring.profiles.active, активный/дефолтный)
6.8. application-{profile}.properties — переопределение по профилю
6.9. @Profile на бинах
6.10. Environment object — программный доступ к конфигу
6.11. Externalized configuration (env variables, JNDI, command line args)
6.12. Secrets management (Spring Cloud Config, Vault — обзорно)
6.13. PropertySource и @PropertySource — кастомные источники

## 7. Servlet API and HTTP Foundations

7.1. Что такое Servlet и Servlet Container (Tomcat, Jetty, Undertow)
7.2. HttpServlet, doGet/doPost — как выглядит "голый" servlet
7.3. HttpServletRequest и HttpServletResponse — что внутри (headers, body, params, attributes)
7.4. ServletContext — application-scope state
7.5. Request lifecycle на уровне контейнера (accept → thread → response)
7.6. Thread-per-request модель и её последствия (ограниченный thread pool Tomcat)
7.7. Sessions (HttpSession, как хранится, sticky vs distributed sessions)
7.8. Cookies на уровне Servlet API
7.9. Async Servlet API 3.0+ (AsyncContext, основа для async controllers)
7.10. web.xml vs Java Config vs Boot autoconfig (историческая эволюция, почему сейчас не пишут web.xml)

## 8. Servlet Filters

8.1. Filter interface — doFilter, init, destroy
8.2. FilterChain — как фильтры выстраиваются
8.3. Порядок фильтров (@Order, FilterRegistrationBean)
8.4. Где живут фильтры — до DispatcherServlet, не знают про Spring controllers
8.5. Регистрация фильтра в Spring Boot (@Component, FilterRegistrationBean, @WebFilter)
8.6. Типичные кейсы (logging, CORS, request ID/MDC, encoding, GZIP, rate limiting)
8.7. OncePerRequestFilter — Spring-обёртка и зачем
8.8. Модификация request/response (HttpServletRequestWrapper, ContentCachingRequestWrapper)
8.9. Чтение body в фильтре — главная ловушка (одноразовый поток)
8.10. Filter vs Interceptor — правило выбора

## 9. Spring MVC: DispatcherServlet and Request Lifecycle

9.1. DispatcherServlet — front controller pattern
9.2. Полная цепочка обработки запроса (пошаговая схема — must know для собеса)
9.3. HandlerMapping (RequestMappingHandlerMapping — как находится метод)
9.4. HandlerAdapter — как вызывается handler
9.5. HandlerMethodArgumentResolver — биндинг параметров
9.6. HandlerMethodReturnValueHandler — обработка возврата
9.7. HttpMessageConverter — JSON/XML сериализация (Jackson по умолчанию)
9.8. ViewResolver (для случаев с View, в REST не используется)
9.9. Конфигурация DispatcherServlet (path, multiple dispatchers)
9.10. Customization через WebMvcConfigurer

## 10. Spring MVC: Building REST Controllers

10.1. @Controller vs @RestController (когда что)
10.2. @RequestMapping и shortcut'ы (@GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping)
10.3. Path variables — @PathVariable, regex в path
10.4. Query parameters — @RequestParam (required, defaultValue)
10.5. Request body — @RequestBody (десериализация JSON)
10.6. Response body — @ResponseBody (или @RestController подразумевает)
10.7. ResponseEntity — полный контроль над response (status, headers, body)
10.8. @ResponseStatus — установка HTTP статуса
10.9. Headers — @RequestHeader, как читать
10.10. @CookieValue — чтение cookies
10.11. Content negotiation (Accept header, produces/consumes)
10.12. File upload (MultipartFile, конфигурация multipart)
10.13. Stream/download response (ResponseEntity<Resource>, StreamingResponseBody)
10.14. CORS configuration (@CrossOrigin, глобально через WebMvcConfigurer)
10.15. URI builder — построение ссылок (UriComponentsBuilder)

## 11. Validation and Error Handling

11.1. Bean Validation (JSR-380, Hibernate Validator под капотом)
11.2. Стандартные аннотации (@NotNull, @NotBlank, @Size, @Min, @Max, @Email, @Pattern)
11.3. @Valid на параметрах контроллера
11.4. BindingResult — программная работа с ошибками валидации
11.5. Validation groups (для разных сценариев валидации одного объекта)
11.6. Кастомные валидаторы (ConstraintValidator)
11.7. @Validated на классе и method-level validation
11.8. MethodArgumentNotValidException — что бросается при @Valid
11.9. @ControllerAdvice — глобальная обработка
11.10. @ExceptionHandler — на уровне контроллера и глобально
11.11. ResponseEntityExceptionHandler — базовый класс с обработкой стандартных Spring исключений
11.12. ProblemDetail и RFC 7807 (Spring 6+)
11.13. Порядок выбора ExceptionHandler (specificity, @Order)
11.14. Логирование и трассировка ошибок (что показывать клиенту, что в логи)

## 12. Spring MVC: Interceptors and Cross-Cutting

12.1. HandlerInterceptor interface (preHandle, postHandle, afterCompletion)
12.2. Отличие от Servlet Filter (знает про handler, внутри DispatcherServlet)
12.3. Регистрация через WebMvcConfigurer.addInterceptors
12.4. preHandle с return false — прерывание
12.5. Порядок выполнения interceptor'ов (mirror order)
12.6. Доступ к HandlerMethod (получение аннотаций контроллера)
12.7. Pattern matching (addPathPatterns, excludePathPatterns)
12.8. Типичные кейсы (auth, audit, timing)
12.9. @InitBinder — кастомизация биндинга
12.10. @ModelAttribute глобально через ControllerAdvice

## 13. Spring Security: Core Concepts

13.1. Архитектура — где Security живёт (Servlet Filter, DelegatingFilterProxy → FilterChainProxy)
13.2. SecurityFilterChain — цепочка из 15+ фильтров
13.3. Ключевые фильтры (SecurityContextPersistenceFilter, UsernamePasswordAuthenticationFilter, ExceptionTranslationFilter, AuthorizationFilter)
13.4. SecurityContext и SecurityContextHolder
13.5. Authentication и Authorization — разница
13.6. Authentication object — что внутри (principal, credentials, authorities)
13.7. AuthenticationManager и AuthenticationProvider
13.8. UserDetails и UserDetailsService
13.9. PasswordEncoder — обязательно использовать (BCrypt стандарт)
13.10. GrantedAuthority и roles vs authorities
13.11. Конфигурация Spring Security (SecurityFilterChain bean — Spring Security 6+)
13.12. HttpSecurity DSL (authorizeHttpRequests, formLogin, httpBasic, etc.)

## 14. Spring Security: Authentication Mechanisms

14.1. Form-based authentication (login page, session)
14.2. HTTP Basic auth (когда применяется, для API)
14.3. JWT — что это, структура (header.payload.signature)
14.4. JWT в Spring Security (как валидировать, фильтр)
14.5. Stateless authentication (SessionCreationPolicy.STATELESS)
14.6. OAuth2 — концептуально (authorization code flow, client credentials, refresh token)
14.7. Spring Security OAuth2 Resource Server (валидация JWT от Keycloak/Auth0)
14.8. Spring Security OAuth2 Client (логин через Google/GitHub)
14.9. Custom AuthenticationFilter (добавление своего в chain)
14.10. Remember-me authentication
14.11. CSRF — что это, когда отключать (для stateless API)

## 15. Spring Security: Authorization

15.1. URL-based авторизация (authorizeHttpRequests, antMatchers/requestMatchers)
15.2. Method security (@PreAuthorize, @PostAuthorize, @Secured)
15.3. @EnableMethodSecurity
15.4. SpEL в @PreAuthorize (hasRole, hasAuthority, #param)
15.5. @PostFilter и @PreFilter (фильтрация коллекций)
15.6. Role hierarchy (RoleHierarchy bean)
15.7. ACL (Access Control Lists) — обзорно, для сложных кейсов
15.8. AccessDeniedHandler и AuthenticationEntryPoint
15.9. Кастомные PermissionEvaluator

## 16. Spring Data and Persistence: JDBC Foundations

16.1. JDBC API — Connection, Statement, PreparedStatement, ResultSet (концептуально)
16.2. Connection pool (HikariCP — дефолт в Spring Boot, что настраивать)
16.3. Spring JdbcTemplate — упрощение JDBC (когда используется без JPA)
16.4. NamedParameterJdbcTemplate
16.5. SQL injection и почему PreparedStatement обязателен
16.6. DataSource — конфигурация в Spring Boot
16.7. Spring Data JDBC (легковесная альтернатива JPA — обзорно)

## 17. JPA and Hibernate: Core Concepts

17.1. JPA — спецификация vs Hibernate — реализация (как Servlet vs Tomcat)
17.2. ORM — что это и зачем (impedance mismatch между ООП и реляционкой)
17.3. EntityManager — главный объект JPA
17.4. Persistence Context — что это, как работает (first-level cache)
17.5. Жизненный цикл entity (transient, managed, detached, removed)
17.6. SessionFactory и Session (Hibernate-specific, обычно скрыты под JPA)
17.7. Конфигурация в Spring Boot (spring.jpa.*, автоматическая настройка)
17.8. show-sql, format-sql, hibernate.dialect

## 18. JPA: Entity Mapping

18.1. @Entity, @Table — базовое маппинг
18.2. @Id, @GeneratedValue (стратегии: IDENTITY, SEQUENCE, AUTO, TABLE — что когда)
18.3. @Column — детальная настройка колонки
18.4. Простые типы (что Hibernate понимает из коробки)
18.5. Enum mapping (@Enumerated — STRING vs ORDINAL, почему всегда STRING)
18.6. Date/time mapping (java.time типы, LocalDateTime vs Instant)
18.7. @Embeddable и @Embedded (value objects)
18.8. @Transient — поле не в БД
18.9. Lifecycle callbacks (@PrePersist, @PostLoad, @PreUpdate и т.д.)
18.10. Equals/hashCode для entity — отдельная тема, много ловушек (id vs business key)

## 19. JPA: Relationships

19.1. @OneToOne (owning side, mappedBy)
19.2. @OneToMany и @ManyToOne (самые частые)
19.3. @ManyToMany (с @JoinTable)
19.4. Owning side vs inverse side — концепция, почему важно
19.5. Bidirectional vs unidirectional (когда какое)
19.6. Cascade types (PERSIST, MERGE, REMOVE, ALL — что когда)
19.7. orphanRemoval — отличие от CascadeType.REMOVE
19.8. FetchType.LAZY vs EAGER (LAZY по умолчанию для коллекций, EAGER для ToOne — переопределять)
19.9. LazyInitializationException — когда возникает, как избегать
19.10. Inheritance mapping (SINGLE_TABLE, JOINED, TABLE_PER_CLASS — компромиссы)
19.11. @MappedSuperclass — общий родитель без таблицы

## 20. JPA: Querying

20.1. find, getReference — разница (getReference не делает SELECT)
20.2. JPQL — основы (синтаксис похож на SQL, но по entities)
20.3. Named queries (@NamedQuery, @NamedNativeQuery)
20.4. Criteria API — типобезопасный query builder (зачем существует, когда применять)
20.5. Native queries (createNativeQuery) — когда JPQL не хватает
20.6. Projections (DTO в JPQL — new DTO(...))
20.7. Pagination (setFirstResult, setMaxResults — concept)
20.8. JOIN FETCH — решение N+1 проблемы
20.9. EntityGraph — альтернатива JOIN FETCH (декларативный fetch plan)

## 21. Spring Data JPA

21.1. Repository pattern в Spring Data (JpaRepository, CrudRepository, PagingAndSortingRepository)
21.2. Что Spring Data генерит автоматически (CRUD методы)
21.3. Derived query methods (findByName, findByAgeGreaterThan — парсинг имени метода)
21.4. @Query с JPQL
21.5. @Query с native SQL (nativeQuery=true)
21.6. @Modifying для UPDATE/DELETE
21.7. @Param — именованные параметры
21.8. Pageable и Page<T> — пагинация
21.9. Sort — сортировка
21.10. Specifications API — динамические запросы (вместо ручного Criteria)
21.11. Projections в Spring Data (интерфейсные projections, DTO projections)
21.12. Custom repository implementations (когда стандартного API не хватает)
21.13. Auditing (@CreatedDate, @LastModifiedDate, @EnableJpaAuditing)
21.14. @Lock — оптимистичные и пессимистичные блокировки

## 22. Transactions in Spring

22.1. ACID — напомнить базу
22.2. @Transactional — как работает (через AOP proxy)
22.3. Self-invocation problem с @Transactional (та же ловушка, что и с другими proxies)
22.4. Propagation (REQUIRED, REQUIRES_NEW, NESTED, SUPPORTS, NOT_SUPPORTED, MANDATORY, NEVER)
22.5. Isolation levels (READ_UNCOMMITTED, READ_COMMITTED, REPEATABLE_READ, SERIALIZABLE)
22.6. Phenomena (dirty read, non-repeatable read, phantom read — что какой уровень предотвращает)
22.7. Rollback правила (rollbackFor — по умолчанию только RuntimeException)
22.8. readOnly=true — что даёт (оптимизация, не семантическая защита)
22.9. timeout
22.10. Где @Transactional ставить (service layer — стандарт)
22.11. Programmatic transactions (TransactionTemplate — когда декларативного не хватает)
22.12. Distributed transactions — упомянуть (JTA, обычно избегают, saga pattern)

## 23. Hibernate Deep Dive: Performance and Pitfalls

23.1. First-level cache (session cache, persistence context — автоматически)
23.2. Second-level cache (entity cache между сессиями — EhCache, Caffeine, Hazelcast)
23.3. Query cache (отдельный кеш для результатов запросов)
23.4. N+1 problem — фундаментальная проблема, как обнаруживать и решать
23.5. JOIN FETCH vs EntityGraph vs @BatchSize
23.6. Open Session in View (OSIV) — anti-pattern, отключать в Boot
23.7. flush и flush modes (AUTO, COMMIT, MANUAL)
23.8. Dirty checking — как Hibernate понимает, что entity изменился
23.9. Batch inserts/updates (hibernate.jdbc.batch_size)
23.10. Stateless session (для bulk operations)
23.11. Cascade pitfalls (REMOVE может удалить лишнее)
23.12. Detached entities и merge() vs persist()
23.13. Equals/hashCode для entity (full discussion — почему обычный @Data из Lombok опасен)
23.14. Hibernate-specific аннотации (@DynamicUpdate, @Formula, @Where)

## 24. Database Migrations

24.1. Зачем нужны миграции (vs ручные SQL, vs hibernate.hbm2ddl.auto)
24.2. hibernate.hbm2ddl.auto (create, create-drop, update, validate, none — когда что)
24.3. Почему update в проде — плохая идея
24.4. Flyway — основы (миграции как версионированные SQL скрипты)
24.5. Flyway naming convention (V1__init.sql, V2__add_user.sql)
24.6. Liquibase — альтернатива Flyway (XML/YAML/SQL changesets)
24.7. Flyway vs Liquibase — что выбрать
24.8. Интеграция с Spring Boot (автоматический запуск при старте)
24.9. Repeatable migrations и callbacks

## 25. Caching in Spring

25.1. Spring Cache abstraction (@EnableCaching)
25.2. @Cacheable, @CachePut, @CacheEvict
25.3. Cache key generation (SpEL в key)
25.4. Cache providers (Caffeine — in-process, Redis — distributed)
25.5. Concurrent access (sync=true)
25.6. TTL и eviction policies
25.7. Когда кешировать, а когда нет (мутирующие данные, инвалидация)
25.8. Difference от Hibernate L2 cache
25.9. Distributed caching gotchas (serialization, consistency)

## 26. Async, Scheduling, Events

26.1. @Async — асинхронное выполнение методов (через AOP)
26.2. Конфигурация TaskExecutor (ThreadPoolTaskExecutor)
26.3. Self-invocation problem с @Async (третий раз — закрепить)
26.4. CompletableFuture как return type @Async
26.5. Виртуальные потоки в Spring (spring.threads.virtual.enabled — Spring Boot 3.2+)
26.6. @Scheduled — cron, fixedRate, fixedDelay (разница)
26.7. @EnableScheduling
26.8. ApplicationEvents — внутренний event bus
26.9. ApplicationEventPublisher
26.10. @EventListener
26.11. @TransactionalEventListener (события привязанные к транзакциям)
26.12. Async events

## 27. Testing Spring Applications

27.1. Уровни тестов в Spring (unit, slice, integration, e2e)
27.2. @SpringBootTest — поднимает полный контекст (когда оправдано)
27.3. Test slices — частичный контекст (@WebMvcTest, @DataJpaTest, @JsonTest, @WebFluxTest)
27.4. @MockBean и @SpyBean — замена бинов моками
27.5. MockMvc — тестирование контроллеров без реального HTTP
27.6. MockMvc API (perform, andExpect, andDo, jsonPath, content)
27.7. WebTestClient — для WebFlux и опционально для MVC
27.8. @DataJpaTest — тесты репозиториев с embedded DB
27.9. TestEntityManager
27.10. Testcontainers — тесты с реальной БД в Docker (must have в 2026)
27.11. @DynamicPropertySource для Testcontainers
27.12. @TestConfiguration — переопределение бинов в тестах
27.13. @Sql, @SqlGroup — выполнение SQL перед/после тестов
27.14. Profile для тестов (@ActiveProfiles)
27.15. Кеширование контекстов между тестами (как избегать пересоздания)
27.16. Integration testing best practices (фабрики данных, чистка между тестами)

## 28. Observability and Production Readiness

28.1. Spring Boot Actuator — что это
28.2. Стандартные endpoints (/health, /info, /metrics, /env, /loggers, /heapdump)
28.3. Security для actuator endpoints (не все наружу)
28.4. Кастомные health indicators (HealthIndicator)
28.5. Кастомные метрики (Micrometer, MeterRegistry)
28.6. Micrometer как фасад (как SLF4J для логов — но для метрик)
28.7. Экспорт метрик (Prometheus, Datadog, CloudWatch)
28.8. Distributed tracing (Micrometer Tracing, OpenTelemetry, ранее Sleuth+Zipkin)
28.9. Trace ID и Span ID — что это, как пробрасывать через сервисы
28.10. Логирование в продакшене (Logback config, structured logging JSON, MDC)
28.11. Graceful shutdown (server.shutdown=graceful)
28.12. Управление пулами (DB connection pool, HTTP client pool — что мониторить)

## 29. Building, Packaging, Deployment

29.1. Spring Boot Maven Plugin / Gradle Plugin
29.2. Executable JAR — что внутри (fat jar, nested jars, Boot's launcher)
29.3. Запуск (java -jar app.jar, флаги JVM)
29.4. Docker и Spring Boot (multi-stage builds, layered jars)
29.5. Buildpacks (Cloud Native Buildpacks, mvn spring-boot:build-image)
29.6. Native image с GraalVM (Spring Boot 3+, ограничения)
29.7. Конфигурация в Docker/Kubernetes (env vars, configmaps)
29.8. Health probes для Kubernetes (liveness vs readiness — отличия)

## 30. Spring WebFlux (обзорно — знать что есть и когда применять)

30.1. Reactive programming — концепция (backpressure, non-blocking)
30.2. Project Reactor — Mono и Flux
30.3. Когда WebFlux оправдан, а когда MVC + virtual threads достаточно
30.4. WebFlux vs Spring MVC — что отличается (Netty по умолчанию, нет Servlet API)
30.5. RouterFunction (functional style) vs annotated controllers
30.6. R2DBC — reactive доступ к БД (зачем нужен, если JDBC blocking)
30.7. Spring Cloud Gateway — построен на WebFlux
30.8. WebClient — reactive HTTP client (заменил RestTemplate)
30.9. Debugging reactive code — почему сложнее (stack traces ломаются)
30.10. Тренд 2026 — virtual threads делают WebFlux менее обязательным

## 31. Inter-Service Communication

31.1. RestTemplate — legacy, но всё ещё встречается
31.2. WebClient — современный HTTP client (работает и в MVC и в WebFlux)
31.3. RestClient — новый синхронный клиент (Spring 6.1+) — замена RestTemplate
31.4. HTTP Interface clients (declarative — @HttpExchange, Spring 6+)
31.5. OpenFeign — declarative HTTP client (популярен в Spring Cloud)
31.6. Retry, timeout, circuit breaker (Resilience4j)
31.7. Service discovery — обзорно (Eureka, Consul, Kubernetes services)

## 32. Spring Boot Interview-Critical Topics (review)

32.1. Как работает autoconfiguration (механика, не просто "магия")
32.2. Bean scopes и lifecycle (часто спрашивают подробно)
32.3. @Transactional pitfalls (self-invocation, propagation, rollback)
32.4. N+1 problem (детально, с примерами решения)
32.5. Spring Security filter chain (нарисовать на доске)
32.6. Жизненный цикл HTTP запроса от Tomcat до контроллера
32.7. Equals/hashCode для JPA entity
32.8. Difference: Filter vs Interceptor vs AOP advice (часто путают)

---

# Приоритеты при прохождении

**Подход к материалу — три уровня глубины:**

1. **Must know (учить глубоко)** — реально спрашивают на каждом mid+ собесе
2. **Nice to know (знать концептуально)** — могут спросить, но не везде
3. **Map territory (знать, что существует)** — можно нагуглить за 5 минут когда понадобится

**Java — must know темы:**

- HashMap внутреннее устройство (с treeification)
- equals/hashCode контракт
- JMM, happens-before, volatile
- GC основы (generations, G1)
- ExecutorService и CompletableFuture
- Virtual threads (Java 21+)
- Streams + Optional на практике
- Generics с type erasure

**Spring — must know темы:**

- DI, bean lifecycle и scopes (главы 2-3)
- Autoconfiguration механика (5.4)
- Полный HTTP request lifecycle от Tomcat до контроллера (главы 7-9)
- @Transactional pitfalls (22.3, 22.4)
- N+1 problem и решения (23.4, 23.5)
- Spring Security filter chain (13.1-13.3)
- JPA entity lifecycle и persistence context (17.3-17.5)
- Filter vs Interceptor vs AOP advice — различия (8, 12, 4)
- Testing slices и Testcontainers (27)

---

# Финальный совет

На собесе никто не спрашивает все 400+ пунктов. Спрашивают 20-30 ядерных тем, которые повторяются от компании к компании. Если эти ядерные темы знаешь глубоко, а остальное — на уровне "понимаю, что это и зачем" — это mid+ уровень.

Структурированная карта нужна не для зубрёжки, а для самопроверки: "могу я объяснить эту тему без подсказки?" Если да — это твоё знание. Если нет — возвращаешься и закрываешь пробел. AI как репетитор, а не как костыль.
