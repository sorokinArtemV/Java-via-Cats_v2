### Глава 11 — как исходные темы легли в уроки

**Урок 11.1 — Что такое JVM и как загружаются классы**

- 11.1. Что такое JVM (спецификация vs реализации — HotSpot, OpenJ9, GraalVM)
- 11.2. Загрузка класса (ClassLoader hierarchy: bootstrap, platform, app)
- _(+ добавлено: байткод и `javap`, parent delegation, `ClassNotFoundException` vs `NoClassDefFoundError`, три фазы линковки, идентичность класса = имя + загрузчик)_

**Урок 11.2 — Структура памяти JVM**

- 11.3. Структура памяти JVM (Heap, Stack, Metaspace, Code Cache, Direct Memory)
- 11.4. Heap detail (Young/Old generations, Eden, Survivor spaces)
- 11.10 _(частично)_. Флаги памяти: `-Xms`, `-Xmx`, `-Xss`, `-XX:MaxMetaspaceSize`, `MaxDirectMemorySize`, `MaxRAMPercentage`
- _(+ добавлено: PermGen → Metaspace, виды `OutOfMemoryError`, TLAB, CompressedOops и граница 32 GB, заголовок объекта)_

**Урок 11.3 — Garbage Collection**

- 11.5. GC основы (mark-sweep, copying, generational hypothesis)
- 11.6. GC алгоритмы (Serial, Parallel, G1, ZGC, Shenandoah — когда какой)
- 11.7. Stop-the-world паузы (что это, как минимизировать)
- 11.10 _(частично)_. GC-флаги: `-XX:+UseG1GC`/`UseZGC`, `MaxGCPauseMillis`, `-Xlog:gc` (вместо устаревшего `-verbose:gc`)
- _(+ добавлено: GC roots и достижимость, safepoints и TTSP, судьба CMS, reference types — Weak/Soft/Phantom/Cleaner)_

**Урок 11.4 — JIT-компиляция**

- 11.8. JIT compilation (C1, C2, tiered compilation, inline, deoptimization)
- 11.9. Escape analysis и scalar replacement
- _(+ добавлено: OSR, интринзики, мономорфные/мегаморфные call sites, warmup как продовая проблема)_

**Урок 11.5 — Утечки, дампы, профилирование**

- 11.11. Memory leaks в managed languages (как возможно с GC, типичные причины)
- 11.12. Heap dumps и анализ (jmap, VisualVM, MAT — `jhat` удалён в JDK 9, заменён)
- 11.13. Профилирование в проде (JFR, async-profiler)
- 11.10 _(сводка)_. Полная таблица-шпаргалка всех флагов главы
- _(+ добавлено: gistogramma классов, dominator tree и retained size, safepoint bias, NMT, Epsilon GC)_