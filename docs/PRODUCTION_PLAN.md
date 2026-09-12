# Production Plan — Замкни цепь!

## Принцип

Идём по протоколу и не пытаемся одновременно писать всю игру и делать весь арт. Главный release target v1 — **Пикабу Игры**. Локальный Web build обязателен для разработки. Yandex сейчас не реализуем и не тестируем.

Главный визуальный риск проекта — повтор ошибки «красивый concept sheet → кривые production assets → слабая интеграция», поэтому работа идёт через короткие gates.

## Milestone 0 — Pre-production

### Готово

- GAME_BRIEF;
- GDD;
- technical plan;
- designer brief;
- decisions;
- initial asset manifest;
- project state;
- Pikabu-first monetization rules.

### Gate

- [x] идея получила `GO`;
- [x] v1 scope ограничен;
- [x] визуальные изображения — references, не specification;
- [x] Пикабу зафиксирован как единственная release platform v1;
- [x] реклама спроектирована через rewarded + natural-break fullscreen + mobile preloader.

---

## Milestone 1 — Core loop prototype

### Цель

Полностью рабочая graybox puzzle без production-графики и без SDK.

### Реализовать

1. Godot 4.7.2 Compatibility project.
2. Main + gameplay scene.
3. Grid 4×4 / 5×5.
4. Tile model + 90° rotation.
5. straight/corner/T/cross/source/lamp.
6. BFS/DFS propagation.
7. Lamp on/off.
8. Win condition.
9. moves counter.
10. restart.
11. mouse + touch semantic input.
12. 3–5 level JSON.
13. local Web export.

### Арт

Только graybox: простые формы/линии/цвета. Никакого production asset pack.

### Gate

- механика понятна;
- соединения однозначны;
- local Web build работает;
- desktop + mobile landscape input проверен;
- core loop хочется повторить.

**Результат:** `GO / PIVOT / KILL`.

---

## Milestone 2 — UX graybox + slot freeze

Собрать Main menu, Gameplay, Pause, Result, Settings и при необходимости Level select.

Зафиксировать:

- board rect на 1600×900;
- tile display size;
- HUD/button/panel rectangles;
- safe areas;
- background crop rules;
- dynamic/baked text.

После этого `ASSET_MANIFEST.md` получает реальные размеры.

### Gate

Graybox корректен на 1280×720, 1366×768, 1600×900, 1920×1080, narrow landscape iframe и mobile landscape.

---

## Milestone 3 — Visual vertical slice

Сначала только 3–5 ключевых assets:

1. tile base;
2. straight/corner family из одной master geometry;
3. generator;
4. lamp off/on;
5. один UI family/background fragment.

После интеграции делаем реальные screenshots и сравниваем с art direction. Если игра хуже reference — массовый арт не запускаем.

---

## Milestone 4 — Architecture freeze

Зафиксировать:

- board/data contracts;
- scene tree;
- input;
- SaveService;
- AdsService;
- PlatformService;
- AudioService;
- level schema + validator;
- responsive rules.

Adapters v1: `LocalPlatformAdapter` и `PikabuPlatformAdapter`.

---

## Milestone 5 — Full asset production

Только после approved vertical slice.

Gameplay: pipe family, energy overlays, generator, lamp states, blockers, hover/press, energy/win FX.

UI: scalable panels, buttons/states, icons, logo, loading visuals, menu/gameplay environment.

Promo assets — только после финальной интеграции.

---

## Milestone 6 — Content production

- 1–3 tutorial;
- 4–15 easy;
- 16–35 medium;
- 36–50 hard.

Все 50 уровней проходят validator и имеют canonical solution.

---

## Milestone 7 — Progression, save, audio

- unlocked level;
- completed levels;
- best moves;
- hint balance;
- settings;
- local-first save;
- rotate/energy/lamp/win/UI audio;
- lifecycle mute.

Gate: reload не теряет прогресс.

---

## Milestone 8 — Pikabu integration + monetization

### Platform

- load Pikabu SDK;
- `PkbSDK.init()` один раз;
- player/auth lifecycle;
- `gameStarted()` только когда первый interactive screen готов;
- cloud save;
- pause/tab lifecycle;
- local fallback остаётся рабочим.

### Ads

#### Mobile preloader

- до `gameStarted()`;
- только mobile/при поддержке;
- failure не блокирует boot.

#### Rewarded

1. hint ×1;
2. strong hint ×3;
3. earn hint credit.

#### Fullscreen

Opportunity после каждого completed level, но фактический показ только через AdsService gate:

- первые 2 уровня — без fullscreen;
- first show не раньше 120 sec session age;
- минимум 120 sec между фактическими impressions;
- минимум 60 sec после rewarded;
- только transition `result → next level`;
- `canShow()` непосредственно перед show;
- no-fill/adblock → мгновенно продолжить без рекламы.

### Gate

- no SDK local build работает;
- no-fill/adblock не ломает игру;
- close/error возвращает корректное состояние;
- награда не выдаётся без success;
- двойной ad request невозможен;
- звук/input корректно восстанавливаются;
- cloud save проходит smoke test.

---

## Milestone 9 — Pikabu QA / release candidate

- desktop Chromium;
- mobile Chromium;
- Safari/iOS при доступности;
- resize/narrow iframe;
- mobile landscape;
- tab hide/show;
- reload/local+cloud save;
- ad unavailable/adblock;
- rewarded success/fail;
- fullscreen cooldown;
- repeated transitions;
- audio lifecycle;
- console/network errors;
- load time/build size;
- Pikabu test/staging.

После RC — никаких новых фич.

---

## Milestone 10 — Pikabu promo/release package

После финального gameplay:

- квадратная promo от 1024×1024;
- горизонтальная promo от 1920×1080 16:9;
- screenshots;
- short/full description;
- controls/how to play;
- age/content fields;
- release report.

---

# Общий ориентир

Factory target: примерно 3–5 рабочих дней к release candidate при отсутствии неожиданных проблем и задержек art iteration.

## Что не параллелим

- full asset pack до slot freeze;
- независимое рисование topology труб;
- лишнюю экономику/магазин;
- Yandex integration;
- promo screenshots до финального gameplay.

## Что можно параллелить после slot freeze

- production art;
- level data;
- audio sourcing;
- Pikabu adapter preparation.

Integration screenshot первых assets остаётся обязательным gate.
