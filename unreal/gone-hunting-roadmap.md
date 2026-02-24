# Gone Hunting: Solo Dev Roadmap

**Timeline:** 16 weeks (1 semester)
**Engine:** Unreal Engine 5
**Goal:** Functional horror survival prototype, not a polished game

## Quick Reference: Core Game Loop

```
Morning → Hunt animals → Sacrifice at altar → Meet quota → Survive night → Repeat
                                                    ↓
                                            Quota increases daily
                                                    ↓
                                        Fail quota + outside at night = death
```


## Performance & Stability

> **If UE5 is slowing down or crashing, see** [ue5-performance-guide.md](ue5-performance-guide.md)


## AI Asset Generation Strategy

> **Full workflow guide (image gen → Photoshop → 3D model → UE5):** [3d-asset-workflow.md](3d-asset-workflow.md)


## Phase 1: Foundation ✅ COMPLETE

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


## Phase 2: Core Systems (Weeks 4-8)

**Priority Order:** Day/night → Hunting → Quota → Monster
Build in this order because each system depends on the previous one.

### Week 4: Day/Night Cycle

**This is your core tension mechanic—everything else revolves around it**

- [ ] Create a "GameManager" Blueprint to track game state
- [ ] Implement time variable that advances (1 real second = X game minutes)
- [ ] Rotate directional light based on time (sun movement)
- [ ] Create day/night states: Morning, Day, Evening, Night
- [ ] Add skybox that changes color with time (use BP_Sky_Sphere or Ultra Dynamic Sky)
- [ ] Display current time on HUD
- [ ] Trigger events when night begins

**Implementation Tips:**
```
Time System (simplified):
- Float variable: CurrentTime (0-24)
- Tick event: Add DeltaTime * TimeScale to CurrentTime
- If CurrentTime >= 24, reset to 0 and increment DayNumber
- Rotate sun: Set directional light rotation based on CurrentTime
```

**Resources:**
| Resource | What It Teaches |
|----------|-----------------|
| "UE5 Day Night Cycle Tutorial" - Ryan Laley | Exact system you need |
| Ultra Dynamic Sky (Fab) | Free, handles everything automatically if you want easier route |

**Milestone:** Time passes visually. Day turns to night. You can see what time it is.

### Week 5: Hunting System - Bow & Arrow

- [ ] Create bow weapon Blueprint (visual mesh + logic)
- [ ] Implement aim state (zoom camera, slow movement)
- [ ] Create arrow projectile with physics
- [ ] Add arrow trajectory preview (optional but helpful)
- [ ] Spawn arrow on release, apply force in aim direction
- [ ] Arrows stick into surfaces on collision
- [ ] Add simple crosshair UI when aiming

**Resources:**
| Resource | Notes |
|----------|-------|
| "Bow and Arrow Tutorial UE5" - various YouTube | Multiple options, pick one that makes sense |
| Projectile Movement Component | Built-in UE5 component, handles physics |

**Milestone:** Can aim and shoot arrows that fly and stick into things.

### Week 6: Animals & Hunting

> **Deer 3D asset already done and placed** — skip straight to Blueprint and animation.

- [x] Deer 3D model imported and placed in scene
- [ ] Find or generate deer animations (idle, walk, flee, death) — Mixamo is the fastest source
- [ ] Create "Deer" Blueprint with health, point value, AI behavior
- [ ] Implement simple AI: wander randomly, flee when player is close
- [ ] Deer takes damage from arrows, dies when health = 0
- [ ] Dead deer becomes harvestable (press E to collect)
- [ ] Collected deer goes into player inventory (simple array)
- [ ] Spawn multiple deer in the forest

**AI Approach (keep it simple):**
```
Deer AI State Machine:
1. IDLE - Stand still, occasionally play animation
2. WANDER - Pick random point nearby, walk to it
3. FLEE - Player too close? Run directly away at high speed
4. DEAD - Stop all movement, become harvestable
```

**Animation note:** Deer is a Static Mesh right now. To get it moving you'll need to convert it to a Skeletal Mesh with a rig, or swap it for an animated deer from Fab/Mixamo and retarget. Easiest path: grab a free animated deer from Fab and apply your existing mesh's material to it.

**Milestone:** Deer wander the forest. You can hunt and collect them.

### Week 7: Altar & Quota System

> **Altar 3D asset already done** — skip to Blueprint logic.

- [x] Altar 3D model created (AI-generated stone bowl, imported into project)
- [ ] Create Altar Blueprint with interaction zone
- [ ] Player can deposit collected animals at altar
- [ ] Calculate total points from deposited animals
- [ ] Track daily quota (starts at 20, increases each day)
- [ ] Display quota progress on HUD: "15/30 points"
- [ ] At sunset: Check if quota met
- [ ] If quota met: Reset for next day, increase quota
- [ ] If quota not met: Enable monster spawn

**Quota Scaling (tune later):**
```
Day 1: 15 points
Day 2: 25 points
Day 3: 40 points
Day 4: 60 points
Formula: BaseQuota * (1.3 ^ DayNumber) or similar
```

**Milestone:** Full gameplay loop minus the monster. Hunt → sacrifice → meet quota → next day.

### Week 8: Monster AI

**The monster should feel inevitable, not smart. Simple but terrifying.**

> **Monster 3D asset done and placed** — skip straight to Blueprint AI logic. Same animation note as the deer applies here.

- [x] Monster 3D model created and placed in scene (AI-generated corrupted elk)
- [ ] Source or generate monster animations (idle, walk/stalk, charge) — Mixamo has quadruped rigs
- [ ] Create Monster Blueprint (visual + AI)
- [ ] Monster spawns when: Night + Quota not met + Player outside cabin
- [ ] Movement: Always move directly toward player (no pathfinding needed initially)
- [ ] Monster is faster than player sprint speed
- [ ] On touch: Player dies, show game over screen
- [ ] Monster despawns at dawn
- [ ] Add audio cue when monster spawns (crucial for tension)
- [ ] Cabin = safe zone (monster can't enter or despawns near it)

**Monster Behavior:**
```
Spawn Conditions:
- IsNight == true
- QuotaMet == false
- PlayerInCabin == false

Behavior:
- Every tick: Set destination to player location
- Move toward destination at speed faster than player
- On overlap with player: Kill player
```

**Don't Overcomplicate:**
- No pathfinding initially—just move toward player
- If monster gets stuck on terrain, add simple navigation later
- The threat is the speed, not the intelligence

**Milestone:** Fail quota, go outside at night, get chased and killed. Core horror loop complete.

## Phase 3: Game Feel & Content (Weeks 9-12)

### Week 9: Polish Core Loop

- [ ] Tune day length (too short = stressful, too long = boring)
- [ ] Tune quota scaling (should feel fair for first 3-4 days)
- [ ] Tune monster speed (should catch player in ~10-15 seconds)
- [ ] Add death screen with stats (days survived, total points)
- [ ] Add restart functionality
- [ ] Test full loop 10+ times, note what feels wrong

**Playtesting Questions:**
- How long should a "day" be in real time? (Start with 5 minutes)
- Can you realistically meet the quota each day?
- Is the monster scary or just annoying?
- Is the forest too big or too small?

**Milestone:** The game is playable and "complete" in the most basic sense.

### Week 10: Audio & Atmosphere

**Audio is 50% of horror. Don't skip this.**

- [ ] Add ambient forest sounds (birds, wind, rustling)
- [ ] Different ambient for night (creepy, minimal)
- [ ] Bow sounds (draw, release, arrow impact)
- [ ] Animal sounds (idle, fleeing, death)
- [ ] Monster sounds (spawn cue, chase sounds, proximity heartbeat)
- [ ] UI sounds (quota met, quota failed, dawn)
- [ ] Footstep sounds for player

**Free Audio Sources:**
| Source | What to Get |
|--------|-------------|
| Freesound.org | Ambient, creature sounds |
| Zapsplat.com | UI sounds, impacts |
| Fab | Audio packs (some free) |
| Epidemic Sound | If you have subscription |

**Key Horror Audio Trick:**
Lower the overall ambient volume at night. Silence is scarier than noise. The monster's audio should break that silence.

**Milestone:** Game has full audio. Night feels different from day.

### Week 11: Visual Polish Pass

> **Winter aesthetic already established** — lean into it. Cold blue nights, snow particle effects, frozen breath from player.

- [ ] Improve lighting (darker nights, cold blue tones to match winter setting)
- [ ] Add fog (increases at night — dense winter fog is more oppressive than regular fog)
- [ ] Post-process effects: slight vignette, heavy desaturation at night
- [ ] Particle effects: snow/snowflakes during day, none at night (silence = dread)
- [ ] Improve altar visuals (glow, particles when sacrificing)
- [ ] Add monster visual effects (trail, glow, distortion)
- [ ] Screen effects when monster is near (chromatic aberration, shake)

**Post-Process Settings for Horror:**
```
- Vignette: 0.4-0.6
- Bloom: Low
- Film Grain: Subtle
- Color Grading: Desaturated, cool tones at night
- Chromatic Aberration: Only when monster is near
```

**Milestone:** Game looks intentionally creepy, not just unfinished.

### Week 12: Content Expansion

- [ ] Add 1-2 more animal types with different behaviors
- [ ] Expand forest area (should take 30-60 seconds to cross)
- [ ] Add environmental storytelling (abandoned campsites, bones)
- [ ] Create "close call" moments (narrow escapes feel rewarding)
- [ ] Add more hiding spots / landmarks for navigation
- [ ] Consider adding a "ritual" animation for altar sacrifices

**Milestone:** More content variety, world feels more complete.

## Phase 4: Finishing (Weeks 13-16)

### Week 13: Bug Fixing & Edge Cases

- [ ] Fix any movement/collision issues
- [ ] Handle edge cases (what if player is in altar zone at night?)
- [ ] Ensure monster can't get permanently stuck
- [ ] Test on different hardware if possible
- [ ] Fix any UI scaling issues
- [ ] Ensure game restarts cleanly

### Week 14: Main Menu & Game Flow

- [ ] Create main menu (Start Game, Quit)
- [ ] Add pause menu (Resume, Restart, Quit to Menu)
- [ ] Create game over screen with statistics
- [ ] Add simple tutorial prompts for first day
- [ ] Optional: Add options menu (volume, sensitivity)

### Week 15: Final Polish

- [ ] One more audio pass (balance levels)
- [ ] One more visual pass (lighting consistency)
- [ ] Performance optimization (if needed)
- [ ] Create a proper game icon
- [ ] Write a short description for itch.io or portfolio

### Week 16: Package & Release

- [ ] Build final executable
- [ ] Test packaged build thoroughly
- [ ] Create screenshots/trailer if desired
- [ ] Upload to itch.io (or just keep for portfolio)
- [ ] Document what you learned

## Stretch Goals (Cut If Behind)

*Only pursue these if core loop is solid by Week 10*

| Priority | Feature | Effort | Impact |
|----------|---------|--------|--------|
| 1 | Multiple monster types | Medium | High |
| 2 | Weather system (rain, fog) | Low | Medium |
| 3 | Traps to slow monster temporarily | Medium | High |
| 4 | Progression (unlock areas) | High | Medium |
| 5 | Leaderboard (local high scores) | Low | Low |
| 6 | Different bow types | Medium | Low |
| 7 | Stamina system | Low | Medium |
| 8 | Monster variety based on day | Medium | High |

### Recommended Workflow

1. **First, check Fab/Sketchfab** - Free existing assets save time
2. **AI-generate unique items** - Things that define your game's identity
3. **Buy cheap packs** - For generic items (trees, rocks, props)

### What to AI-Generate for Gone Hunting

- [ ] The demon altar (unique centerpiece)
- [ ] The monster (needs to be distinctive)
- [ ] Ritual items / occult props
- [ ] Custom animal variants if base ones feel wrong

### What NOT to AI-Generate

- Trees, rocks, foliage (Fab has better free options)
- Character hands/arms (hard to get right)
- Anything requiring complex rigging

---

## Free Asset Sources Cheat Sheet

| Source | Best For | Direct Link |
|--------|----------|-------------|
| Fab | Everything (UE5 native) | fab.com |
| Sketchfab | Specific 3D models | sketchfab.com |
| itch.io | Indie game assets | itch.io/game-assets |
| Mixamo | Character animations | mixamo.com |
| Poly Haven | HDRIs, textures | polyhaven.com |
| Kenney | Simple/stylized assets | kenney.nl |
| Freesound | Audio | freesound.org |

### Specific Asset Searches

**For Your Forest:**
- Fab: "Megascans forest", "stylized trees", "nature pack"
- Search: "low poly forest" for consistent style

**For Animals:**
- Fab: "deer", "wildlife pack", "forest animals"
- Mixamo: Upload model → apply animations

**For Horror Elements:**
- Fab: "horror props", "abandoned", "occult"
- itch.io: "horror asset pack"

---

## Risk Areas & Contingencies

### High Risk: Monster AI Feels Wrong

**Symptoms:** Monster gets stuck, doesn't feel threatening, or is too easy to avoid

**Solutions:**
1. Simplify: Monster teleports closer when out of sight
2. Use NavMesh for basic pathfinding (add in Week 8 if needed)
3. Add "fear zone" - player slows down when monster is near
4. Make monster partially phase through obstacles

---

### High Risk: Day/Night Transition Looks Bad

**Symptoms:** Jarring lighting changes, ugly sky

**Solutions:**
1. Use Ultra Dynamic Sky (free on Fab) - handles everything
2. Extend transition time (gradual change over 30 seconds)
3. Add fog during transition to hide lighting pops

---

### High Risk: Hunting Isn't Fun

**Symptoms:** Arrows feel bad, animals too hard/easy to hit

**Solutions:**
1. Add aim assist (arrows curve slightly toward animals)
2. Slow animals down significantly
3. Add animal "grazing" state where they stand still
4. Make arrows faster with less arc

---

### Medium Risk: Performance Issues

**Symptoms:** Low framerate, hitching, gradual slowdown, crashes

**Solutions:**
1. **Disable Lumen and Nanite first** (Edit → Project Settings → Rendering) — biggest single win
2. Lower viewport scalability to Medium and disable real-time rendering when not actively previewing
3. Use the **Foliage tool** for all tree/rock placement to enable Instanced Static Mesh rendering
4. Reduce Megascans texture resolution to 1K–2K in the editor while building
5. Enable **World Partition** for the open world level to stream chunks in/out of memory
6. Increase Windows virtual memory page file to at least 16GB
7. Use LODs (Level of Detail) on all meshes
8. Bake lighting instead of dynamic for final build

---

## Weekly Time Estimates

*Assuming 8-12 hours/week availability*

| Phase | Weeks | Focus | Hours/Week |
|-------|-------|-------|------------|
| Foundation | 1-3 | Learning | 10-12 |
| Core Systems | 4-8 | Building | 10-15 |
| Game Feel | 9-12 | Polish | 8-12 |
| Finishing | 13-16 | Ship | 6-10 |

---

## Success Criteria

**Minimum Viable Product (must have by Week 12):**
- [ ] Playable day/night cycle
- [ ] Can hunt at least 2 animal types
- [ ] Quota system works
- [ ] Monster chases and kills player
- [ ] Game restarts after death
- [ ] Basic audio exists

**Good Prototype (target by Week 16):**
- [ ] All MVP features polished
- [ ] Atmospheric visuals and audio
- [ ] Main menu and game flow
- [ ] Tuned difficulty curve
- [ ] 3-5 minute average run time
- [ ] Feels intentionally creepy

---

## Quick Links

**Tutorials Playlist to Follow:**
1. [UE5 Beginner Tutorial - Unreal Sensei](https://www.youtube.com/c/UnrealSensei)
2. [Ryan Laley Blueprint Tutorials](https://www.youtube.com/c/RyanLaley)
3. [Matt Aspland Quick Tutorials](https://www.youtube.com/c/MattAspland)

**Your Web Dev Advantage:**
- Blueprint logic = similar to JavaScript event handling
- UI/UMG = similar to HTML/CSS layout
- Variables/functions = you already know this
- Debugging = console + print strings work the same