# Codex Task 01 — Visual Asset Batch 01

## Роль

Ты работаешь **только как художник / asset designer** игры **«Замкни цепь!»**.

Ты НЕ разработчик этой задачи.

Запрещено писать или менять игровой код, сцены, настройки Godot и gameplay-логику.

Текущий release target: **Пикабу Игры**.

Общие производственные правила:

`https://github.com/AndrewStartcev/starcev-games-protocols`

Перед началом прочитай в этом репозитории:

1. `AGENTS.md`
2. `docs/DESIGNER_TZ.md`
3. `docs/ASSET_MANIFEST.md`
4. `docs/GAME_BRIEF.md`
5. `docs/GDD.md`
6. `docs/DECISIONS.md`
7. `docs/PROJECT_STATE.md`

Из protocol repo для этой задачи достаточно:

- `docs/06_ASSET_PIPELINE.md`
- `docs/17_AI_AGENT_WORKFLOW.md`

Не трать контекст на чтение всего protocol repo.

---

# Главное правило

Приложенные изображения/концепты — **только визуальные референсы**.

Они показывают:

- атмосферу;
- уровень качества;
- industrial/electric style;
- тёмный металл;
- amber/yellow electricity;
- cyan/blue secondary accents;
- объёмный casual-game rendering.

Они НЕ являются:

- точным макетом;
- sprite sheet;
- готовыми ассетами;
- источником точных размеров;
- обязательным составом HUD;
- обязательной экономикой/монетами;
- инструкцией копировать элементы пиксель-в-пиксель.

Нужно сделать **собственные чистые production-ready элементы в этом направлении**.

---

# ЖЁСТКИЙ SCOPE

В этой задаче создаются **только графические ассеты Batch 01**.

НЕ трогать:

- `.gd`
- `.tscn`
- `project.godot`
- `.godot/`
- export presets
- gameplay logic
- баланс
- уровни
- SDK Пикабу
- рекламу как код
- сохранения
- аудио

Если таких файлов ещё нет — не создавать их.

Не пытайся собрать игру.

---

# Цель Batch 01

Нужно получить первый визуально качественный и геометрически правильный набор, по которому мы утвердим art direction перед массовым производством.

Сделать:

1. базовую плитку;
2. четыре типа проводников;
3. energized-варианты проводников;
4. генератор;
5. лампочку OFF / ON;
6. несколько базовых UI-элементов для проверки стиля;
7. один preview/contact sheet только для просмотра.

**Каждый реальный игровой элемент должен существовать отдельным файлом.**

Contact sheet не является production asset.

---

# Критично: единая геометрия проводов

Это самое важное требование всей задачи.

Все tile-related source assets создавай на **едином квадратном master canvas 1024×1024**.

Потенциальные connector points находятся строго:

- TOP = `(512, 0)`
- RIGHT = `(1024, 512)`
- BOTTOM = `(512, 1024)`
- LEFT = `(0, 512)`

Ось трубы должна точно приходить в эти точки.

Все трубы должны иметь:

- одинаковую центральную ось;
- одинаковую толщину;
- одинаковый профиль;
- одинаковый диаметр соединения;
- одинаковый визуальный материал;
- одинаковый масштаб.

### Запрещено

Не рисовать straight / corner / tee / cross как четыре независимые художественные картинки.

Они должны быть производными от **одной master geometry**.

Если наложить соседние sprites на идеальную квадратную сетку, трубы должны соединяться без:

- ступеньки;
- зазора;
- смещения;
- разной толщины;
- разного диаметра;
- перспективного перекоса.

### Perspective

Внутри gameplay tile — **нулевая перспектива**.

Объём создаётся:

- bevel;
- shading;
- material highlights;
- glow;
- ambient occlusion-like shading.

Но не наклоном или геометрическим смещением трубы.

---

# 1. Tile base

Создай:

`assets/gameplay/tiles/tile_base.webp`

Требования:

- квадрат;
- polished dark navy / graphite metal;
- аккуратный bevel;
- небольшие industrial details допустимы;
- не слишком шумный;
- не содержит трубы;
- не содержит текста;
- центр спокойный;
- хорошо повторяется в сетке;
- углы и края не должны создавать визуальный хаос при 6×6.

Source/master можно хранить в:

`source_assets/gameplay/tiles/`

---

# 2. Pipe base family

Создай прозрачные изображения **БЕЗ плитки и БЕЗ фона**:

- `assets/gameplay/pipes/pipe_straight_base.webp`
- `assets/gameplay/pipes/pipe_corner_base.webp`
- `assets/gameplay/pipes/pipe_tee_base.webp`
- `assets/gameplay/pipes/pipe_cross_base.webp`

Все — 1024×1024 source geometry, transparent background.

Стиль:

- clean steel / cool blue-gray metal;
- premium casual rendering;
- достаточно толстые, чтобы читаться на небольшом экране;
- без грязного реализма;
- без pixel art;
- без общей тени от «квадратной клетки»;
- труба может иметь собственный мягкий material shadow/highlight.

## Canonical orientation

- straight: TOP ↔ BOTTOM;
- corner: TOP ↔ RIGHT;
- tee: LEFT ↔ TOP ↔ RIGHT;
- cross: все 4 стороны.

Остальные ориентации будут получаться поворотом на 90° в игре.

Не создавай отдельные `_left`, `_right`, `_up`, `_down` версии.

---

# 3. Energized pipe family

Создай соответствующие прозрачные overlays/states:

- `assets/gameplay/pipes/pipe_straight_energy.webp`
- `assets/gameplay/pipes/pipe_corner_energy.webp`
- `assets/gameplay/pipes/pipe_tee_energy.webp`
- `assets/gameplay/pipes/pipe_cross_energy.webp`

Геометрия должна **совпадать 1:1** с base family.

Энергия:

- amber / yellow / orange-gold;
- яркое внутреннее свечение;
- мягкий bloom вокруг проводника;
- без чрезмерного белого пересвета;
- форма трубы не меняется;
- glow не должен визуально расширять соединительный порт так, что соседние элементы выглядят разного размера.

Предпочтение: energy asset можно накладывать поверх base.

---

# 4. Generator

Создай:

`assets/gameplay/objects/generator.webp`

Требования:

- прозрачный фон;
- 1024×1024 master canvas;
- центрированный корпус;
- визуально мощнее обычной трубы;
- polished industrial-electric device;
- amber lightning/energy core;
- **круглые/технические connector sockets по всем 4 сторонам**;
- центры connector sockets строго совпадают с теми же TOP/RIGHT/BOTTOM/LEFT точками master geometry;
- ни один connector не смещён;
- никаких встроенных текстов;
- не рисовать квадратную gameplay плитку внутри ассета.

Генератор имеет **одно основное визуальное состояние** в Batch 01.

Дополнительное динамическое glow/FX можно будет сделать отдельным overlay позже.

---

# 5. Lamp

Создай два отдельных файла:

- `assets/gameplay/objects/lamp_off.webp`
- `assets/gameplay/objects/lamp_on.webp`

Требования:

- прозрачный фон;
- 1024×1024 master canvas;
- форма OFF и ON совпадает 1:1;
- canonical connector находится строго по BOTTOM `(512, 1024)`;
- lamp сможет поворачиваться в игре вместе с connector orientation;
- OFF: холодная/тёмная лампа, но силуэт читается;
- ON: сильное тёплое amber glow;
- не делать ON другой лампочкой по форме;
- не добавлять tile background.

---

# 6. UI test family

Нужно проверить, что gameplay assets и UI ощущаются одной игрой.

Создай небольшой тестовый набор:

- `assets/ui/panels/panel_basic.webp`
- `assets/ui/buttons/button_primary.webp`
- `assets/ui/buttons/button_secondary.webp`
- `assets/ui/icons/icon_hint.webp`
- `assets/ui/icons/icon_rewarded.webp`
- `assets/ui/icons/icon_pause.webp`

Правила:

- никаких надписей внутри кнопок;
- никаких цен и цифр;
- primary CTA — amber/gold;
- secondary — cyan/blue/dark steel;
- панели — dark navy/graphite;
- icon_hint может использовать лампочку, но визуально отличаться от gameplay lamp;
- icon_rewarded — понятная иконка просмотра рекламы/видео без текста;
- UI должен быть чистым и не выглядеть как интерфейс сайта.

Панель и кнопки проектируй так, чтобы позже их можно было использовать как 9-patch / scalable frame. Не запекай сложную композицию по всей ширине.

---

# 7. FX test

Создай максимум 2 тестовых эффекта:

- `assets/fx/fx_electric_spark_01.webp`
- `assets/fx/fx_lamp_ignite.webp`

Transparent background.

Это не sprite animation sheet — пока только отдельные ключевые FX для проверки языка эффектов.

---

# 8. Preview sheet

Дополнительно сделай **один обзорный лист**, где показаны рядом:

- tile base;
- 4 pipe types OFF;
- 4 pipe types energized;
- generator;
- lamp OFF/ON;
- UI examples;
- FX examples.

Путь:

`source_assets/previews/batch_01_preview.png`

Этот файл нужен только для быстрой визуальной проверки.

**Не нарезать его потом автоматически на игровые ассеты.**

---

# 9. Качество изображения

Нам не нужен pixel art.

Нужен уровень polished casual/mobile/browser game:

- чистые материалы;
- аккуратные края;
- качественный anti-aliasing;
- без мыла;
- без артефактов генерации;
- без кривых винтов/геометрии;
- без случайного текста;
- без водяных знаков;
- без обрезанных деталей;
- без разной перспективы между ассетами.

Если изображение получилось визуально красивым, но геометрически несовместимым — оно считается **непригодным**.

---

# 10. Форматы

Runtime-ready:

- WebP lossless или PNG lossless;
- alpha для pipe/object/icon/FX assets;
- не использовать JPEG для прозрачных элементов.

Если инструмент генерации сначала создаёт PNG — это нормально.

Не ухудшай качество повторным aggressive compression.

---

# 11. Что НЕ делать в Batch 01

Пока не создавать:

- весь фон игры;
- финальное главное меню;
- логотип;
- магазин;
- монеты;
- сундуки;
- level select;
- результат уровня;
- настройки;
- десятки FX;
- promo/icon/cover;
- полный UI pack;
- вариации тем;
- orientation variants pipe sprites.

Сначала утверждаем этот пакет.

---

# 12. Обязательная самопроверка

Перед сдачей создай технический preview-grid и визуально проверь минимум:

1. straight → straight;
2. straight → corner;
3. corner → tee;
4. tee → cross;
5. generator → straight со всех четырёх сторон;
6. lamp → straight;
7. поворот corner на 90/180/270°;
8. совпадение OFF / ENERGY geometry.

Все соединения должны быть идеально ровными.

Если нет — исправь ассеты до сдачи.

---

# 13. Отчёт

Создай/обнови:

`docs/ASSETS_REPORT.md`

В отчёте перечисли:

- все созданные файлы;
- размеры;
- формат;
- alpha yes/no;
- кратко назначение;
- какие geometry tests выполнены;
- какие элементы требуют следующего согласования;
- какие файлы являются source-only;
- какие runtime-ready.

Если использовались внешние материалы/текстуры/шрифты — обязательно обнови `docs/ASSET_SOURCES.md` с источником и лицензией.

---

# 14. Git

Работай прямо в текущем репозитории.

Не удаляй существующие docs.

Не меняй проектную документацию по механике без необходимости.

После завершения:

1. убедись, что нет случайных temp/garbage файлов;
2. проверь paths;
3. сделай один понятный commit;
4. выдай короткий итог:
   - что создано;
   - где лежит preview;
   - где лежит отчёт;
   - что нужно визуально проверить владельцу проекта.

После Batch 01 **остановись**.

Не начинай Batch 02 без подтверждения.
