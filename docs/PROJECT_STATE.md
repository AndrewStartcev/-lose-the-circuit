# Project State

**Updated:** 2026-09-12 10:58 +08:00  
**Protocol:** 1.0.0  
**Godot:** 4.7.2-stable  
**Game version:** 0.1.0-preproduction  
**Current milestone:** Milestone 0 — Pre-production → next: Core loop prototype  
**Last verified commit:** not yet verified as a runnable build  
**Last verified build:** none

## Working now

Зафиксировать scope, gameplay contract, designer brief и asset pipeline перед созданием Godot prototype.

## Done

- Репозиторий инициализирован.
- Протоколы v1.0.0 подключены как source of truth.
- Игра классифицирована как `A — Factory`.
- Production Score: `54/60`, решение `GO`.
- Зафиксирован core loop: rotate tile → propagate energy → light all lamps → next level.
- Зафиксирован landscape target `1600×900`.
- Зафиксировано: пользовательские изображения — art-direction references, не точная спецификация.
- Описана data-driven level architecture.
- Описан pipe geometry contract.
- Подготовлено ТЗ дизайнеру.
- Подготовлен initial ASSET_MANIFEST со slot-freeze gate.
- Монетная экономика исключена из обязательного v1.

## Known issues / blockers

- Реальные размеры gameplay/UI slots пока неизвестны — до graybox production asset pack заблокирован.
- Нет Godot project и playable prototype.
- Не подтверждён пользователем окончательный v1 scope после первичной документации.

## Current decisions

- 50 уровней, размер до 6×6.
- Генератор и лампы фиксированы; трубы вращаются на 90°.
- Victory = все lamps energized.
- Нет таймера/жизней/hard fail в v1.
- Hint использует canonical solution.
- Pipes строятся слоями: tile base + pipe base + energy overlay + FX.
- Все topology происходят из одной master connector geometry.
- Rewarded = добровольная подсказка.
- Fullscreen = natural break после уровня с cooldown.

## Next actions

1. Получить подтверждение/правки по GAME_BRIEF/GDD от пользователя.
2. Создать Godot 4.7.2 Compatibility project по `docs/TECHNICAL_PLAN.md`.
3. Реализовать graybox 4×4 и 3–5 тестовых уровней.
4. Сделать первый Web export и проверить mouse/touch.
5. Собрать graybox основных экранов и зафиксировать реальные UI/tile slots.
6. Обновить `ASSET_MANIFEST.md` точными размерами.
7. Передать дизайнеру только test production pack из 3–5 ключевых assets.

Не хранить здесь длинную историю. Долгие решения → `DECISIONS.md`, история изменений → Git.
