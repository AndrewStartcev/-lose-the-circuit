# Technical Plan — Замкни цепь!

## 1. Цель

Сделать простую, детерминированную и хорошо тестируемую grid-систему без привязки gameplay к SDK площадки. Release target v1 — Пикабу Игры; локальный adapter обязателен для разработки.

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

Gameplay не знает, запущен он на Пикабу или локально. Yandex adapter в v1 не реализуется.

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
3. выполнить BFS/DFS;
4. переход разрешён только при взаимных connector masks;
5. пометить достигнутые клетки energized;
6. обновить visual state;
7. проверить все lamp cells.

Board максимум 6×6, поэтому полный пересчёт после каждого хода предпочтительнее сложной incremental graph logic.

## 6. Victory

```text
win = every lamp.energized == true
```

В v1 нет автоматического поражения.

## 7. Level data

Формат: JSON. Для каждого уровня хранится canonical solution orientation.

Пример:

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

## 8. Level validator

Dev-only validator проверяет:

- координаты и уникальность cells;
- ровно один source;
- минимум одну lamp;
- допустимые tile types/rotation;
- solution реально зажигает все lamps;
- initial state не solved;
- tutorial constraints.

## 9. Hint system

Алгоритм v1:

1. найти rotatable tiles, где `rotation != solution_rotation`;
2. выбрать плитку по детерминированному правилу;
3. после бесплатного hint credit или подтверждённого rewarded привести её к `solution_rotation`;
4. propagation;
5. save.

Strong hint выполняет то же до трёх раз за один подтверждённый rewarded context.

## 10. Input

Один semantic action `rotate_tile(cell)` для mouse и touch. На время pause/result/ad/transition input блокируется централизованно.

## 11. Responsive layout

Base: `1600×900`, landscape.

- Board container занимает основную центральную область.
- Размер клетки вычисляется от доступной board area.
- Клетки всегда квадратные.
- HUD не ломает board при resize.
- Background crop разрешён только в декоративной safe zone.

Minimum QA: 1280×720, 1366×768, 1600×900, 1920×1080, narrow landscape iframe, mobile landscape.

## 12. Rendering contract for tiles

```text
TileRoot
├── BaseVisual
├── ContentVisual
├── EnergyVisual
├── FxLayer
└── InteractionFeedback
```

`ContentVisual` вращается кратно 90°. `BaseVisual` не обязан вращаться.

Asset model:

- общий `tile_base`;
- прозрачные `pipe_*_base`;
- прозрачные energized overlays или shader;
- generator/lamp отдельными layers;
- FX отдельно.

Straight/corner/T/cross строятся из одной master connector geometry.

## 13. Animation

### Rotate

- state меняется сразу;
- visual rotation 80–140 ms;
- energy feedback после/в конце поворота.

### Energy

Emissive/glow + короткий directional pulse, без физической симуляции.

### Win

Последняя lamp → 300–700 ms celebration → result overlay.

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

Local-first. Pikabu cloud sync — debounced после значимых событий, не после каждого поворота.

## 15. Platform boundaries

Gameplay вызывает только абстрактные действия:

```text
AdsService.request_rewarded(HINT_ONE)
AdsService.request_rewarded(HINT_THREE)
AdsService.request_rewarded(EARN_HINT)
AdsService.request_fullscreen(LEVEL_COMPLETE)
SaveService.save_progress(...)
PlatformService.is_available(...)
```

Никаких прямых `JavaScriptBridge` / `PkbSDK` calls внутри tile/board scripts.

Adapters v1:

- `LocalPlatformAdapter`;
- `PikabuPlatformAdapter`.

## 16. AdsService contract — Pikabu

Состояния минимум:

```text
IDLE
CHECKING
SHOWING_FULLSCREEN
SHOWING_REWARDED
RECOVERING
```

### Fullscreen gate

Хранить:

```text
session_started_at
last_fullscreen_impression_at
last_rewarded_finished_at
completed_level_count
```

Условия:

- `completed_level_count >= 2`;
- session age >= 120 sec;
- elapsed since last fullscreen >= 120 sec;
- elapsed since rewarded >= 60 sec;
- только context `LEVEL_COMPLETE`;
- `canShow()` непосредственно перед show.

Если любое условие не выполнено — вернуть `SKIPPED` без задержки transition.

### Rewarded

Каждый request получает уникальный runtime `request_id`. Reward применяется один раз только при подтверждённом result `reward == true`.

### Preloader

Mobile-only до `gameStarted()`. Failure не блокирует boot.

### Failure paths

Adblock / no-fill / SDK error / unsupported → игра продолжает работу, без spinner soft-lock.

## 17. Pikabu boot/lifecycle

Порядок production build:

```text
load SDK
→ PkbSDK.init() exactly once
→ init PikabuPlatformAdapter
→ load game/save
→ optional supported mobile preloader
→ first interactive screen ready
→ sdk.gameStarted()
```

При скрытии вкладки / рекламе / platform UI добавлять соответствующую pause reason и глушить игровой звук.

## 18. Debug tools

Dev-only:

- load level by id;
- connector masks;
- show solution;
- auto-solve;
- validate all levels;
- reset save;
- simulate `canShow=false`;
- simulate rewarded success/fail;
- simulate fullscreen cooldown;
- local platform mode.

## 19. Prototype Definition of Done

- [ ] Godot 4.7.2 Compatibility project.
- [ ] 4×4 graybox board.
- [ ] straight/corner/T/cross через единую topology model.
- [ ] mouse + touch semantic action.
- [ ] source propagation.
- [ ] lamps on/off.
- [ ] win только моделью.
- [ ] restart.
- [ ] минимум 3 тестовых level JSON.
- [ ] Web export запускается локально.
- [ ] responsive smoke test.
- [ ] никаких production assets на этом этапе.

После prototype фиксируются реальные tile/UI slots, затем запускается visual vertical slice.
