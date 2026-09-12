# Codex Task 01 — Core Loop Prototype

## Role

Ты работаешь как разработчик игры **«Замкни цепь!»** в уже открытом Git-репозитории.

Не придумывай новый production process. Источник истины по процессу:

`https://github.com/AndrewStartcev/starcev-games-protocols`

Текущий release target — **Пикабу Игры**. Yandex сейчас не входит в scope.

## Перед началом

Обязательно прочитай в текущем репозитории:

1. `AGENTS.md`
2. `docs/PROJECT_STATE.md`
3. `docs/GAME_BRIEF.md`
4. `docs/GDD.md`
5. `docs/TECHNICAL_PLAN.md`
6. `docs/PRODUCTION_PLAN.md`
7. `docs/DECISIONS.md`

Из protocols для этой задачи достаточно:

- `docs/00_MASTER_PROTOCOL.md`
- `docs/03_GODOT_ARCHITECTURE.md`
- `docs/04_WEB_RUNTIME.md`
- `docs/05_UI_UX_RESPONSIVE.md`
- `docs/14_QA_TESTING.md`
- `docs/17_AI_AGENT_WORKFLOW.md`

Не перечитывай весь protocol repo без необходимости.

---

# Цель задачи

Создать **Milestone 1 — рабочий graybox prototype** игры на Godot.

Это не финальная игра и не этап дизайна.

Нужно доказать, что core loop работает правильно:

```text
поворот плитки
→ пересчёт соединений
→ распространение энергии от генератора
→ включение подключённых ламп
→ победа, когда включены все лампы
→ следующий уровень / restart
```

---

# Технологии

Обязательно:

- Godot `4.7.2-stable`;
- GDScript;
- Renderer: `Compatibility`;
- WebGL 2;
- single-thread Web export;
- base design resolution `1600×900`;
- landscape;
- русский UI;
- desktop mouse + touch semantics.

Не использовать:

- C#;
- GDExtension;
- сторонние тяжёлые плагины;
- внешние CDN для runtime;
- multithread Web;
- Yandex SDK.

---

# ВАЖНО: что сейчас НЕ делать

На этом этапе запрещено:

- создавать финальную красивую графику;
- генерировать UI asset pack;
- использовать изображения-референсы как готовые макеты;
- нарезать присланные concept sheets на ассеты;
- подключать Pikabu SDK;
- писать cloud save;
- подключать реальную рекламу;
- добавлять Yandex;
- добавлять магазин, монеты, жизни, энергию;
- делать 50 финальных уровней;
- менять механику из GDD без причины.

Изображения проекта — **только art direction reference**, не source of truth.

Graybox может быть простым, но должен быть аккуратным и понятным.

---

# Архитектура

Создай структуру проекта близко к:

```text
project.godot
assets/
content/
  levels/
scenes/
  main/
  gameplay/
  ui/
scripts/
  gameplay/
  ui/
services/
  platform/
  save/
  ads/
  audio/
localization/
docs/
```

`docs/` должен быть исключён из Godot import через `.gdignore`.

Не создавай manager-классы «на всякий случай».

Для prototype достаточно gameplay архитектуры + минимальных stubs/interfaces там, где это реально нужно.

---

# Board model

Игровая логика должна быть отделена от визуальных Node.

Используй direction bitmask:

```text
N = 1
E = 2
S = 4
W = 8
```

Canonical topology:

```text
straight = N | S
corner   = N | E
tee      = N | E | W
cross    = N | E | S | W
```

Поворот выполняется математически через `rotation = 0..3` и трансформацию connections.

Нельзя определять логику соединения по имени sprite или цвету Node.

Минимальная модель tile:

```text
id
type
rotation
initial_rotation
solution_rotation
base_connections
rotatable
energized
```

Типы prototype:

- `straight`
- `corner`
- `tee`
- `cross`
- `source`
- `lamp`
- `empty`
- `blocker`

---

# Правило соединения

Две соседние conductive клетки соединены только если:

1. клетка A имеет порт в сторону B;
2. клетка B имеет обратный порт в сторону A.

Никаких соединений «почти рядом».

Все порты логически находятся строго по центрам сторон клетки.

---

# Распространение энергии

После каждого допустимого поворота:

1. сбросить energized state;
2. найти source;
3. выполнить BFS или DFS;
4. переходить только через взаимно совместимые порты;
5. отметить все достижимые conductive cells как energized;
6. обновить визуальное состояние;
7. проверить лампы.

Уровень пройден только если:

```text
every lamp.energized == true
```

Размеры поля маленькие, поэтому пересчитывай всю сеть после каждого хода. Не усложняй incremental graph optimization.

---

# Поворот плиток

- ЛКМ / tap по rotatable tile → поворот на 90° по часовой стрелке.
- Один input = один поворот.
- Source и lamp по умолчанию не вращаются.
- Blocker/empty не вращаются.
- Не допускай двойной обработки touch + emulated mouse.

Добавь короткий визуальный поворот примерно 80–140 ms, но игровая модель должна оставаться детерминированной.

---

# Graybox визуал

Нужна только технически аккуратная визуализация:

- квадратная клетка;
- линии/простые формы проводов;
- source понятно отличается;
- lamp off/on понятно отличаются;
- energized network имеет отдельный яркий цвет/glow-like feedback;
- неэнергизированная сеть спокойная/серая;
- hover на desktop допустим;
- selected/pressed feedback допустим.

Не пытайся повторять industrial art из concept images.

Главное сейчас — читаемость topology.

---

# Level data

Уровни должны грузиться из данных, а не быть захардкожены Node-ами в сцене.

Сделай минимум **5 тестовых уровней** в `content/levels/`.

Нужно проверить:

1. straight;
2. corner;
3. tee;
4. cross;
5. несколько ламп.

Каждый level содержит:

- id;
- rows;
- cols;
- tiles;
- initial rotation;
- canonical `solution_rotation`.

Сделай небольшой validator, который как минимум проверяет:

- ровно один source;
- есть хотя бы одна lamp;
- coordinates внутри сетки;
- нет дублирования cells;
- solution реально зажигает все lamps;
- initial state не solved.

---

# Gameplay UI prototype

Достаточно:

- `Уровень N`;
- `Ходы: N`;
- кнопка `Заново`;
- кнопка `Пауза` или `Esc`;
- простой win/result overlay;
- `Следующий уровень`.

Пока не нужны:

- монеты;
- магазин;
- реальные рекламные кнопки;
- красивые панели;
- логотип;
- финальные иконки.

Можно сделать disabled/dev кнопку `Подсказка`, только если это помогает проверить solution data, но реальную рекламу не подключать.

---

# Responsive

Base design: `1600×900`.

Игровое поле:

- клетки всегда квадратные;
- board центрируется;
- board масштабируется от доступной области;
- HUD не должен перекрывать board;
- не использовать абсолютные магические координаты там, где нужны anchors/Containers.

Проверить минимум:

- 1280×720;
- 1366×768;
- 1600×900;
- 1920×1080;
- узкий landscape viewport.

---

# Local platform fallback

На этом milestone реального Pikabu SDK нет.

Но код не должен позже потребовать переписывания gameplay.

Не делай прямых platform calls из gameplay.

Если нужны заглушки — создай минимальный local adapter/interface, но не строй лишнюю инфраструктуру раньше времени.

---

# Debug / developer utilities

Будет полезно сделать dev-only возможности:

- загрузить level по ID;
- показать solution;
- auto-solve текущий level;
- validate all test levels;
- reset текущего уровня.

Они не должны попадать в обычный release UI.

---

# Что должно работать в конце

Обязательный Definition of Done:

- [ ] проект открывается в Godot 4.7.2 без ошибок;
- [ ] renderer Compatibility;
- [ ] 5 data-driven test levels;
- [ ] straight/corner/tee/cross имеют корректную topology;
- [ ] rotation работает мышью;
- [ ] touch semantic реализован;
- [ ] энергия распространяется только через реальные соединения;
- [ ] lamp корректно меняет off/on state;
- [ ] несколько lamps работают;
- [ ] победа определяется моделью, а не визуалом;
- [ ] moves counter работает;
- [ ] restart возвращает initial rotations;
- [ ] next level работает;
- [ ] pause не ломает state;
- [ ] resize не ломает board;
- [ ] validator проходит для всех тестовых levels;
- [ ] создан Web export preset;
- [ ] локальный Web build запускается без Pikabu/Yandex SDK;
- [ ] в Output/Debugger нет критических ошибок.

---

# После реализации

Не переходи самостоятельно к production graphics или Pikabu SDK.

Сначала:

1. проверь проект;
2. обнови `docs/PROJECT_STATE.md`;
3. при новых архитектурных решениях обнови `docs/DECISIONS.md`;
4. дай короткий отчёт:
   - что сделано;
   - какие файлы/папки добавлены;
   - что реально протестировано;
   - известные проблемы;
   - что мне нужно проверить вручную в Godot;
5. если Git доступен — сделай один понятный commit для milestone.

После этого остановись и жди подтверждения следующего этапа.
