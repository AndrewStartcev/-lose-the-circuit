# Technical Plan — Замкни цепь!

## 1. Цель

Сделать простую, детерминированную и хорошо тестируемую grid-систему без привязки gameplay к SDK площадок.

## 2. Архитектура проекта

```text
project.godot
assets/
  audio/
  fonts/
  gameplay/
  backgrounds/
  ui/
  fx/
  promo/
content/
  levels/
scenes/
  main/
  menu/
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
docs/.gdignore
source_assets/.gdignore
```

## 3. Main flow

```text
Main
├── ScreenRoot
│   ├── MainMenu / Gameplay / Result / ...
│   └── OverlayRoot
└── RuntimeSystems
```

Глобальные сервисы только там, где они реально нужны между сценами:

- `PlatformService`;
- `SaveService`;
- `AdsService`;
- `AudioService`.

Gameplay не знает, запущен он на Яндексе, Пикабу или локально.

## 4. Board model

Gameplay state отделён от визуальных Node.

### Direction bitmask

```text
N = 1
E = 2
S = 4
W = 8
```

Canonical masks:

- straight: `N | S`;
- corner: `N | E`;
- tee: `N | E | W`;
- cross: `N | E | S | W`.

Поворот на 90° выполняется над маской/ориентацией, а не через проверку имени sprite.

### Tile state

Минимальная модель клетки:

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

Визуальная сцена клетки только отображает состояние модели.

## 5. Energy propagation

После каждого допустимого поворота:

1. сбросить `energized` у conductive cells;
2. найти source;
3. выполнить BFS/DFS по соседям;
4. переход разрешён только при взаимных connector masks;
5. пометить достигнутые клетки energized;
6. обновить visual state;
7. проверить все lamp cells.

Board size максимум 6×6 в v1, поэтому полный пересчёт после каждого хода дешевле и надёжнее incremental graph logic.

## 6. Victory

```text
win = every lamp.energized == true
```

Проверка выполняется после propagation и короткого visual settle, чтобы игрок успел увидеть последний импульс.

В v1 нет автоматического поражения.

## 7. Level data

Формат: JSON, один файл на уровень или компактные pack-файлы после оценки overhead.

Пример схемы:

```json
{
  "id": 1,
  "rows": 4,
  "cols": 4,
  "difficulty": 1,
  "tiles": [
    {
      "x": 0,
      "y": 0,
      "type": "source",
      "rotation": 1,
      "solution_rotation": 1,
      "rotatable": false
    }
  ]
}
```

Для pipe type canonical topology определяется кодом, в JSON не дублируем connection mask без необходимости.

## 8. Level validator

Dev-only validator обязан проверять до релиза:

- координаты внутри сетки;
- уникальность cell coordinates;
- ровно один source;
- минимум одна lamp;
- допустимые tile types/rotation;
- solution state действительно зажигает все lamps;
- все заявленные conductive элементы solution-схемы принадлежат source component, если уровень не помечен исключением;
- initial state не должен случайно быть уже решён;
- tutorial levels имеют ожидаемые topology constraints.

Validator должен запускаться пакетно для всех уровней.

## 9. Hint system

Hint не анализирует «лучший ход» в реальном времени.

Алгоритм v1:

1. собрать rotatable tiles, где `rotation != solution_rotation`;
2. выбрать подходящую плитку по детерминированному правилу или near-source priority;
3. после подтверждённой оплаты/успешного rewarded привести её к `solution_rotation`;
4. выполнить propagation;
5. сохранить расход hint.

Это делает поведение предсказуемым и не требует solver.

## 10. Input

Один semantic action `rotate_tile(cell)` вызывается и от mouse, и от touch.

Запрещено иметь отдельную gameplay-логику для desktop/mobile.

На время:

- pause;
- result animation;
- ad;
- async transition

rotation input блокируется централизованно.

## 11. Responsive layout

### Base

`1600×900`, landscape.

### Gameplay composition

- Board container занимает основную центральную область.
- HUD располагается вокруг поля и не меняет размер клеток хаотично.
- Размер клетки вычисляется от доступной board area и rows/cols.
- Поле сохраняет квадратность каждой клетки.
- Background может crop/cover только в декоративной safe zone.

### Minimum QA

- 1280×720;
- 1366×768;
- 1600×900;
- 1920×1080;
- narrow landscape iframe;
- mobile landscape.

## 12. Rendering contract for tiles

Плитка собирается слоями:

```text
TileRoot
├── BaseVisual
├── ContentVisual
├── EnergyVisual
├── FxLayer
└── InteractionFeedback
```

`ContentVisual` вращается кратно 90°. `BaseVisual` не обязан вращаться.

Рекомендуемая asset-модель:

- общий `tile_base`;
- отдельные прозрачные `pipe_*_base`;
- отдельные прозрачные `pipe_*_energy` или shader/overlay;
- generator/lamp как отдельные content layers;
- FX отдельно.

Это исключает необходимость рисовать четыре ориентации каждой трубы и снижает риск несовпадения портов.

## 13. Animation

### Rotate

- input state меняется сразу;
- visual rotation 80–140 ms;
- новые electrical connections можно пересчитать сразу, но energy effect стартует после/в конце поворота для читаемости.

### Energy

Не нужен физический simulation. Использовать:

- emissive/glow state;
- короткий directional pulse/line effect;
- lamp on transition.

### Win

1. последняя lamp включается;
2. 300–700 ms celebration;
3. result overlay.

## 14. Save contract

Черновая schema:

```json
{
  "schema_version": 1,
  "unlocked_level": 1,
  "completed": {},
  "best_moves": {},
  "hints": 3,
  "tutorial": {},
  "settings": {
    "music": true,
    "sfx": true
  }
}
```

Local-first. Platform cloud sync не должен вызываться на каждый поворот.

## 15. Platform boundaries

Gameplay может запросить только абстрактные действия:

```text
AdsService.request_rewarded(HINT)
AdsService.request_interstitial(NATURAL_BREAK)
SaveService.save_progress(...)
PlatformService.is_available(...)
```

Никаких `JavaScriptBridge`/Yandex/Pikabu SDK calls внутри tile/board scripts.

## 16. Debug tools

До content production полезны dev-only функции:

- load level by id;
- show connector masks;
- show solution orientation;
- auto-solve current level;
- validate all levels;
- reset save;
- simulate ad unavailable.

Они не попадают в release UI.

## 17. Prototype Definition of Done

- [ ] Godot project создан по protocol.
- [ ] 4×4 graybox board.
- [ ] straight/corner/T/cross работают через единую topology model.
- [ ] mouse + touch semantic action.
- [ ] source propagation работает.
- [ ] 1–2 lamps корректно включаются.
- [ ] win определяется только моделью.
- [ ] restart уровня.
- [ ] минимум 3 тестовых уровня из data files.
- [ ] первый Web export запускается в браузере.
- [ ] responsive smoke test.

После этого можно фиксировать реальные tile/UI slots и запускать первые production assets.
