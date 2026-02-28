# Git & LFS Workflow for Gone Hunting

## The Short Answer

**GitKraken handles LFS automatically.** Pull and push exactly like you normally would — GitKraken downloads and uploads LFS files behind the scenes. You never need to run `git lfs pull` or `git lfs push` manually during normal development.

The only exception: if LFS files somehow end up missing (you'll see "invalid package summary" errors in Unreal when opening the project), run `git lfs pull` once from the terminal to fix it.

---

## What Is Tracked by LFS

All binary assets are automatically stored in LFS based on `.gitattributes` at the repo root:

| File Type | Examples |
|-----------|---------|
| `.uasset` | All Unreal assets — meshes, materials, blueprints, textures |
| `.umap` | Level files |
| `.png` `.jpg` `.tga` `.exr` | Images and textures |
| `.wav` `.mp3` `.ogg` | Audio |
| `.fbx` `.obj` | 3D model source files |
| `.ttf` `.otf` | Fonts |

Text files (`.cpp`, `.h`, `.md`, `.ini`, `.uproject`) go through git normally — no LFS.

---

## Daily Workflow in GitKraken

### Pulling

Just click **Pull** in GitKraken. It fetches the latest commits and downloads any new LFS files automatically.

### Staging & Committing

Stage files and commit as normal. When you commit a `.uasset` or `.umap`, GitKraken automatically stores the binary in LFS and commits a small pointer in its place.

### Pushing

Click **Push**. GitKraken uploads LFS objects to GitHub first, then pushes the commits.

---

## Unreal-Specific Rules

These are the things that will cause broken references and missing assets if you skip them.

### 1. Always rename and move assets INSIDE Unreal Editor

Never rename or move `.uasset` files in Windows Explorer, git, or GitKraken. Unreal needs to create asset redirectors when things move so that existing references update automatically.

**Right way:** Content Browser → right-click asset/folder → Rename or Move Here
**Wrong way:** Renaming in Explorer, dragging in GitKraken, `git mv` in terminal

If you rename outside Unreal, any actor in the map that referenced the old path will break silently.

### 2. Commit new assets before closing Unreal

When Unreal generates new `.uasset` files — foliage types, blueprint variants, material instances — they exist on disk but are untracked by git. If you close without committing, another machine (or a fresh clone) won't have them, and the map will open with missing asset errors.

**After any session where you added or created new assets:**
1. Check GitKraken for unstaged `.uasset` files
2. Stage and commit them before closing

### 3. Commit `__ExternalActors__` files when you save the map

Gone Hunting uses World Partition, which splits map data across many small files in:
```
Content/__ExternalActors__/ThirdPerson/Map/...
```

Every time you save the map in Unreal, some of these files change (actors moved, foliage painted, references updated). These need to be committed alongside your `.umap` file — they ARE the map data.

In GitKraken after saving the map you'll often see a batch of these as modified. Stage and commit them all together.

---

## Troubleshooting

### "The summary for the package X is invalid"

The `.uasset` on disk is an LFS pointer instead of real content. LFS objects didn't download properly.

**Fix:**
```bash
cd /d/GitProjects/Games
git lfs pull
```

Then reopen the project.

### "A dependent package was not available"

An asset the map references doesn't exist on disk at all. Causes:
- File was never committed (most common — check for untracked files in GitKraken)
- File was renamed outside of Unreal (see rule #1 above)
- File was deleted

**Fix:** Either commit the missing file, or fix the reference inside Unreal.

### "Foliage instances for a missing static mesh have been removed"

A FoliageType `.uasset` is missing. Unreal auto-strips those instances from memory on load.

**Fix (if you haven't saved the map yet):**
1. In Content Browser, right-click the mesh → Asset Actions → Create Foliage Type
2. This recreates the `_FoliageType.uasset` at the correct path
3. Commit those new files immediately
4. Close and reopen the map — foliage will load correctly

**If you already saved:** The placement data is gone. Re-paint the foliage using the Foliage tool, then commit the new FoliageType assets and external actor files.

---

## Fresh Clone on a New Machine

```bash
git clone https://github.com/Asher-Harris/Games.git
cd Games
git lfs pull
```

GitKraken handles this automatically if you clone through it — LFS files download as part of the clone. The manual `git lfs pull` is only needed if cloning via terminal without the smudge filter running.
