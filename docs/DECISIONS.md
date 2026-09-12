# Project Decisions — Замкни цепь!

Фиксируются только решения, которые иначе пришлось бы обсуждать заново.

## ADR-001 — Project class A / Factory

**Date:** 2026-09-12  
**Status:** accepted

### Context

Механика простая, хорошо работает мышью и touch, не требует 3D/сложной физики.

### Decision

Держать v1 в классе `A — Factory`, ориентир 3–5 рабочих дней до release candidate без учёта внешней модерации и задержек дизайна.

### Consequences

Любая новая механика должна оправдывать увеличение scope.

---

## ADR-002 — Visualizations are not source of truth

**Date:** 2026-09-12  
**Status:** accepted

### Decision

Пользовательские изображения — только art direction/reference. Механика, layout, UI slots и production assets определяются GDD, graybox и manifest.

### Consequences

Нельзя нарезать concept sheets и считать их готовыми production assets. Нельзя переносить монеты/звёзды/режимы только потому, что они нарисованы.

---

## ADR-003 — Pipe geometry from one master system

**Date:** 2026-09-12  
**Status:** accepted

### Decision

Straight/corner/T/cross выводятся из одной master connector geometry. Порты строго в центрах сторон. Ориентации создаются поворотом на 90° в Godot.

### Consequences

Запрещено рисовать типы труб независимо. Pipe sprite не содержит tile background.

---

## ADR-004 — Layered tile rendering

**Date:** 2026-09-12  
**Status:** accepted

### Decision

Плитка собирается как `tile base + content + energy overlay + FX + interaction feedback`.

---

## ADR-005 — No mandatory coin economy in v1

**Date:** 2026-09-12  
**Status:** accepted

### Decision

В v1 нет обязательных монет, магазина и energy system. Есть hints, level progress и best moves.

### Consequences

Монетизация не требует искусственной валюты и строится на voluntary rewarded + natural-break fullscreen.

---

## ADR-006 — Deterministic level data with canonical solution

**Date:** 2026-09-12  
**Status:** accepted

### Decision

V1 levels хранятся в data files и содержат canonical solution orientation. Dev-only generator допустим; runtime procedural generation не обязателен.

---

## ADR-007 — Landscape only gameplay v1

**Date:** 2026-09-12  
**Status:** accepted

### Decision

Gameplay v1 проектируется landscape, base design size `1600×900`.

---

## ADR-008 — Pikabu is the only release platform for v1

**Date:** 2026-09-12  
**Status:** accepted

### Context

На текущем этапе задача — быстрее выпускать и проверять игры на Пикабу, не размазывая разработку по нескольким площадкам.

### Decision

Release target v1 — только **Пикабу Игры**. Локальный Web adapter остаётся для разработки. Yandex integration не реализуется сейчас и не входит в release QA.

### Consequences

- architecture всё равно остаётся platform-abstract;
- реализуются только `LocalPlatformAdapter` и `PikabuPlatformAdapter`;
- Pikabu cloud save, ads, lifecycle и promo requirements имеют приоритет;
- Yandex можно добавить отдельным milestone после проверки игры на Пикабу.

### Revisit when

После публикации/метрик Пикабу или отдельного решения о мультиплатформенном релизе.

---

## ADR-009 — Higher ad density, but only through Pikabu-safe natural gates

**Date:** 2026-09-12  
**Status:** accepted

### Context

Игра должна зарабатывать больше на рекламе, но нельзя ломать core loop и retention агрессивными прерываниями.

### Decision

Использовать три рекламных слоя:

1. mobile preloader до `gameStarted()` при поддержке;
2. rewarded: hint ×1, strong hint ×3, earn hint credit;
3. fullscreen opportunity после каждого завершённого уровня, но реальный показ только через AdsService gate.

Fullscreen gate v1:

- уровни 1–2 без fullscreen;
- первый показ не раньше 120 секунд сессии;
- минимум 120 секунд между фактическими fullscreen impressions;
- минимум 60 секунд после rewarded;
- только `result → next level`;
- `canShow()` непосредственно перед показом;
- no-fill/adblock/error никогда не блокируют переход.

### Consequences

Рекламных opportunity больше, чем в минимальной схеме, но gameplay не вызывает SDK напрямую и игрок не получает fullscreen в середине решения головоломки.

### Revisit when

После D+1/D+3/D+7 данных по session length, ads/player, show rate и revenue/player.
