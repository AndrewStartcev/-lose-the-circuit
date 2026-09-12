# Production Plan — Замкни цепь!

## Принцип

Идём по протоколу и не пытаемся одновременно писать всю игру и делать весь арт. Главный риск этого проекта — не код, а повтор ошибки «красивый concept sheet → кривые production assets → слабая интеграция».

Поэтому работа идёт через короткие gates.

## Milestone 0 — Pre-production

### Уже делаем

- GAME_BRIEF;
- GDD;
- technical plan;
- designer brief;
- decisions;
- initial asset manifest;
- project state.

### Gate

- [x] идея получила `GO` по Production Score;
- [x] v1 scope ограничен;
- [x] визуальные изображения зафиксированы как references, не specification;
- [ ] пользователь подтверждает базовую механику и scope либо сообщает правки.

---

## Milestone 1 — Core loop prototype

### Цель

Сделать полностью рабочую серую версию puzzle без production-графики.

### Что реализовать

1. Godot 4.7.2 Compatibility project.
2. Main scene и gameplay scene.
3. Grid 4×4 / 5×5.
4. Tile model и 90° rotation.
5. Типы straight/corner/T/cross/source/lamp.
6. BFS/DFS propagation от source.
7. Lamp on/off state.
8. Win condition.
9. moves counter.
10. restart.
11. mouse + touch semantic input.
12. 3–5 level JSON.
13. Web export.

### Арт

Только graybox: простые цвета/линии/иконки. Никаких массовых красивых ассетов.

### Gate

- игрок понимает механику без объяснения;
- соединения работают без двусмысленности;
- первый Web build работает;
- desktop + mobile landscape input проверен;
- core loop хочется повторить несколько раз.

**Результат:** `GO / PIVOT / KILL`.

Ориентир: 0.5–1 рабочий день.

---

## Milestone 2 — UX graybox + slot freeze

### Цель

Получить реальную композицию игры, на основе которой дизайнер делает production assets.

### Экраны

- Main menu;
- Gameplay;
- Pause;
- Result;
- Settings;
- минимальный Level select, если нужен на этом этапе.

### Фиксируем

- board rect на 1600×900;
- tile display size для каждой целевой board size;
- HUD rectangles;
- button rectangles;
- panel minimum sizes;
- safe areas;
- background crop rules;
- динамический/запечённый текст.

После этого обновляется `ASSET_MANIFEST.md`: `TBD` заменяется точными display/source contracts.

### Gate

Graybox корректно выглядит минимум на:

- 1280×720;
- 1366×768;
- 1600×900;
- 1920×1080;
- mobile landscape;
- narrow landscape iframe.

Ориентир: 0.5 рабочего дня.

---

## Milestone 3 — Visual vertical slice

### Цель

Сделать **один настоящий игровой экран**, который реально выглядит на уровне утверждённого art direction.

### Сначала только 3–5 ключевых assets

1. tile base;
2. straight/corner pipe family из одной master geometry;
3. generator;
4. lamp off/on;
5. один UI button/panel family или background frame.

### Проверка

- импорт в Godot;
- реальный screenshot 1600×900;
- screenshot 1366×768;
- mobile landscape screenshot;
- сравнение с art direction.

### Gate

Если реальная игра выглядит хуже reference — **не генерируем остальной пак**, а правим pipeline/геометрию/материалы.

Ориентир: 0.5–1 день вместе с итерацией дизайна.

---

## Milestone 4 — Architecture freeze

### Закрыть до массового content

- board/data contracts;
- scene tree;
- input;
- SaveService;
- AdsService interface;
- PlatformService interface;
- AudioService;
- level schema;
- validator;
- responsive rules.

### Gate

Крупные архитектурные изменения после этого — только при blocker.

Ориентир: 0.25–0.5 дня.

---

## Milestone 5 — Full asset production

Только после approved vertical slice.

### Gameplay

- pipe topology family;
- energized overlays;
- generator;
- lamp states;
- blockers/empty;
- selection/hover/press;
- energy FX;
- win FX.

### UI

- scalable panels;
- buttons + states;
- icons;
- logo;
- loading visuals;
- menu/gameplay environment background.

### Promo

**Не сейчас.** Icon/cover/screenshots делаются после финальной интеграции, чтобы реклама игры соответствовала реальной игре.

Ориентир: зависит от дизайнера, но код не блокируется — gameplay уже работает на graybox.

---

## Milestone 6 — Content production

### Level plan

- 1–3 tutorial;
- 4–15 easy;
- 16–35 medium;
- 36–50 hard.

### Инструменты

- validator;
- auto-solve against canonical solution;
- batch level check;
- optional dev generator.

### Gate

Все 50 уровней:

- валидны;
- не стартуют solved;
- имеют хотя бы одну lamp;
- имеют корректную canonical solution;
- проходят автоматически validator.

Ориентир: 0.5–1 день при генераторе/шаблонах.

---

## Milestone 7 — Progression, save, audio

- unlocked level;
- best moves;
- hint balance;
- settings;
- local-first save;
- cloud sync adapter;
- rotate/energy/lamp/win/UI audio;
- lifecycle mute.

Gate: reload не теряет прогресс.

Ориентир: 0.5 дня.

---

## Milestone 8 — Platforms + monetization

### Platform abstraction

- local fallback;
- Yandex adapter;
- Pikabu adapter.

### Rewarded

- hint ×1;
- optional hint ×3.

### Fullscreen

- natural break после уровня;
- configurable cooldown;
- корректное pause/audio/input restore.

### Gate

- no SDK → локальная игра работает;
- no-fill → игра работает;
- ad close → возвращает корректное состояние;
- награда не выдаётся без success callback.

Ориентир: 0.5–1 день.

---

## Milestone 9 — QA / release candidate

- browser smoke;
- resize;
- mobile landscape;
- tab hide/show;
- reload/save;
- ad unavailable;
- repeated ads;
- audio lifecycle;
- console errors;
- load time/build size;
- Yandex draft;
- Pikabu test.

После RC никаких новых фич.

Ориентир: 0.5 дня + время модераций.

---

## Milestone 10 — Store/promo package

После финального gameplay:

- icon;
- horizontal cover;
- screenshots;
- short description;
- full description;
- controls/how to play;
- age/content fields;
- release report.

---

# Общий ориентир

При отсутствии неожиданных проблем это проект класса A:

- **код + content + интеграции:** примерно 3–5 рабочих дней;
- **финальный срок зависит в основном от скорости согласования и качества asset iteration**, а не от сложности gameplay.

## Что не параллелим

Нельзя одновременно:

- делать весь asset pack и ещё менять layout;
- рисовать трубы независимо друг от друга;
- добавлять валюту/магазин до подтверждения необходимости;
- генерировать promo screenshots до финального gameplay.

## Что можно параллелить

После slot freeze:

- production art;
- level data;
- audio sourcing;
- platform adapter preparation.

Но integration screenshot первых assets остаётся обязательным gate перед массовым арт-паком.
