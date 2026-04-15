# Gone Hunting: Solo Dev Roadmap

**Timeline:** 15 weeks (1 semester)
**Engine:** Unreal Engine 5
**Goal:** Functional horror survival prototype, not a polished game


## Performance & Stability

> **If UE5 is slowing down or crashing, see** [ue5-performance-guide.md](ue5-performance-guide.md)


## AI Asset Generation Strategy

> **Full workflow guide (image gen -> Photoshop -> 3D model -> UE5):** [3d-asset-workflow.md](3d-asset-workflow.md)


## Milestone 1: Foundation ✅ COMPLETE

**What was built:** A visually complete game world with all key 3D assets placed. No Blueprints or game logic yet - everything is static. Winter aesthetic is established and carries through all future phases.

**World:**
- [x] UE5 project created using the **Open World template**
- [x] Circular open world map with landscape material and painted layers
- [x] Mountains in the background
- [x] Winter tree pack from Fab, painted across the map with the Foliage tool

**Assets placed (all static, no logic yet):**
- [x] Cabin (from Fab) - safe zone location
- [x] Demon altar (AI-generated, see [3d-asset-workflow.md](3d-asset-workflow.md))
- [x] Deer (AI-generated)
- [x] Monster - corrupted elk (AI-generated)

**Milestone:** ✅ The world looks like the game. All key assets are in. Nothing moves or works yet - that's Phase 2.


## Milestone 2: Core Systems ✅ COMPLETE

**What was built:** The foundational systems needed to support the full game loop - time, hunting, and the enemy.

### Day/Night Cycle
- [x] Day Sequence plugin added; time advances automatically, day turns to night visually
- [x] Night event fires when time reaches 18.5 (`bIsNight` flag set on Game Mode)
- [x] Bed placed in cabin - player can press E at night to skip to morning

### Bow & Arrow Hunting
- [x] Bow Blueprint with aim state (zoomed camera, slowed movement)
- [x] Arrow projectile with physics - spawns on release, flies, sticks into surfaces
- [x] Crosshair UI when aiming
- Tutorial used: https://www.youtube.com/watch?v=sB25-mkmlDQ

### Deer (Asset only)
- [x] Deer 3D model imported and placed in scene (static - AI/harvest logic deferred to Milestone 3)

### Altar (Asset only)
- [x] Altar 3D model created and placed in scene (sacrifice/quota logic deferred to Milestone 3)

### Monster AI (Balrog)
- [x] Balrog 3D model created and placed statically on the map
- [x] Blueprint AI: Balrog follows the player when it sees them
- Night spawning, kill-on-touch, and quota gating deferred to Milestone 3


## Milestone 3: Deer Systems & Spawn Management ✅ COMPLETE

**What was built:** Deer behavior is now moving out of static world placement and into Blueprint-driven systems. `BP_Deer` has the core deer-side events needed to support runtime setup, and `BP_DeerSpawner` can create a random herd in the forest while avoiding the cleared center area.

**Goal:** Finish the deer-side foundation so the final milestone can focus on the actual kill -> offer -> quota -> sleep loop instead of still building base wildlife systems.

### Current Status

- [x] `BP_Deer` exists as the deer gameplay Blueprint
- [x] `BP_Deer` has a roam event for wandering behavior
- [x] `BP_Deer` has an `InitializeDeer` event for runtime setup
- [x] `BP_DeerSpawner` exists and spawns a random herd at game start
- [x] Deer spawning is constrained with box volumes so deer appear in the forest, not near the map origin / starting clearing
- [x] Deer spawning is stable enough to support the final prototype loop

### Carry-Forward Notes

- Deer spawning should now be treated as complete for roadmap purposes.
- Any remaining deer-side work should only be done if it is directly required for kill -> offer -> quota -> sleep.
- Avoid expanding deer AI scope further; the final milestone needs the end-to-end gameplay loop more than more wildlife polish.

**Milestone:** Deer are no longer purely static set dressing. The game now has runtime deer spawning, deer-side initialization hooks, and a herd system that can support the final prototype loop.


## Milestone 4: Final Prototype Gameplay Loop

> **This is the last milestone.** Scope must stay tight. Cut polish and secondary systems unless they directly support the core loop: kill deer -> offer deer -> meet quota -> sleep if quota met. If quota is not met, night should remain active and the player should not be allowed to end the day.

**Primary Goal:** Land one complete, playable loop from hunting to bedtime with clear quota feedback.

**Priority Order:** Deer death / on-the-spot offering -> Quota UI -> Night lock / bed gate -> Minimal fail state polish

### Activity Breakdown

| Activity | Est. Hours |
|----------|-----------|
| Deer Death + On-the-Spot Offering | 4 hrs |
| Quota UI | 2 hrs |
| Night Lock + Bed Gate | 2 hrs |
| Basic End State / Tuning | 2 hrs |
| **Total** | **10 hrs** |


### 1. Deer Death + On-the-Spot Offering (4 hrs)

**Keep this simple.** No advanced carcass system is needed for the prototype.

- [ ] Wire deer point value into the harvest / offering loop
- [ ] When a deer is hit / killed, switch it into a dead state
- [ ] Easiest presentation: deer falls over / collapses and stops roaming
- [ ] Dead deer remains in the world so the player can walk up to it
- [ ] On overlap or interaction range: show `Press E to Offer`
- [ ] On `E` press: offer that deer immediately on the spot and add its point value directly to the day's quota progress
- [ ] Mark the deer as already harvested so it cannot be offered twice
- [ ] If needed for reliability, respawn a replacement deer after one is harvested / removed

**Prototype rule:** No altar deposit step, carrying system, or inventory transfer is needed. Walking up to a dead deer and pressing `E` is enough.

**Milestone:** The player can kill a deer, approach the body, and convert it directly into quota progress.


### 2. Quota UI (2 hrs)

- [ ] Add a simple HUD widget showing current quota progress
- [ ] Display `Current Offered Points / Daily Quota`
- [ ] Add a progress bar that fills as offerings are deposited
- [ ] Show whether the player is currently carrying unoffered value
- [ ] Add a simple `Quota Met` state when the requirement is reached

**Keep it minimal:** one progress bar, one text value, one small carry-status text is enough.

**Suggested first-pass values:**
```text
Day 1 quota: 15
Day 2 quota: 20
Day 3 quota: 30
```

**Milestone:** The player always knows whether they still need to hunt more or can safely end the day.


### 3. Night Lock + Bed Gate (2 hrs)

- [ ] When night begins, keep the world in night state instead of letting the cycle roll back into day
- [ ] If quota is not met, bed interaction should show a failure message and deny sleep
- [ ] If quota is met, bed interaction should allow sleep
- [ ] On successful sleep: advance to morning, reset daily quota progress, and begin the next day

**Core rule:** If quota is not met and it is nighttime, the game remains in nighttime until the player either meets quota or dies.

**Milestone:** Bedtime becomes the checkpoint for success or failure of the day's hunt.


### 4. Basic End State / Tuning (2 hrs)

- [ ] Add the smallest possible fail-state feedback if the player dies
- [ ] Show days survived and/or total offered points
- [ ] Add restart functionality if it is quick to implement
- [ ] Tune deer point values, day length, and quota targets so one day feels achievable but not trivial

**Nice-to-have only if fast:**
- [ ] Simple sacrifice VFX
- [ ] Short UI message when quota is met
- [ ] Short sound cue on successful offering

**Milestone:** The prototype has a readable win/fail rhythm even if presentation remains very rough.
