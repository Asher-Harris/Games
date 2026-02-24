# UE5 Performance & Stability Guide

> If UE5 is slowing down or crashing, follow this guide first. The open world map + Megascans foliage + Lumen combination is one of the heaviest workloads in UE5. This is a hardware problem but mostly a settings problem.

## The Crash Pattern (and Why It Happens)

If UE5 runs fine at first then gradually slows to a crawl before crashing, you're likely hitting one of these:

- **Shader compilation** — UE5 compiles shaders in the background as you work, eating up VRAM and CPU over time. This is the most common cause of the "starts fine, then degrades" pattern.
- **VRAM exhaustion** — The RTX 3060 Ti has 8GB of VRAM. Large textures, Nanite meshes, and Lumen fill it fast. Once VRAM is full it spills to system RAM and performance tanks.
- **Lumen real-time calculations** — Lumen runs constantly in the viewport even when you're not previewing. It's beautiful but expensive.

## Immediate Fixes (Do These First)

1. **Disable Lumen and Nanite** — Go to **Edit → Project Settings → Rendering** and turn off Lumen Global Illumination, Lumen Reflections, and Nanite. You can re-enable them later for a final visual pass. This is the single biggest performance win.
2. **Lower viewport scalability** — Click the dropdown in the top-left of the viewport and set it to **Medium**. This reduces real-time render quality in the editor without affecting your actual game settings.
3. **Disable real-time rendering when not needed** — Click the icon in the top-left corner of the viewport (looks like a play button) to toggle real-time off. The viewport will only update when you interact with it. Huge FPS difference when just placing assets.
4. **Increase your Windows page file** — Go to System → Advanced System Settings → Virtual Memory and set a manual page file of at least 16GB. This gives UE5 overflow room when VRAM fills up.

## Foliage-Specific Tips

Foliage is the main performance culprit in an open world map:

- **Always use the Foliage tool** (not dragging assets manually) so Instanced Static Mesh rendering kicks in — it batches thousands of trees into efficient draw calls instead of treating each one separately.
- **Keep Megascans textures in check** — Megascans assets default to high-res textures. In the asset's settings, you can override the texture resolution to 1K or 2K while you're building. Bump back up for final polish.
- **Don't place dense foliage everywhere yet** — Block out the space first with sparse placement, then add density once the core gameplay works.

## World Partition for Open World

If your map is intended to be large (30–60 seconds to cross), enable **World Partition** in your level settings. It streams chunks of the world in and out automatically so the entire map isn't loaded into memory at once. This is the right architecture for an open world level from the start — much harder to add later.

To enable: **World Settings → World Partition → Enable Streaming**

## GPU Driver & Hardware Notes

- Keep NVIDIA drivers up to date — UE5 benefits significantly from recent driver updates
- Your Ryzen 7 5700X and 32GB RAM are not the bottleneck here — the 8GB VRAM on the 3060 Ti is the constraint to work around
- For this project, disabling Lumen is the right call. The game is a horror prototype, not a tech demo — baked or simpler lighting will still look great and will be far more stable
