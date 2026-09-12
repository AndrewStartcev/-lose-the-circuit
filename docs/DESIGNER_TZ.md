# ТЗ дизайнеру — Замкни цепь!

## 0. Критично: что являются референсами

Изображения, приложенные к постановке игры, — **визуализации направления**, а не макеты для копирования и не production sprite sheets.

Из них берём:

- настроение;
- сочетание тёмного металла + тёплой электрической энергии;
- ощущение объёмного casual UI;
- industrial/electrical окружение;
- визуальный контраст `cold inactive` / `warm energized`.

Из них **НЕ берём автоматически**:

- точную сетку;
- состав HUD;
- монеты/магазин;
- конкретные кнопки;
- точные размеры;
- все декоративные элементы;
- готовые трубы, которые можно просто вырезать из картинки;
- точный текст.

Главный источник истины для production после graybox: `docs/ASSET_MANIFEST.md`.

---

# 1. Общий стиль

## Жанр визуала

Полированная casual puzzle-game в стилистике электрической мастерской/технической панели.

Нужно ощущение:

- качественной мобильной/браузерной игры;
- объёмных материалов;
- понятных игровых объектов;
- приятного электрического свечения;
- чистой визуальной иерархии.

## Не надо

- pixel art;
- реалистичной грязи/ржавчины до потери читаемости;
- хоррора;
- постапокалипсиса;
- слишком мелких декоративных деталей на gameplay tiles;
- перспективной деформации сетки;
- изометрии;
- псевдо-3D, из-за которого трубы перестают совпадать по краям.

---

# 2. Палитра

Ориентир, а не жёсткие HEX:

- **основа:** dark navy / graphite / blue-black metal;
- **inactive metal:** холодный steel / desaturated blue-gray;
- **energy:** amber / warm yellow / orange-gold;
- **secondary UI accent:** cyan / electric blue;
- **success:** тёплый свет + мягкий bloom;
- **danger:** использовать редко, muted red/orange.

Важно: cyan и amber не должны конкурировать. В gameplay energized network — главный яркий акцент.

---

# 3. Материалы

## Панели и окружение

- металл с мягкими bevels;
- болты/стыки допустимы;
- детали должны выглядеть собранными, а не как фотография промышленного щита;
- фон может быть богаче по деталям, но центр около игрового поля спокойнее.

## Трубы/проводники

- чистый металлический/изолированный канал;
- достаточная толщина для быстрого считывания;
- inactive state холодный;
- energized state — та же геометрия + glow/emission/light overlay.

---

# 4. Самое важное — геометрия проводов

Это обязательный production contract.

## Единая сетка

Каждый gameplay tile — квадрат.

У него ровно четыре потенциальные точки подключения:

```text
            TOP
             ●
             │
LEFT ●───────┼───────● RIGHT
             │
             ●
           BOTTOM
```

Точки подключения:

- строго по центру каждой стороны;
- на одинаковом расстоянии от края;
- одинакового диаметра;
- одинакового профиля.

## Запрещено

Нельзя рисовать:

- straight;
- corner;
- T;
- cross

как четыре независимые «красивые картинки».

Они должны быть **производными от одной master geometry**.

Если взять corner и повернуть на 90°, его конечная точка обязана совпасть с straight/T/cross без ручной подгонки.

## Нулевая перспектива gameplay content

Внутри квадратного tile:

- нет перспективного сужения;
- нет наклона камеры;
- нет смещения портов ради «объёма»;
- объём создаётся светом, материалом, bevel и shading, а не геометрическим перекосом.

## Rotation test

Перед сдачей pipe family дизайнер обязан проверить:

1. straight ↔ straight;
2. straight ↔ corner;
3. corner ↔ T;
4. T ↔ cross;
5. все четыре направления;
6. поворот каждого элемента на 90°/180°/270°.

Ни в одной комбинации не должно быть ступеньки, разницы толщины или смещения оси.

---

# 5. Правильная asset-модель

Не рисуем каждую клетку как готовый квадрат с трубой внутри.

Gameplay tile собирается слоями:

```text
BASE TILE
+ CONTENT (pipe / generator / lamp / blocker)
+ ENERGY OVERLAY
+ FX
+ INTERACTION FEEDBACK
```

Это позволяет:

- переиспользовать одну плитку;
- вращать content в Godot;
- не плодить 4 ориентации;
- менять energized state без смены всей клетки;
- исправлять фон и трубу независимо.

---

# 6. Pipe assets

## Обязательные topology

1. `straight`
2. `corner`
3. `tee`
4. `cross`

## Предпочтительный production вариант

Для каждого topology:

- `base` — inactive metallic pipe, transparent background;
- `energy` — только свет/энергия/эмиссия, transparent background.

Пример будущих путей:

```text
assets/gameplay/pipes/pipe_straight_base.webp
assets/gameplay/pipes/pipe_straight_energy.webp
assets/gameplay/pipes/pipe_corner_base.webp
assets/gameplay/pipes/pipe_corner_energy.webp
assets/gameplay/pipes/pipe_tee_base.webp
assets/gameplay/pipes/pipe_tee_energy.webp
assets/gameplay/pipes/pipe_cross_base.webp
assets/gameplay/pipes/pipe_cross_energy.webp
```

Ориентации создаются rotation в Godot.

## Фон

Pipe assets должны быть **без фона и без плитки**, с alpha.

Плитка — отдельный asset.

---

# 7. Tile base

Нужна одна базовая gameplay plate, которая:

- квадратная;
- работает в повторяющейся сетке;
- имеет аккуратную рамку/внутреннюю фаску;
- не создаёт слишком сильные seams;
- не содержит pipe;
- не содержит встроенного текста;
- не зависит от направления.

Допускаются состояния/overlays:

- normal;
- hover;
- pressed;
- selected/hint highlight.

Лучше делать feedback отдельными overlay/StyleBox, а не 4 полностью разными тяжёлыми картинками.

---

# 8. Generator

Генератор — главный источник энергии и важная визуальная точка.

Требования:

- читается мгновенно даже без текста;
- визуально мощнее обычной трубы;
- не перекрывает точки соединения;
- порт/порты совпадают с pipe master geometry;
- inactive/active геометрия одинакова;
- active state даёт amber electrical light.

Нельзя делать генератор только с «левым/правым» декоративным соединением, если level data потенциально может использовать другие стороны. Production contract портов определяется после prototype; визуально корпус должен позволять универсальную ориентацию либо иметь отдельный корректный connector layer.

---

# 9. Lamp

Нужно минимум два состояния:

- off;
- on.

Требования:

- одна и та же форма/позиция;
- off — читаемый силуэт, но не яркий;
- on — сильный тёплый glow;
- нижний электрический connector точно совпадает с общей сеткой;
- сама лампа не должна визуально сливаться с UI-иконкой подсказки.

При необходимости lamp content может поворачиваться как endpoint tile, но production решение фиксируется после graybox.

---

# 10. Blocker / empty

Если используется blocker:

- должен ясно показывать «сюда нельзя провести сеть»;
- не выглядеть как сломанная интерактивная труба;
- не быть ярче energized network.

Empty может быть просто base tile без content.

---

# 11. Energy FX

Нужно ощущение «ток реально пошёл», но без визуального шума.

Желаемые эффекты:

- короткий электрический pulse вдоль трубы;
- мягкий emission/glow;
- небольшой spark на успешном соединении;
- lamp ignition flash;
- компактный success burst.

FX не должны менять геометрию трубы и закрывать соседние клетки.

---

# 12. Background / environment

## Задача

Создать industrial-electric workshop frame вокруг gameplay, а не огромную иллюстрацию, на которую потом накладывается всё остальное.

## Композиция

- центр/зона board спокойнее;
- по краям могут быть панели, кабели, лампы, ящики, warning-signs;
- decorative props не должны выглядеть интерактивными;
- никакой важный объект нельзя размещать там, где его гарантированно закроет HUD.

## Safe crop

Background обязан выдерживать crop на разных landscape aspect ratios.

Критичные элементы не размещать у самого края.

Точные safe zones фиксируются после UX graybox.

---

# 13. UI

## Основная идея

UI — dark metal / blue-black panels с ясной типографикой.

CTA может использовать warm amber/gold.

Secondary actions — cyan/steel.

## Кнопки

Production кнопки должны поддерживать:

- normal;
- hover;
- pressed;
- disabled.

Но предпочтительно делать scalable frame / 9-patch + dynamic label/icon, а не запекать надпись в изображение.

## Panels

Panel family должна быть пригодна для 9-patch/StyleBox.

Corners и border не должны растягиваться.

---

# 14. Текст

## По умолчанию весь UI-текст dynamic

Не запекать в графику:

- `Уровень 17`;
- количество ходов;
- best moves;
- подсказки;
- цены;
- награды;
- число уровней;
- кнопки `Играть`, `Продолжить`, `Пауза` и т.п.

## Можно baked

- логотип `Замкни цепь!`, если утверждён финально;
- декоративные вывески окружения, если они не несут важный gameplay смысл и не мешают локализации.

---

# 15. Typography

Конкретный шрифт утверждается отдельно.

Требования:

- кириллица;
- хороший bold/semibold;
- читаемость на 1280×720;
- цифры хорошо различимы;
- без ультратонкого начертания;
- не делать весь UI condensed-font ради «технического» ощущения.

---

# 16. Logo

Рабочее название: **«Замкни цепь!»**

Направление:

- compact;
- industrial metal plate / electric mark;
- один яркий lightning/electric accent;
- читается в малом размере;
- не занимает половину gameplay screen.

Логотип используется в menu/promo, но не обязан висеть во время gameplay.

---

# 17. Этапы работы дизайнера

## Этап A — Art direction confirmation

Сейчас, до graybox, можно сделать только:

1. style board;
2. пример материала tile;
3. master pipe geometry sketch;
4. generator/lamp direction;
5. один UI panel/button direction;
6. menu/gameplay mood composition.

**Не делать весь asset pack.**

## Этап B — Test production pack

Только после того, как разработчик даст реальные slot sizes из graybox.

Первые 3–5 production assets:

1. tile base;
2. pipe straight + corner из общей geometry;
3. generator;
4. lamp off/on;
5. один UI frame/button family.

Разработчик интегрирует их в Godot и отдаёт screenshot.

## Этап C — Full pack

Только после подтверждения integration screenshot.

---

# 18. Форматы

Финальные runtime assets:

- PNG/WebP lossless для UI и alpha overlays;
- большие непрозрачные backgrounds — WebP после visual quality test;
- transparent empty margins обрезать;
- без 4K «на всякий случай»;
- source files можно хранить в `source_assets/` и не импортировать Godot.

Точные source sizes будут в `ASSET_MANIFEST.md` после slot freeze.

---

# 19. Naming

Примеры:

```text
bg_workshop.webp
tile_base.webp
pipe_straight_base.webp
pipe_straight_energy.webp
pipe_corner_base.webp
pipe_corner_energy.webp
generator_base.webp
generator_energy.webp
lamp_off.webp
lamp_on.webp
fx_electric_pulse.webp
panel_primary.9patch.webp
btn_primary_frame.9patch.webp
icon_hint.webp
icon_pause.webp
```

Никаких `image1_final_new2.png`.

---

# 20. Acceptance checklist

Asset pack не принимается, если:

- трубы не совпадают по центру сторон;
- straight/corner/T/cross имеют разную толщину;
- energized state сдвигает геометрию;
- на pipe sprite есть tile/background;
- в кнопки запечён изменяемый текст;
- panel нельзя масштабировать без деформации;
- элементы нарезаны из общего concept sheet с остатками общего света/перспективы;
- icon/cover обещают интерфейс, которого нет в игре;
- реальный screenshot заметно слабее approved direction.

## Главный критерий

Если отключить glow, все соединения всё равно должны быть идеально ровными и легко читаться.
