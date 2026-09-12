# Project State

**Updated:** 2026-09-12  
**Protocol:** 1.0.0  
**Godot:** 4.7.2-stable  
**Game version:** 0.1.0-preproduction  
**Current milestone:** Milestone 1 — Core loop prototype  
**Last verified commit:** docs/pre-production only  
**Last verified build:** none

## Working now

Передать Codex задачу на создание первого Godot graybox prototype без production-графики и без платформенного SDK.

## Done

- Репозиторий инициализирован.
- Протоколы v1.0.0 подключены как source of truth.
- Игра классифицирована как `A — Factory`.
- Production Score: `54/60`, решение `GO`.
- Core loop: rotate tile → propagate energy → light all lamps → next level.
- Landscape target `1600×900`.
- Пользовательские изображения зафиксированы как art-direction references, не точная спецификация.
- Описана data-driven level architecture.
- Описан pipe geometry contract и layered rendering.
- Подготовлены designer TZ и initial ASSET_MANIFEST.
- Release target v1 изменён на **Пикабу Игры only**.
- Yandex исключён из текущего scope.
- Pikabu monetization расширена: mobile preloader + 3 rewarded сценария + fullscreen natural-break gate.
- Fullscreen: первые 2 уровня без рекламы, first show >=120 sec session age, >=120 sec между impressions, >=60 sec после rewarded.

## Known issues / blockers

- Godot project и playable prototype ещё не созданы.
- Реальные размеры gameplay/UI slots неизвестны — production asset pack заблокирован до graybox slot freeze.
- Pikabu SDK/cloud save не интегрируются до подтверждения core loop/architecture; это более поздний milestone.

## Current decisions

- Primary release platform v1: Pikabu Games.
- Local Web fallback обязателен.
- 50 уровней, размер до 6×6.
- Генератор и лампы фиксированы; трубы вращаются на 90°.
- Victory = все lamps energized.
- Нет таймера/жизней/hard fail.
- Hint использует canonical solution.
- Pipes: tile base + content + energy overlay + FX.
- Topology из одной master connector geometry.
- Нет обязательной coin economy.
- Rewarded: hint ×1 / strong hint ×3 / earn hint credit.
- Fullscreen только `result → next level` через centralized AdsService gate.

## Next actions

1. Codex создаёт Milestone 1 graybox prototype по `docs/CODEX_TASK_01_PROTOTYPE.md`.
2. Запустить проект в Godot 4.7.2 и проверить core loop.
3. Сделать локальный Web export.
4. Проверить desktop mouse + mobile/touch semantics.
5. После подтверждения перейти к UX graybox и slot freeze.
6. Обновить `ASSET_MANIFEST.md` реальными размерами.
7. Только затем делать 3–5 test production assets и visual vertical slice.

Не хранить здесь длинную историю. Долгие решения → `DECISIONS.md`, история изменений → Git.
