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

### Activity Breakdown

| Activity | Est. Hours |
|----------|-----------|
| Deer Death + On-the-Spot Offering | 4 hrs |
| Quota UI | 2 hrs |
| Night Lock + Bed Gate | 2 hrs |
| Basic End State / Tuning | 4 hrs |
| **Total** | **12 hrs** |



### 1. Deer Kill + Immediate Offering (4 hrs)

- [x] Wire deer point value into the offering / quota system
- [x] Add a dedicated deer body hitbox so arrow hits align to the torso better than the movement capsule
- [x] When an arrow hits a deer, call deer-side kill logic from `BP_Arrow`
- [x] On kill, offer that deer immediately and add its point value directly to the day's quota progress
- [x] Mark the deer as consumed so it cannot score twice
- [x] Delete the deer actor after it is consumed
- [x] Set and tune the daily quota target in `BP_ThirdPersonGameMode`
- [x] Add deer kill SFX feedback

**Milestone:** The player can shoot a deer and convert it directly into quota progress.

### 2. Quota UI (2 hrs)

- [x] Add a progress bar that fills as offerings are deposited
- [x] Quota met SFX
- [x] Add a simple `Quota Met` state when the requirement is reached

**Milestone:** The player always knows whether they still need to hunt more or can safely end the day.

### 3. Night Lock + Bed Gate (2 hrs)

- [X] When night begins, keep the world in night state instead of letting the cycle roll back into day
- [X] If quota is not met, bed should not allow sleep
- [X] If quota is met, bed interaction should allow sleep
- [X] On successful sleep: advance to morning, reset daily quota progress, and begin the next day

**Milestone:** Bedtime becomes the checkpoint for success or failure of the day's hunt.

### 4. Basic End State / Tuning (4 hrs)

- [X] Balrog needs to spawn near the player when nighttime occurs
- [X] When Balrog and player collide the player should die

**Nice-to-have only if fast:**
- [X] Offering SFX
- [X] Death SFX
- [X] Other SFX
