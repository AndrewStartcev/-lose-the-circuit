# QA Report — Замкни цепь!

Документ заполняется на каждом значимом Web build. Сейчас проект в pre-production, поэтому все runtime проверки ожидают prototype.

## Build

- **Game version:** `0.1.0-preproduction`
- **Commit:** TBD
- **Godot:** `4.7.2-stable`
- **Platform build:** Local / Yandex / Pikabu
- **Test date:** TBD
- **Tester/agent:** TBD

## Environment

| Device | OS | Browser | Viewport | Result |
|---|---|---|---|---|
| Desktop | Windows | Chromium | 1280×720 | WAITING |
| Desktop | Windows | Chromium | 1366×768 | WAITING |
| Desktop | Windows | Chromium | 1600×900 | WAITING |
| Desktop | Windows | Chromium | 1920×1080 | WAITING |
| Desktop | Windows | Chromium | narrow landscape iframe | WAITING |
| Mobile | Android | Chromium | landscape | WAITING |
| Mobile | iOS if available | Safari | landscape | WAITING |

## Suites

### Core smoke

- [ ] Boot → menu.
- [ ] New game / continue.
- [ ] Load level.
- [ ] Rotate valid tile.
- [ ] Fixed tile ignores rotate.
- [ ] Energy propagation updates correctly.
- [ ] Lamps switch on/off correctly.
- [ ] Win only when all lamps energized.
- [ ] Result → next level.
- [ ] Restart restores initial rotations.

### Level/content validation

- [ ] Exactly one source per level.
- [ ] At least one lamp per level.
- [ ] No out-of-bounds/duplicate tiles.
- [ ] All rotations valid.
- [ ] Canonical solution passes.
- [ ] Initial board is not accidentally solved.
- [ ] Batch validator passes all v1 levels.

### Responsive

- [ ] Board cells stay square.
- [ ] No controls clipped.
- [ ] No text overflow.
- [ ] Background crop respects safe zone.
- [ ] 6×6 remains readable on smallest supported landscape viewport.
- [ ] Resize during gameplay does not corrupt layout/state.

### Input

- [ ] Mouse click rotates once.
- [ ] Touch tap rotates once.
- [ ] No duplicate mouse+touch event.
- [ ] Hover is optional feedback only.
- [ ] Input locked during result/ad/transition.

### Lifecycle

- [ ] Page hidden → gameplay/audio paused.
- [ ] Page visible → previous state restored correctly.
- [ ] Pause menu + ad + page hidden combinations do not resume early.

### Audio

- [ ] Rotate SFX.
- [ ] Energy SFX.
- [ ] Lamp SFX.
- [ ] Win SFX.
- [ ] Music/SFX settings persist.
- [ ] Ads mute game audio.

### Save

- [ ] Current unlocked level persists after reload.
- [ ] Completed state persists.
- [ ] Best moves persist and only improve correctly.
- [ ] Hint balance persists.
- [ ] Save schema version present.
- [ ] Local-first works when platform SDK unavailable.
- [ ] Cloud sync does not run on every tile rotation.

### Ads

- [ ] Rewarded requires explicit user action.
- [ ] Reward granted only after success callback.
- [ ] Reward not granted on close/fail.
- [ ] No-fill does not soft-lock UI.
- [ ] Fullscreen only at natural break.
- [ ] Repeated requests are guarded.
- [ ] Game/audio/input restore to previous state.

### Platform SDK

- [ ] Local fallback works without SDK.
- [ ] Yandex init ready path.
- [ ] Pikabu init ready path.
- [ ] Platform failure produces recoverable fallback.

### Performance/load

- [ ] No frame spikes during energy propagation.
- [ ] No oversized transparent textures.
- [ ] First interactive screen loads acceptably.
- [ ] Build/archive size within platform limits.

### Console/network

- [ ] No uncaught JS errors.
- [ ] No recurring Godot errors/warnings caused by game code.
- [ ] SDK/network failure logs are concise and recoverable.

## Visual integration QA

Проверять отдельно после первых production assets:

- [ ] all pipe ports align exactly;
- [ ] straight/corner/T/cross use same thickness;
- [ ] 90° rotation aligns pixel-perfectly;
- [ ] energized overlay does not shift geometry;
- [ ] tile/content layers have no baked mismatch;
- [ ] actual screenshot matches approved art direction;
- [ ] gameplay readability remains higher priority than decorative background.

## Metrics

- **Game ready time:** TBD
- **Build/archive size:** TBD
- **Network transferred:** TBD
- **FPS notes:** TBD

## Bugs

| Severity | Description | Steps | Status |
|---|---|---|---|
| — | No runtime build yet | — | WAITING |

## Known accepted issues

- Pre-production only: runtime checks not started.

## Verdict

`NOT TESTED — PRE-PRODUCTION`
