# Gone Hunting: Solo Dev Roadmap

**Timeline:** 16 weeks (1 semester)
**Engine:** Unreal Engine 5
**Goal:** Functional horror survival prototype, not a polished game

---

## Quick Reference: Core Game Loop

```
Morning → Hunt animals → Sacrifice at altar → Meet quota → Survive night → Repeat
                                                    ↓
                                            Quota increases daily
                                                    ↓
                                        Fail quota + outside at night = death
```

---

## Phase 1: Foundation (Submission Sprint)

> **Tutorial done. Start building.** You already understand variables, events, and logic from web dev—Blueprints will click faster by doing than by watching more tutorials.

- [x] Complete "Your First Hour in Unreal Engine 5"
- [ ] Create new UE5 project from the **Third Person template** (gives you a working character for free—do not use a blank project)
- [ ] Create a new level and delete/ignore the default template level
- [ ] Add terrain using the Landscape tool—raise some hills, carve out a forest clearing
- [ ] Grab a free Megascans nature pack from Fab, place trees and rocks in the scene
- [ ] Place two landmark actors: one for the **cabin** (safe zone), one for the **altar** (sacrifice point)—placeholder shapes are fine
- [ ] Add a directional light and sky sphere, set a moody late-afternoon look
- [ ] Build one Blueprint: walk up to the altar, press E, a counter increments
- [ ] Add a basic HUD widget showing "Quota: X / 15" on screen
- [ ] Walk through the space and make sure navigation feels open and readable

**Tips:**
- Use **Print String** nodes in Blueprints to debug—faster than anything else when you're starting out
- Save constantly (Ctrl+S). UE5 will crash. It is not a matter of if.
- Nanite and Lumen are enabled by default in UE5—they look great but can tank performance on lower-end hardware. If you're getting low framerates, turn them off in Project Settings first before optimizing anything else.
- If you get stuck on a Blueprint, search YouTube for the exact feature + "UE5"—Matt Aspland's short tutorials are the fastest way to unblock yourself

**Asset Sources:**
| Source | What to Get | Notes |
|--------|-------------|-------|
| Fab | Megascans trees, rocks, foliage | Free with UE subscription |
| UE5 Starter Content | Basic shapes, materials | Built-in, good for placeholders |

**Milestone:** A walkable forest space with cabin and altar landmarks, one working Blueprint interaction, and a quota number on screen.

---

## Phase 2: Core Systems (Weeks 4-8)

> **Priority Order:** Day/night → Hunting → Quota → Monster
> Build in this order because each system depends on the previous one.

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

---

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

---

### Week 6: Animals & Hunting

- [ ] Create base "Animal" Blueprint with health, point value, movement
- [ ] Add 2-3 animal types (deer = 10pts, rabbit = 3pts, boar = 15pts)
- [ ] Implement simple AI: Wander randomly, flee when player is close
- [ ] Animals take damage from arrows, die when health = 0
- [ ] Dead animals become "harvestable" (press E to collect)
- [ ] Collected animals go into player inventory (simple array)
- [ ] Spawn animals in the forest area

**AI Approach (keep it simple):**
```
Animal AI State Machine:
1. IDLE - Stand still, occasionally play animation
2. WANDER - Pick random point nearby, walk to it
3. FLEE - Player too close? Run directly away
4. DEAD - Stop all movement, become harvestable
```

**Asset Sources:**
| Source | What to Get |
|--------|-------------|
| Fab | Search "deer", "rabbit", "boar" - many free options |
| Mixamo | Free animal animations (limited selection) |
| AI-generated (Meshy/Tripo) | Custom animals if needed |

**Milestone:** Animals wander the forest. You can hunt them and collect bodies.

---

### Week 7: Altar & Quota System

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

---

### Week 8: Monster AI

**The monster should feel inevitable, not smart. Simple but terrifying.**

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

---

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

---

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

---

### Week 11: Visual Polish Pass

- [ ] Improve lighting (darker nights, more contrast)
- [ ] Add fog (increases at night)
- [ ] Post-process effects: slight vignette, desaturation at night
- [ ] Particle effects: dust motes, fireflies at dusk
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

---

### Week 12: Content Expansion

- [ ] Add 1-2 more animal types with different behaviors
- [ ] Expand forest area (should take 30-60 seconds to cross)
- [ ] Add environmental storytelling (abandoned campsites, bones)
- [ ] Create "close call" moments (narrow escapes feel rewarding)
- [ ] Add more hiding spots / landmarks for navigation
- [ ] Consider adding a "ritual" animation for altar sacrifices

**Milestone:** More content variety, world feels more complete.

---

## Phase 4: Finishing (Weeks 13-16)

### Week 13: Bug Fixing & Edge Cases

- [ ] Fix any movement/collision issues
- [ ] Handle edge cases (what if player is in altar zone at night?)
- [ ] Ensure monster can't get permanently stuck
- [ ] Test on different hardware if possible
- [ ] Fix any UI scaling issues
- [ ] Ensure game restarts cleanly

---

### Week 14: Main Menu & Game Flow

- [ ] Create main menu (Start Game, Quit)
- [ ] Add pause menu (Resume, Restart, Quit to Menu)
- [ ] Create game over screen with statistics
- [ ] Add simple tutorial prompts for first day
- [ ] Optional: Add options menu (volume, sensitivity)

---

### Week 15: Final Polish

- [ ] One more audio pass (balance levels)
- [ ] One more visual pass (lighting consistency)
- [ ] Performance optimization (if needed)
- [ ] Create a proper game icon
- [ ] Write a short description for itch.io or portfolio

---

### Week 16: Package & Release

- [ ] Build final executable
- [ ] Test packaged build thoroughly
- [ ] Create screenshots/trailer if desired
- [ ] Upload to itch.io (or just keep for portfolio)
- [ ] Document what you learned

---

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

---

## AI Asset Generation Strategy

### When to Use AI Tools

| Tool | Best For | Quality | Speed |
|------|----------|---------|-------|
| Hunyuan 3D (Tencent) | Text/image to 3D, PBR-ready output, free studio | High | Fast |
| Meshy.ai | 3D models from text/image | Medium | Fast |
| Tripo AI | Character models | Medium-High | Fast |
| Luma Genie | Organic shapes | Medium | Fast |
| CSM.ai | Detailed models | High | Medium |

**Hunyuan 3D Notes:**
- Free online studio (Hunyuan 3D Studio)—no subscription, no credits, no local install required
- **PBR-ready output**: textures come with base color, roughness, metallic, and normal maps already set up—these plug directly into UE5's material system without cleanup
- GLB/OBJ output imports into UE5 via drag-and-drop into the Content Browser
- Good for the altar, monster, and occult props where you want something unique that you can't find on Fab
- Generate from text prompts or upload a reference image for more control over the result

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

**Symptoms:** Low framerate, hitching

**Solutions:**
1. Reduce foliage density
2. Use LODs (Level of Detail) on all meshes
3. Bake lighting instead of dynamic
4. Reduce shadow quality in project settings

---

### Medium Risk: Scope Creep

**Symptoms:** Adding features not in plan, falling behind

**Solutions:**
1. Check this roadmap weekly
2. If not on the current week's tasks, stop
3. Write new ideas in a "future features" doc, don't implement
4. Prototype is the goal, not a finished game

---

### Low Risk: Burnout

**Symptoms:** Dreading working on the project

**Solutions:**
1. Work in short sessions (1-2 hours max)
2. Alternate between creative and technical tasks
3. Play other horror games for inspiration
4. It's okay to take days off

---

## Weekly Time Estimates

*Assuming 8-12 hours/week availability*

| Phase | Weeks | Focus | Hours/Week |
|-------|-------|-------|------------|
| Foundation | 1-3 | Learning | 10-12 |
| Core Systems | 4-8 | Building | 10-15 |
| Game Feel | 9-12 | Polish | 8-12 |
| Finishing | 13-16 | Ship | 6-10 |

**Total Estimated Hours:** 140-180 hours

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

**Communities for Help:**
- r/unrealengine (Reddit)
- Unreal Slackers (Discord)
- Epic Developer Community Forums

**Your Web Dev Advantage:**
- Blueprint logic = similar to JavaScript event handling
- UI/UMG = similar to HTML/CSS layout
- Variables/functions = you already know this
- Debugging = console + print strings work the same

---

*Last updated: Project kickoff*
*Remember: A finished bad game teaches more than an unfinished perfect one.*
