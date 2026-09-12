# GDD — Замкни цепь!

Короткий living document. Изображения-референсы из постановки задачи задают направление, но не являются точным UI/asset specification.

## 1. Product summary

- **Genre:** casual rotate-the-network / pipe puzzle.
- **Audience:** широкая браузерная аудитория, ориентир 6+/8+, без сложного текста.
- **Platforms:** Яндекс Игры, Пикабу Игры, локальный Web build.
- **Orientation:** landscape.
- **Base design space:** 1600×900.
- **Unique hook:** приятная электрическая реакция сети — ток визуально распространяется после каждого поворота, а финальное зажигание всех ламп даёт сильный audiovisual payoff.

## 2. Core loop

```text
ПОВЕРНУТЬ ПЛИТКУ → ПЕРЕСЧИТАТЬ СЕТЬ → ПОКАЗАТЬ ТОК → ЗАЖЕЧЬ ЛАМПЫ → ЗАВЕРШИТЬ УРОВЕНЬ → СЛЕДУЮЩАЯ СХЕМА
```

Игрок поворачивает только проводящие плитки. Генератор и лампы по умолчанию фиксированы. После каждого хода выполняется propagation от генератора по взаимно совместимым портам соседних клеток.

## 3. Rules

### Tile topology

Базовые conductive topology:

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
3. обе клетки являются conductive.

### Win / success

Уровень завершён, когда **все lamp tiles находятся в energized component генератора**.

В v1 нет таймера, жизней и hard-fail. Игрок может перезапустить схему вручную.

### Scoring

- `moves_current` — количество поворотов.
- `best_moves` — лучший сохранённый результат уровня.
- На result screen показывать улучшение рекорда, если оно есть.
- Звёзды/монеты не входят в обязательный v1 и не должны появляться только потому, что были на визуализациях.

### Difficulty

Сложность растёт через:

- размер поля;
- количество ламп;
- число junction tiles;
- плотность сети;
- количество визуально правдоподобных неверных ориентаций;
- длину пути от source до lamps.

Не использовать искусственный дефицит времени.

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

Только по метрикам:

- daily puzzle;
- дополнительные visual themes;
- challenge packs;
- achievements/leaderboard.

## 5. Economy

В базовом v1 отдельная монетная экономика отсутствует.

| Resource | Earn | Spend | Persistent |
|---|---|---|---|
| Hints | стартовый небольшой запас / rewarded | исправление одной плитки | yes |
| Level progress | прохождение уровня | открывает следующий | yes |
| Best moves | хороший результат | не тратится | yes |

Если экономика монет будет добавлена позже, это отдельное продуктово-аналитическое решение.

## 6. Content

### Level count

50 уровней.

### Difficulty bands

- **1–3:** tutorial, 4×4, минимум элементов.
- **4–15:** 4×4 / 5×5, straight + corner + первые T.
- **16–35:** 5×5 / 6×5, несколько ламп, T/cross.
- **36–50:** 6×5 / 6×6, плотные схемы и более длинные ветви.

### Storage

Уровни хранятся data-driven в `content/levels/*.json`.

Runtime generator для прохождения не нужен. Допускается dev-only генератор, который создаёт валидные level JSON и затем проходит validator.

Каждый уровень хранит валидное solution state — оно используется validator и системой подсказок.

## 7. Screens

### Boot/loading

Минимальный branded loading. Не задерживать вход искусственно.

### Main menu

Главная CTA: `Играть` или `Продолжить`.

Дополнительно:

- настройки;
- выбор уровня после появления прогресса;
- звук.

Не перегружать экран валютами и лишними режимами v1.

### Level select

Компактная сетка/линейка открытых уровней. Может быть отложена до vertical slice, если `Продолжить` покрывает ранний UX.

### Gameplay

Приоритеты:

1. игровое поле;
2. уровень / moves;
3. hint;
4. pause.

Игровая сетка визуально доминирует. Декоративный фон не конкурирует с трубами и током.

### Pause

- Продолжить;
- Перезапустить;
- Настройки;
- В меню.

### Result

- `Уровень пройден`;
- moves + best;
- `Дальше`;
- optional rewarded action, только если обещание награды явно понятно.

### Settings

- звук;
- музыка;
- вибрация только если реально используется и поддерживается;
- язык можно не выводить в v1, если релиз только RU, но строки всё равно отделены от логики.

## 8. Input

### Mouse

- ЛКМ = rotate clockwise.
- Hover подсветка допустима как feedback.

### Touch

- tap = rotate clockwise.
- один физический tap не должен дублироваться mouse emulation.

### Keyboard optional

- `Esc` = pause.
- `R` = restart после подтверждения или только в debug/desktop convenience.

## 9. Tutorial

Tutorial встроен в первые уровни, без отдельного длинного экрана.

### Level 1

- одна лампа;
- 2–3 поворачиваемые плитки;
- hand/pulse на первой плитке;
- текст: `Нажми на плитку, чтобы повернуть`.

### Level 2

Показываем угол и визуальную передачу энергии.

### Level 3

Показываем развилку/T и две лампы.

После подтверждённого понимания tutorial prompts исчезают и не повторяются без причины.

## 10. Ads

### Rewarded placements

#### Hint ×1

Игрок явно нажимает `Подсказка`. После успешного rewarded одна подходящая неверно ориентированная плитка приводится к сохранённой solution orientation.

#### Strong hint ×3

Опционально после UX-теста: просмотр рекламы корректирует до трёх плиток. Не показывать агрессивно после каждого неверного хода.

### Fullscreen placements

Только на естественной границе после завершения уровня. Cadence регулируется AdsService, а не gameplay scene.

### No-fill/adblock behavior

- кнопка rewarded становится disabled/альтернативной;
- puzzle остаётся полностью проходимым;
- UI не зависает в loading;
- награда без успешного callback не выдаётся.

## 11. Saves

### Schema scope

- schema version;
- unlocked level;
- completed levels;
- best moves per level;
- hint balance;
- tutorial flags;
- audio settings.

### Local events

Сразу после:

- победы;
- изменения setting;
- расхода/получения hint.

### Cloud events

Debounced sync после значимого изменения, через SaveService/PlatformService.

### Migration plan

Каждая опубликованная schema имеет version. Изменения после релиза сопровождаются migration function.

## 12. Audio

### Music states

- menu ambience;
- gameplay low-intensity industrial/electric ambience.

Музыка не должна быть громкой или тревожной.

### Feedback SFX

- rotate click;
- connector snap;
- energy propagation/electric hum;
- lamp on;
- level complete;
- UI click;
- rewarded/result feedback.

### Mute rules

Звук глушится при:

- рекламе;
- скрытой вкладке;
- пользовательском mute.

После возврата восстанавливается предыдущее состояние.

## 13. Visual direction

Ключевые признаки:

- тёмная industrial/electrical мастерская;
- graphite / dark navy металл;
- тёплый amber/yellow energized state;
- холодный cyan/blue для secondary UI/accent;
- объёмные, чистые, casual-friendly элементы;
- высокая читаемость topology;
- не pixel art;
- не фотореализм;
- без грязного хоррора/постапокалипсиса;
- декоративная среда может быть богаче gameplay tiles, но не мешает чтению сети.

Главное качество визуала — **идеальная геометрия соединений**, а не количество мелких деталей.

Production details: `docs/DESIGNER_TZ.md` и `docs/ASSET_MANIFEST.md`.

## 14. Technical risks

1. **Неровные pipe assets:** решается общей master geometry и runtime rotation.
2. **Concept лучше реальной игры:** visual QA после первых 3–5 assets до mass production.
3. **Слишком мелкое поле на mobile landscape:** max 6×6 + responsive board container.
4. **Hint ambiguity при нескольких решениях:** level data хранит одно гарантированно валидное canonical solution.
5. **Слишком лёгкий/однообразный контент:** difficulty curve тестируется до производства всех 50 уровней.

## 15. Definition of v1

- [ ] Core loop проходит от menu до result и следующего уровня.
- [ ] 50 валидированных уровней.
- [ ] Mouse + touch.
- [ ] Responsive landscape layout.
- [ ] Local save + platform cloud sync where available.
- [ ] Hint rewarded.
- [ ] Fullscreen placement с cooldown.
- [ ] Audio + mute/pause lifecycle.
- [ ] Final integrated art соответствует approved art direction.
- [ ] Yandex draft QA.
- [ ] Pikabu QA.
- [ ] Promo assets готовы только после финального gameplay screenshot.
