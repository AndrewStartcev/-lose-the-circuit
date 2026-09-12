# GDD — Замкни цепь!

Living document. Изображения-референсы из постановки задачи задают art direction, но не являются точным UI/asset specification.

## 1. Product summary

- **Genre:** casual rotate-the-network / pipe puzzle.
- **Audience:** широкая браузерная аудитория, ориентир 6+/8+, без сложного текста.
- **Primary platform v1:** Пикабу Игры.
- **Development fallback:** локальный Web build без SDK.
- **Yandex:** вне текущего scope.
- **Orientation:** landscape.
- **Base design space:** 1600×900.
- **Unique hook:** приятная электрическая реакция сети — ток визуально распространяется после каждого поворота, а финальное зажигание всех ламп даёт сильный audiovisual payoff.

## 2. Core loop

```text
ПОВЕРНУТЬ ПЛИТКУ → ПЕРЕСЧИТАТЬ СЕТЬ → ПОКАЗАТЬ ТОК → ЗАЖЕЧЬ ЛАМПЫ → ЗАВЕРШИТЬ УРОВЕНЬ → СЛЕДУЮЩАЯ СХЕМА
```

Игрок поворачивает проводящие плитки. Генератор и лампы по умолчанию фиксированы. После каждого хода выполняется propagation от генератора по взаимно совместимым портам соседних клеток.

## 3. Rules

### Tile topology

- `straight` — 2 противоположных порта;
- `corner` — 2 соседних порта;
- `tee` — 3 порта;
- `cross` — 4 порта;
- `source` — источник питания с 1–4 портами согласно level data;
- `lamp` — конечный потребитель с одним портом;
- `empty` / `blocker` — непроводящая клетка.

Ориентация задаётся `0 / 1 / 2 / 3`, шаг = 90° по часовой стрелке.

### Connection rule

Соединение между соседями существует только если:

1. текущая клетка имеет порт в сторону соседа;
2. сосед имеет обратный порт;
3. обе клетки conductive.

### Win / success

Уровень завершён, когда **все lamp tiles находятся в energized component генератора**.

В v1 нет таймера, жизней и hard-fail. Игрок может перезапустить схему вручную.

### Scoring

- `moves_current` — количество поворотов;
- `best_moves` — лучший сохранённый результат уровня;
- на result screen показывать улучшение рекорда;
- звёзды/монеты не входят в обязательный v1 и не появляются только потому, что присутствуют на визуализациях.

### Difficulty

Сложность растёт через размер поля, количество ламп, junction tiles, плотность сети, правдоподобные неверные ориентации и длину ветвей. Искусственный таймер не используется.

## 4. Progression

### Short-term

Повернуть очередную плитку и увидеть, что energized segment стал длиннее.

### Session

Пройти последовательность уровней с постепенным вводом новых topology.

### Long-term v1

- открыть все 50 уровней;
- улучшать best moves;
- продолжать с последнего открытого уровня.

### Post-release candidates

Только по метрикам: daily puzzle, дополнительные visual themes, challenge packs, achievements/leaderboard.

## 5. Economy

Отдельная монетная экономика отсутствует.

| Resource | Earn | Spend | Persistent |
|---|---|---|---|
| Hints | стартовый запас / rewarded | исправление плиток | yes |
| Level progress | прохождение уровня | открывает следующий | yes |
| Best moves | хороший результат | не тратится | yes |

## 6. Content

### Level count

50 уровней.

### Difficulty bands

- **1–3:** tutorial, 4×4;
- **4–15:** 4×4 / 5×5, straight + corner + первые T;
- **16–35:** 5×5 / 6×5, несколько ламп, T/cross;
- **36–50:** 6×5 / 6×6, плотные схемы и длинные ветви.

### Storage

Уровни data-driven в `content/levels/*.json`. Каждый уровень хранит canonical solution state для validator и hint system.

## 7. Screens

### Boot/loading

Минимальный branded loading. На mobile Пикабу допускается platform preloader до `gameStarted()`, если поддерживается. Нельзя искусственно задерживать старт.

### Main menu

Главная CTA: `Играть` / `Продолжить`. Дополнительно: настройки, звук, level select после появления прогресса. Не перегружать валютами.

### Gameplay

Приоритеты:

1. игровое поле;
2. уровень / moves;
3. hint;
4. pause.

### Pause

- Продолжить;
- Перезапустить;
- Настройки;
- В меню.

### Result

- `Уровень пройден`;
- moves + best;
- `Дальше`;
- optional rewarded CTA с явно указанной наградой.

### Settings

- звук;
- музыка;
- вибрация только если реально используется;
- RU интерфейс по умолчанию.

## 8. Input

### Mouse

- ЛКМ = rotate clockwise.
- Hover — feedback only.

### Touch

- tap = rotate clockwise.
- один физический tap не должен дублироваться mouse emulation.

### Keyboard optional

- `Esc` = pause;
- `R` = restart после подтверждения или как desktop convenience.

## 9. Tutorial

Tutorial встроен в первые уровни.

### Level 1

Одна лампа, 2–3 поворачиваемые плитки, pulse/hand на первой плитке, текст `Нажми на плитку, чтобы повернуть`.

### Level 2

Показываем угол и передачу энергии.

### Level 3

Показываем T и две лампы.

Первые два уровня защищены от fullscreen-рекламы.

## 10. Ads — Pikabu-first monetization

Монетизация проектируется активнее, чем минимальная схема, но строго через естественные точки и централизованный AdsService.

### 10.1 Mobile preloader

- только mobile;
- только до `sdk.gameStarted()`;
- только если platform capability сообщает поддержку;
- может идти параллельно загрузке ресурсов;
- при ошибке/недоступности игра продолжает запуск.

### 10.2 Rewarded placements

#### A. Hint ×1

Игрок нажимает `Подсказка` → rewarded → при `reward == true` одна неверная плитка выставляется в canonical solution orientation.

#### B. Strong hint ×3

Отдельная добровольная CTA. После успешного rewarded корректируются до трёх неверных плиток. Не показывать автоматически после каждого неверного хода.

#### C. Earn hint credit

На result/menu, если запас hints низкий/нулевой, игрок может добровольно посмотреть rewarded и получить один hint credit для будущего уровня.

Общие правила:

- reward только при подтверждённом success/reward flag;
- idempotent apply_reward_once;
- no-fill/adblock не блокирует core progression;
- rewarded CTA disabled/hidden при недоступности;
- никакого misleading UI.

### 10.3 Fullscreen placements

Opportunity создаётся на каждом natural break после завершения уровня, но AdsService решает, можно ли реально показать рекламу.

Gate v1:

- уровни 1–2: fullscreen запрещён;
- первый fullscreen не раньше чем через 120 секунд после начала активной сессии;
- далее минимум 120 секунд между фактическими fullscreen impressions;
- только на переходе `result → next level`;
- после rewarded — suppress fullscreen минимум 60 секунд;
- при `canShow() == false` переход выполняется сразу;
- во время active puzzle/input/energy/win animation fullscreen запрещён;
- не запускать два ad request параллельно.

### 10.4 Pause/audio/input contract

Перед ad show:

- добавить pause reason `AD`;
- заблокировать gameplay input;
- остановить/приглушить audio.

После завершения:

- снять только `AD`;
- восстановить прежнее audio state;
- resume только если других pause reasons нет.

### 10.5 Analytics minimum

Логировать минимум:

- `ad_opportunity`;
- `ad_request`;
- `ad_available`;
- `ad_started`/`rendered`;
- `ad_closed`;
- `ad_reward_granted`;
- `ad_failed(reason)`.

Цель после релиза — оптимизировать revenue/player и session length, а не просто число запросов.

## 11. Saves

Для Пикабу cloud save входит в release scope.

### Schema scope

- schema version;
- unlocked level;
- completed levels;
- best moves per level;
- hint balance;
- tutorial flags;
- audio settings.

### Local events

Сразу после победы, изменения setting и расхода/получения hint.

### Cloud events

Debounced sync после значимого изменения через SaveService/PlatformService. Не синхронизировать на каждый поворот.

### Identity

Pikabu player id и auth changes обрабатываются platform layer. Gameplay ничего об этом не знает.

## 12. Audio

### Music states

- menu ambience;
- gameplay low-intensity industrial/electric ambience.

### Feedback SFX

- rotate click;
- connector snap;
- energy propagation;
- lamp on;
- level complete;
- UI click;
- rewarded/result feedback.

### Mute rules

Звук глушится при рекламе, скрытой вкладке и пользовательском mute.

## 13. Visual direction

- тёмная industrial/electrical мастерская;
- graphite / dark navy металл;
- тёплый amber/yellow energized state;
- холодный cyan/blue secondary UI;
- объёмные, чистые casual-friendly элементы;
- высокая читаемость topology;
- не pixel art;
- не фотореализм;
- без грязного хоррора/постапокалипсиса.

Главное качество визуала — **идеальная геометрия соединений**. Production details: `docs/DESIGNER_TZ.md` и `docs/ASSET_MANIFEST.md`.

## 14. Technical risks

1. Неровные pipe assets → единая master geometry и runtime rotation.
2. Concept лучше реальной игры → screenshot QA после первых 3–5 assets.
3. Мелкое поле на mobile landscape → max 6×6 + responsive board container.
4. Hint ambiguity → canonical solution.
5. Однообразный content → difficulty curve до производства всех уровней.
6. Ad lifecycle soft-lock → централизованный AdsService state machine + failure path.
7. Pikabu cloud identity/save → PlatformService/SaveService, без прямых SDK calls из gameplay.

## 15. Definition of v1

- [ ] Core loop menu → gameplay → result → next level.
- [ ] 50 валидированных уровней.
- [ ] Mouse + touch.
- [ ] Responsive landscape layout.
- [ ] Local save + Pikabu cloud save.
- [ ] Pikabu SDK init/lifecycle/gameStarted.
- [ ] Mobile preloader при поддержке.
- [ ] Rewarded: hint ×1, strong hint ×3, earn hint credit.
- [ ] Fullscreen на natural breaks через 120s gate.
- [ ] Adblock/no-fill path полностью играбелен.
- [ ] Audio + mute/pause lifecycle.
- [ ] Final integrated art соответствует approved art direction.
- [ ] Pikabu QA.
- [ ] Promo assets готовы после финального gameplay screenshot.
