# Project Decisions — Замкни цепь!

Фиксируются только решения, которые иначе пришлось бы обсуждать заново.

## ADR-001 — Project class A / Factory

**Date:** 2026-09-12  
**Status:** accepted

### Context

Механика простая, хорошо работает мышью и touch, не требует backend/3D/сложной физики.

### Decision

Держать v1 в классе `A — Factory`, ориентир 3–5 рабочих дней до release candidate без учёта внешней модерации и задержек дизайна.

### Consequences

Любая новая механика должна оправдывать увеличение scope. Магазин, главы, meta-progression и daily mode не входят автоматически.

### Revisit when

Если prototype показывает, что базовой прогрессии недостаточно для 30+ минут gameplay.

---

## ADR-002 — Visualizations are not source of truth

**Date:** 2026-09-12  
**Status:** accepted

### Context

Пользователь предоставил несколько качественных визуализаций меню/gameplay/UI/asset sheet, но отдельно указал, что это не истина.

### Decision

Использовать их как art direction reference: настроение, palette, material language, density, contrast.

Механика, layout, экономика, UI slots и production assets определяются GDD, graybox и manifest.

### Consequences

Нельзя нарезать concept sheets и считать это готовыми ассетами. Нельзя переносить монеты/звёзды/режимы только потому, что они есть на изображении.

### Revisit when

Никогда без явного решения пользователя.

---

## ADR-003 — Pipe geometry from one master system

**Date:** 2026-09-12  
**Status:** accepted

### Context

Для pipe puzzle любое несовпадение осей сразу разрушает качество и читаемость.

### Decision

Straight/corner/T/cross выводятся из одной master connector geometry. Порты строго в центрах сторон. Ориентации создаются поворотом кратно 90° в Godot.

### Consequences

Запрещено рисовать типы труб независимо друг от друга. Pipe sprite не содержит tile background.

### Revisit when

Только если gameplay topology полностью меняется.

---

## ADR-004 — Layered tile rendering

**Date:** 2026-09-12  
**Status:** accepted

### Context

Готовая картинка клетки для каждого типа/состояния создаёт много ассетов, усложняет поворот и повышает риск несовпадений.

### Decision

Плитка собирается как:

`tile base + content + energy overlay + FX + interaction feedback`.

### Consequences

Можно переиспользовать tile base, вращать только content и менять glow отдельно.

### Revisit when

Если vertical slice докажет, что shader/overlay подход визуально недостаточен.

---

## ADR-005 — No mandatory coin economy in v1

**Date:** 2026-09-12  
**Status:** accepted

### Context

Концепты показывают монеты и reward panels, но базовая головоломка не нуждается в экономике для core loop.

### Decision

В v1 нет обязательных монет, магазина и energy system. Есть только hints, level progress и best moves.

### Consequences

Меньше UI, save-state и балансовой работы. Monetization строится на добровольной подсказке и natural-break fullscreen.

### Revisit when

После D+7/D+30 метрик либо если retention явно требует meta progression.

---

## ADR-006 — Deterministic level data with canonical solution

**Date:** 2026-09-12  
**Status:** accepted

### Context

Для подсказок и QA нужен гарантированно валидный ответ, а runtime procedural generation добавляет риск.

### Decision

V1 levels хранятся в data files и содержат canonical solution orientation. Dev-only generator допустим, runtime generator не обязателен.

### Consequences

Hint прост, уровни воспроизводимы, validator может проверять весь content автоматически.

### Revisit when

После релиза, если нужен endless/daily generation.

---

## ADR-007 — Landscape only gameplay v1

**Date:** 2026-09-12  
**Status:** accepted

### Context

Grid puzzle и desktop/Pikabu лучше используют горизонтальный экран. Одновременная production-поддержка portrait увеличит стоимость UI и backgrounds.

### Decision

Gameplay v1 проектируется landscape, base design size `1600×900`.

### Consequences

Mobile portrait не получает отдельный gameplay layout; при необходимости используется rotate-device overlay.

### Revisit when

Если платформенная статистика после релиза показывает существенную потерю аудитории из-за ориентации.
