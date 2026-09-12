# Asset Manifest — Замкни цепь!

**Art direction/reference:** user-provided industrial/electric visualizations + `docs/DESIGNER_TZ.md`  
**Design resolution:** `1600×900`  
**Last approved integration screenshot:** none yet  
**Status:** `DRAFT — real slot sizes are NOT frozen`

## Важно

Этот manifest сейчас фиксирует **структуру и контракты**, но не разрешает массовую генерацию production assets.

До graybox значения `display_rect` и `source_size` остаются `TBD`.

После Milestone 2 разработчик обязан заменить `TBD` реальными размерами и только затем запускать test production pack.

## Общие правила

- Один production asset = одна запись.
- Не генерировать весь pack, пока первые 3–5 ключевых элементов не проверены в Godot.
- Изменяемый текст не запекается.
- Pipe content — transparent, без tile background.
- Все pipe topology наследуют одну master geometry.
- Все connector endpoints строго по центру сторон tile.
- Energized variant не меняет geometry.

---

## Gameplay — first test pack

### `tile_base`

- **Path:** `assets/gameplay/tiles/tile_base.webp`
- **Purpose:** базовая квадратная plate под любую gameplay cell.
- **Display rect:** `TBD after graybox`
- **Source size:** `TBD after graybox`
- **Aspect ratio:** `1:1`
- **Format:** WebP/PNG lossless
- **Alpha:** optional; prefer no if full square is opaque
- **Stretch:** none
- **9-patch margins:** n/a
- **Safe area:** content center + connector zones must remain visually clear
- **Baked text:** no
- **States/variants:** base only; hover/pressed preferably overlays/styles
- **Reference:** `docs/DESIGNER_TZ.md`
- **Status:** TODO / SLOT NOT FROZEN
- **Notes:** no pipes, bulbs or generator baked into base.

### `pipe_straight_base`

- **Path:** `assets/gameplay/pipes/pipe_straight_base.webp`
- **Purpose:** inactive straight conductor overlay.
- **Display rect:** `TBD; square content rect equal to tile`
- **Source size:** `TBD`
- **Aspect ratio:** `1:1 canvas`
- **Format:** WebP/PNG lossless
- **Alpha:** yes
- **Stretch:** none
- **9-patch margins:** n/a
- **Safe area:** endpoints exactly at TOP/BOTTOM center in canonical rotation
- **Baked text:** no
- **States/variants:** canonical orientation only; rotations done in Godot
- **Reference:** master pipe geometry
- **Status:** TODO / SLOT NOT FROZEN
- **Notes:** transparent background, no plate.

### `pipe_corner_base`

- **Path:** `assets/gameplay/pipes/pipe_corner_base.webp`
- **Purpose:** inactive 90° conductor overlay.
- **Display rect:** `TBD; square content rect equal to tile`
- **Source size:** `TBD`
- **Aspect ratio:** `1:1 canvas`
- **Format:** WebP/PNG lossless
- **Alpha:** yes
- **Stretch:** none
- **Safe area:** endpoints exactly at TOP/RIGHT center in canonical rotation
- **Baked text:** no
- **States/variants:** canonical orientation only; rotations in Godot
- **Reference:** same master geometry as straight
- **Status:** TODO / SLOT NOT FROZEN
- **Notes:** pipe diameter and port profile identical to straight.

### `pipe_straight_energy`

- **Path:** `assets/gameplay/pipes/pipe_straight_energy.webp`
- **Purpose:** amber energized overlay over straight conductor.
- **Display rect:** exactly same as `pipe_straight_base`
- **Source size:** exactly same canvas as base
- **Aspect ratio:** `1:1 canvas`
- **Format:** PNG/WebP lossless
- **Alpha:** yes
- **Stretch:** none
- **Baked text:** no
- **States/variants:** energized overlay
- **Status:** TODO / SLOT NOT FROZEN
- **Notes:** geometry must not shift; only light/emission changes.

### `pipe_corner_energy`

- **Path:** `assets/gameplay/pipes/pipe_corner_energy.webp`
- **Purpose:** amber energized overlay over corner conductor.
- **Display rect:** same as base
- **Source size:** same as base
- **Aspect ratio:** `1:1 canvas`
- **Format:** PNG/WebP lossless
- **Alpha:** yes
- **Stretch:** none
- **Baked text:** no
- **States/variants:** energized overlay
- **Status:** TODO / SLOT NOT FROZEN
- **Notes:** derived from same energy style as straight.

### `generator_base`

- **Path:** `assets/gameplay/objects/generator_base.webp`
- **Purpose:** source object inactive/base state.
- **Display rect:** `TBD`
- **Source size:** `TBD`
- **Aspect ratio:** `1:1 canvas`
- **Format:** PNG/WebP lossless
- **Alpha:** yes
- **Stretch:** none
- **Baked text:** no
- **States/variants:** base
- **Status:** TODO / SLOT NOT FROZEN
- **Notes:** connector contract must match pipe ports exactly.

### `generator_energy`

- **Path:** `assets/gameplay/objects/generator_energy.webp`
- **Purpose:** active energy overlay/state.
- **Display rect:** same as generator base
- **Source size:** same canvas as generator base
- **Format:** PNG/WebP lossless
- **Alpha:** yes
- **Stretch:** none
- **Baked text:** no
- **States/variants:** energized
- **Status:** TODO / SLOT NOT FROZEN

### `lamp_off`

- **Path:** `assets/gameplay/objects/lamp_off.webp`
- **Purpose:** unpowered lamp.
- **Display rect:** `TBD`
- **Source size:** `TBD`
- **Aspect ratio:** `1:1 canvas`
- **Format:** PNG/WebP lossless
- **Alpha:** yes
- **Stretch:** none
- **Baked text:** no
- **States/variants:** off
- **Status:** TODO / SLOT NOT FROZEN
- **Notes:** endpoint aligns to master connector system.

### `lamp_on`

- **Path:** `assets/gameplay/objects/lamp_on.webp`
- **Purpose:** powered lamp.
- **Display rect:** exactly same as `lamp_off`
- **Source size:** exactly same canvas as `lamp_off`
- **Format:** PNG/WebP lossless
- **Alpha:** yes
- **Stretch:** none
- **Baked text:** no
- **States/variants:** on
- **Status:** TODO / SLOT NOT FROZEN
- **Notes:** same geometry; warm glow only.

---

## Gameplay — full pack after vertical slice approval

### `pipe_tee_base` / `pipe_tee_energy`

- **Path:** `assets/gameplay/pipes/pipe_tee_*.webp`
- **Display/source:** TBD
- **Alpha:** yes
- **Rotation:** runtime
- **Status:** BLOCKED UNTIL TEST PACK APPROVED

### `pipe_cross_base` / `pipe_cross_energy`

- **Path:** `assets/gameplay/pipes/pipe_cross_*.webp`
- **Display/source:** TBD
- **Alpha:** yes
- **Rotation:** cross visual does not need orientation, but uses same canvas contract
- **Status:** BLOCKED UNTIL TEST PACK APPROVED

### `blocker`

- **Path:** `assets/gameplay/tiles/tile_blocker.webp`
- **Display/source:** TBD
- **Alpha:** depends on layering
- **Status:** BLOCKED UNTIL TEST PACK APPROVED

### `tile_hint_highlight`

- **Path:** `assets/gameplay/overlays/tile_hint_highlight.webp`
- **Purpose:** temporary hint/attention overlay.
- **Display/source:** TBD
- **Alpha:** yes
- **Status:** BLOCKED UNTIL UX SLOT FREEZE

---

## FX

### `fx_electric_pulse`

- **Path:** `assets/fx/fx_electric_pulse.webp` or sprite sequence
- **Purpose:** short pulse along energized path.
- **Display rect:** TBD
- **Alpha:** yes
- **Baked text:** no
- **Status:** BLOCKED UNTIL VERTICAL SLICE

### `fx_lamp_ignite`

- **Path:** `assets/fx/fx_lamp_ignite.webp`
- **Purpose:** lamp ignition flash.
- **Display rect:** TBD
- **Alpha:** yes
- **Status:** BLOCKED UNTIL VERTICAL SLICE

### `fx_level_complete`

- **Path:** `assets/fx/fx_level_complete.webp` / particles assembled in Godot
- **Purpose:** compact completion celebration.
- **Status:** BLOCKED UNTIL VERTICAL SLICE

---

## UI

Точные UI assets добавляются после graybox. Предпочтение — Godot StyleBox/9-patch + dynamic labels/icons.

Планируемые families:

- `panel_primary`
- `panel_modal`
- `btn_primary_frame`
- `btn_secondary_frame`
- `icon_hint`
- `icon_pause`
- `icon_settings`
- `icon_sound_on/off`
- `icon_restart`
- `icon_home`
- `logo_close_the_circuit`

Все dynamic labels создаются в Godot.

---

## Backgrounds

### `bg_workshop`

- **Path:** `assets/backgrounds/bg_workshop.webp`
- **Purpose:** decorative industrial-electric environment around gameplay/menu.
- **Display rect:** viewport cover
- **Source size:** TBD after safe composition test
- **Aspect ratio:** landscape master; likely 16:9 with safe crop
- **Format:** WebP
- **Alpha:** no
- **Stretch:** cover with approved crop
- **Safe area:** TBD after graybox
- **Baked text:** decorative only if explicitly approved
- **Status:** BLOCKED UNTIL GRAYBOX SAFE ZONES

---

## Promo assets

Не создавать до финального gameplay integration.

Планируемые:

- platform icon;
- horizontal cover;
- screenshots.

Promo должен показывать фактическую игру, а не concept-only UI.

---

## Integration QA

| Asset | 16:9 | Narrow | Mobile landscape | Visual match | Status |
|---|---|---|---|---|---|
| tile_base | — | — | — | — | WAITING FOR GRAYBOX |
| pipe_straight | — | — | — | — | WAITING FOR GRAYBOX |
| pipe_corner | — | — | — | — | WAITING FOR GRAYBOX |
| generator | — | — | — | — | WAITING FOR GRAYBOX |
| lamp | — | — | — | — | WAITING FOR GRAYBOX |
