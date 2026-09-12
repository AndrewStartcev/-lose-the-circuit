# Project State

**Updated:** 2026-09-12 11:10 +08:00  
**Protocol:** 1.0.0  
**Godot:** 4.7.2-stable  
**Game version:** 0.1.0-preproduction  
**Current milestone:** Pre-production; parallel visual Batch 01  
**Last verified commit:** not yet verified as a runnable build  
**Last verified build:** none

## Working now

Два независимых трека:

1. gameplay prototype будет реализован отдельно;
2. Codex в текущей постановке используется **только для создания визуальных ассетов**, без изменения кода/сцен/Godot-настроек.

Текущая задача Codex: `docs/CODEX_TASK_01_ASSETS.md`.

## Done

- Репозиторий инициализирован.
- Протоколы v1.0.0 подключены как source of truth.
- Игра классифицирована как `A — Factory`.
- Production Score: `54/60`, решение `GO`.
- Зафиксирован core loop: rotate tile → propagate energy → light all lamps → next level.
- Release target v1: **Пикабу Игры**; Yandex исключён из текущего scope.
- Зафиксирован landscape target `1600×900`.
- Зафиксировано: пользовательские изображения — art-direction references, не точная спецификация.
- Описана data-driven level architecture.
- Описан pipe geometry contract.
- Подготовлено ТЗ дизайнеру.
- Подготовлен initial ASSET_MANIFEST.
- Подготовлено отдельное asset-only ТЗ Codex: `docs/CODEX_TASK_01_ASSETS.md`.
- Неверное ТЗ Codex на разработку prototype удалено.
- Монетная экономика исключена из обязательного v1.
- Рекламная стратегия ориентирована на Пикабу: mobile preloader при доступности, несколько добровольных rewarded-сценариев и fullscreen только на natural breaks через cooldown/gate.

## Known issues / blockers

- Реальные финальные размеры gameplay/UI slots будут уточнены после graybox; поэтому Batch 01 является test/art-direction pack, а не разрешением на массовое производство всего UI.
- Нет Godot project и playable prototype.
- Не утверждён Batch 01 после визуального review.

## Current decisions

- 50 уровней, размер до 6×6.
- Генератор и лампы фиксированы логикой уровня; трубы вращаются на 90°.
- Victory = все lamps energized.
- Нет таймера/жизней/hard fail в v1.
- Hint использует canonical solution.
- Pipes строятся слоями: tile base + pipe base + energy overlay + FX.
- Все topology происходят из одной master connector geometry.
- Codex текущего этапа = asset designer only, не разработчик.
- Rewarded = добровольные подсказки/дополнительный hint supply.
- Fullscreen = natural break после уровня с platform-safe cooldown/gates.

## Next actions

1. Передать Codex текущую папку репозитория и prompt на выполнение `docs/CODEX_TASK_01_ASSETS.md`.
2. Получить Batch 01 и `docs/ASSETS_REPORT.md`.
3. Проверить геометрию straight/corner/tee/cross/generator/lamp по preview-grid.
4. Утвердить или вернуть Batch 01 на точечные исправления.
5. Отдельным треком собрать gameplay graybox и зафиксировать реальные UI/tile slots.
6. После slot freeze обновить `ASSET_MANIFEST.md` точными размерами.
7. Только после этого запускать массовый production asset pack.

Не хранить здесь длинную историю. Долгие объяснения → `DECISIONS.md`, история изменений → Git.
