# 3D Asset Creation Workflow

How to go from an idea to a game-ready 3D asset in UE5 using AI tools.

## The Full Workflow

```
Nano Banana Pro (4 images) → Photoshop (remove backgrounds) → Hunyuan 3D (3D model) → Blender (optional cleanup) → Unreal Engine 5
```

## Step 1: Generate Images in Nano Banana Pro

You need **4 views** of whatever you're making: front, back, left side, right side.

### Prompt Structure

Write one detailed front view prompt, then reference it for the other three:

**Front View** — put all your details here:
```
Full body [front view] of [subject description].
The creature/object faces DIRECTLY toward the camera, no angle, no tilt.
Entire [subject] visible from [top] to [bottom], wide framing with white space on all sides.
[All visual details here]
Neutral standing pose. Photorealistic, Unreal Engine 5, PBR render.
Pure white background #FFFFFF, no text, no labels, no shadows, no ground.
```

**Back View:**
```
Full body image of the same [subject] from the front view reference.
The [subject] faces DIRECTLY away from the camera, perfectly straight, no angle, no tilt.
Entire [subject] visible from [top] to [bottom], wide framing with white space on all sides.
Neutral standing pose. Photorealistic, Unreal Engine 5, PBR render.
Pure white background #FFFFFF, no text, no labels, no shadows, no ground.
```

**Left Side View:**
```
Full body image of the same [subject] from the front view reference.
The [subject] faces perfectly to the right, camera perpendicular to its left side, no angle, no tilt.
Entire [subject] visible from [top] to [bottom], wide framing with white space on all sides.
Neutral standing pose. Photorealistic, Unreal Engine 5, PBR render.
Pure white background #FFFFFF, no text, no labels, no shadows, no ground.
```

**Right Side View:**
```
Full body image of the same [subject] from the front view reference.
The [subject] faces perfectly to the left, camera perpendicular to its right side, no angle, no tilt.
Entire [subject] visible from [top] to [bottom], wide framing with white space on all sides.
Neutral standing pose. Photorealistic, Unreal Engine 5, PBR render.
Pure white background #FFFFFF, no text, no labels, no shadows, no ground.
```

### Nano Banana Pro Prompting Tips

**Style language:**
- Use `Photorealistic, Unreal Engine 5, PBR render` — tells the model to render, not illustrate
- Never say `concept art` or `reference sheet` — causes illustration style and adds text labels
- Add `no text, no labels` explicitly to prevent watermarks or view labels being baked in

**Background:**
- Always use `Pure white background #FFFFFF, no gradient, no vignette, no shadows, no ground`
- White gives maximum contrast against dark subjects — Photoshop's Remove Background nails it in one click
- Never use gradient or colored backgrounds — they make masking harder

**Framing:**
- Say `entire [subject] visible from [antler tips] to [hooves]` to force a full body shot
- Add `wide framing with white space on all sides` — prevents the subject from being cropped
- If the model keeps zooming in too close, be even more explicit: `zoomed out, full body, nothing cropped`

**Camera angle:**
- For front view: `faces DIRECTLY toward the camera, no angle, no tilt`
- For side views: `camera perpendicular to its left/right side, no angle, no tilt`
- Avoid vague terms like `front-facing` — be explicit about direction

**Consistency across views:**
- Front prompt has all the details
- Back/side prompts just say `same [subject] from the front view reference` — keeps the design consistent
- If views are coming out inconsistent, add `consistent with the front view reference` at the end

**When things go wrong:**
- Cropped image → add `entire subject fully visible, nothing cut off`
- Wrong angle → add more explicit camera direction language
- Looks illustrated not photorealistic → remove any concept art language, add `not illustrated, not drawn`
- Text/labels appearing → add `no text, no labels, no annotations`
- Too many details causing confusion → simplify the prompt, the model handles clean prompts better

## Step 2: Remove Backgrounds in Photoshop (Optional)

1. Open each of the 4 images
2. Go to **Select → Subject** (or click Remove Background in the Properties panel)
3. Check the selection edges — zoom in and refine if needed with Select and Mask
4. Delete the background layer, leaving just the subject on transparency
5. Export as **PNG** (preserves transparency)

The pure white background from Step 1 makes this nearly automatic.

## Step 3: Generate the 3D Model in Hunyuan 3D

Go to: **https://3d.hunyuan.tencent.com/**

1. Upload your front view image as the primary reference
2. Upload back, left, and right views in the additional reference slots
3. Let it generate — takes 1-3 minutes
4. Preview the result in the viewer
5. Download as **GLB**

**Tips:**
- Hunyuan outputs PBR-ready textures (base color, roughness, metallic, normal maps) — these plug directly into UE5's material system
- If the mesh looks wrong (merged legs, missing details), go back and regenerate — sometimes the second attempt is significantly better
- Simple geometry generates more cleanly than complex organic shapes

## Step 4: Optional — Clean Up in Blender

Open the GLB in Blender if you need to:
- Fix geometry errors (merged limbs, holes in the mesh)
- Adjust proportions
- Clean up the UV maps
- Reduce poly count for performance

When done, export as **FBX** for the Unreal import.

## Step 5: Import into Unreal Engine 5

**If staying as GLB:**
- Drag the GLB file directly into the Content Browser
- Hit Import in the dialog that appears

**If you went through Blender and exported FBX:**
- Same process — drag FBX into the Content Browser

**After import:**
- Drop the **Static Mesh** (props/objects) or **Skeletal Mesh** (characters/creatures) into your level
- Check that materials came in correctly — AI-generated textures sometimes need manual tweaking in the Material Editor
- If the scale is wrong, adjust it in the Details panel after placing
