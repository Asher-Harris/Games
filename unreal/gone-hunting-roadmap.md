# Gone Hunting: Solo Dev Roadmap

**Timeline:** 15 weeks (1 semester)
**Engine:** Unreal Engine 5
**Goal:** Functional horror survival prototype, not a polished game


## Performance & Stability

> **If UE5 is slowing down or crashing, see** [ue5-performance-guide.md](ue5-performance-guide.md)


## AI Asset Generation Strategy

> **Full workflow guide (image gen → Photoshop → 3D model → UE5):** [3d-asset-workflow.md](3d-asset-workflow.md)


## Milestone 1: Foundation ✅ COMPLETE

**What was built:** A visually complete game world with all key 3D assets placed. No Blueprints or game logic yet — everything is static. Winter aesthetic is established and carries through all future phases.

**World:**
- [x] UE5 project created using the **Open World template**
- [x] Circular open world map with landscape material and painted layers
- [x] Mountains in the background
- [x] Winter tree pack from Fab, painted across the map with the Foliage tool

**Assets placed (all static, no logic yet):**
- [x] Cabin (from Fab) — safe zone location
- [x] Demon altar (AI-generated, see [3d-asset-workflow.md](3d-asset-workflow.md))
- [x] Deer (AI-generated)
- [x] Monster — corrupted elk (AI-generated)

**Milestone:** ✅ The world looks like the game. All key assets are in. Nothing moves or works yet — that's Phase 2.


## Milestone 2: Core Systems ✅ COMPLETE

**What was built:** The foundational systems needed to support the full game loop — time, hunting, and the enemy.

### Day/Night Cycle
- [x] Day Sequence plugin added; time advances automatically, day turns to night visually
- [x] Night event fires when time reaches 18.5 (`bIsNight` flag set on Game Mode)
- [x] Bed placed in cabin — player can press E at night to skip to morning

### Bow & Arrow Hunting
- [x] Bow Blueprint with aim state (zoomed camera, slowed movement)
- [x] Arrow projectile with physics — spawns on release, flies, sticks into surfaces
- [x] Crosshair UI when aiming
- Tutorial used: https://www.youtube.com/watch?v=sB25-mkmlDQ

### Deer (Asset only)
- [x] Deer 3D model imported and placed in scene (static — AI/harvest logic deferred to Milestone 3)

### Altar (Asset only)
- [x] Altar 3D model created and placed in scene (sacrifice/quota logic deferred to Milestone 3)

### Monster AI (Balrog)
- [x] Balrog 3D model created and placed statically on the map
- [x] Blueprint AI: Balrog follows the player when it sees them
- Night spawning, kill-on-touch, and quota gating deferred to Milestone 3

## Milestone 3: Complete Core Gameplay Loop (Weeks 9-12)

**Goal:** Close the loop end-to-end — deer spawn and can be hunted → player sacrifices deer to meet quota → night falls and the Balrog spawns → player must meet quota to sleep.

**Priority Order:** Deer spawning → Sacrifice spot + inventory → Quota UI → Night lock + Balrog spawn → Day/night fix

### Activity Breakdown

| Activity | Est. Hours |
|----------|-----------|
| Deer Random Spawning (varying sizes) | 3 hrs |
| Sacrifice Spot & Simple Inventory | 4 hrs |
| Quota UI Progress Bar | 1 hr |
| Night Lock + Balrog Spawn | 3 hrs |
| Day/Night Cycle Fix (freeze at night) | 1 hr |
| **Total** | **12 hrs** |
| **Stretch: Deer & Balrog Animations** | +4 hrs |


### Deer Random Spawning (3 hrs)

- [ ] Remove statically placed deer from the map
- [ ] Create a Deer Spawner Blueprint that places deer at random locations in the forest
- [ ] Randomize deer size on spawn (scale between 0.7x and 1.5x)
- [ ] Assign point value based on size: larger deer = more points
- [ ] Spawn 5–10 deer at game start; respawn after a delay when one is killed
- [ ] Deer wander randomly, flee when player is close (same AI as Milestone 2 plan)

**Point Value (simple formula):**
```
Points = Round(Scale * 10)

Scale 0.7 → ~7 points
Scale 1.0 → 10 points
Scale 1.5 → ~15 points
```

**Milestone:** Multiple deer of varying sizes wander the forest. Bigger deer are worth more.


### Sacrifice Spot & Simple Inventory (4 hrs)

> **Design change from Milestone 2:** Instead of dropping deer into the altar bowl, the player walks to a sacrifice spot and sacrifices deer from their inventory. The altar mesh can still be reused as the sacrifice spot — just the interaction model changes.

**Simple Inventory (on Player Character Blueprint):**
- [ ] Add an inventory integer (or array if you want to track individual deer sizes) to the player
- [ ] When a deer is killed and player presses E: add deer to inventory (store its point value)
- [ ] Show deer count on HUD (e.g. "Deer: 2")

**Sacrifice Spot Blueprint (`BP_SacrificeSpot`):**
- [ ] Place the altar (or a simple marker) somewhere accessible in the world
- [ ] Add a Box Collision interaction zone
- [ ] On player overlap: show "Press E to Sacrifice" prompt
- [ ] On E press: if inventory has deer, remove one, add its points to the quota total
- [ ] Play a simple visual effect on sacrifice (particles or glow — can be a basic Niagara burst)
- [ ] HUD updates to reflect new quota total after each sacrifice

**Milestone:** Player kills a deer → picks it up → carries it to the sacrifice spot → sacrifices it for points.


### Quota UI Progress Bar (1 hr)

- [ ] Create a HUD widget with a progress bar showing sacrifice progress
- [ ] Display current points / daily quota target (e.g. "15 / 30")
- [ ] Progress bar fills as deer are sacrificed
- [ ] Bar turns green (or flashes) when quota is met

**Quota Scaling:**
```
Day 1: 15 points
Day 2: 25 points
Day 3: 40 points
Formula: BaseQuota * (1.5 ^ DayNumber) — tune in playtesting
```

**Milestone:** Player always knows how close they are to meeting the quota.


### Night Lock + Balrog Spawn (3 hrs)

**When night falls (`bIsNight = true`):**
- [ ] Spawn the Balrog at a random location 50–100m from the player (not right on top of them)
- [ ] Balrog hunts the player (follow AI already implemented)
- [ ] Bed interaction checks quota: if quota not met, show "You have not met your quota" and block sleep
- [ ] If quota is met: allow sleep → skip to morning → despawn Balrog → reset quota for new day → increment day counter
- [ ] On Balrog touch: kill player, show game over screen (days survived + total points)

**Milestone:** Full loop works. Hunt → sacrifice → meet quota → sleep. Fail quota → Balrog hunts you → can't sleep → die.


### Day/Night Cycle Fix (1 hr)

**Current bug:** The day/night cycle keeps running at night, so it eventually cycles back to daytime.

**Fix:** Freeze the Day Sequence when night starts. Only resume when the player sleeps.

```
When bIsNight becomes true:
  Get DaySequenceActor → Set Time Scale to 0.0   (freeze)

When player sleeps:
  Set Time Scale to 1.0                          (resume)
  Set Time of Day to 6.0                         (jump to morning)
  Set bIsNight = false
```

- [ ] Add "Set Time Scale" call when night event fires
- [ ] Add "Set Time Scale" + "Set Time of Day" calls in the sleep logic

**Milestone:** Night stays night until the player sleeps.


### Stretch Goal: Animations (4 hrs)

*Only pursue if the core loop above is solid and time allows*

- [ ] Find animated deer from Fab or Mixamo (idle, walk, flee, death)
- [ ] Retarget / apply deer animations to the spawned deer Blueprint
- [ ] Find or retarget animations for the Balrog (idle, walk/stalk, charge)
- [ ] Apply Balrog animations in its Blueprint

**Fastest path:** Grab a free animated quadruped from Mixamo, convert to Skeletal Mesh, apply your existing mesh material.

**Milestone (stretch):** Deer and Balrog have animations — the world feels alive.


## Milestone 4: Game Feel & Polish (Weeks 13-14) ⚠️ TENTATIVE

> **This milestone will flex based on what carries over from Milestone 3.** Given that Milestone 2 deferred its largest tasks forward, expect some Milestone 3 items to land here. Priorities below are ordered — do them in sequence and stop when time runs out.

### Must Do (regardless of M3 spillover)

- [ ] Death screen with days survived + total points
- [ ] Restart functionality (game loops back cleanly)
- [ ] Basic tuning: day length, quota values, Balrog speed — these can't be skipped or the game won't feel right

### Do If M3 Is Largely Complete

- [ ] Monster spawn audio cue — single sound when Balrog appears (highest impact / lowest effort for horror)
- [ ] Lower ambient volume at night vs. day (silence is scarier than adding more sound)
- [ ] Add fog at night — UE5 exponential height fog is fast to set up and adds a lot
- [ ] Darker nights + cold blue post-process color grading (one post-process volume, ~30 min)

### Only If Ahead

- [ ] Ambient forest day sounds (birds, wind)
- [ ] UI sounds (quota met, quota failed)
- [ ] Vignette + chromatic aberration when Balrog is near
- [ ] Sacrifice spot visual effect (particle burst on sacrifice)
