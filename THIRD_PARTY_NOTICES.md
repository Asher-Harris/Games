# Third-party content

This repository is a public portfolio, not an open-source release. It does not grant an open-source license for the original project files. The third-party material listed below remains subject to its own terms.

## Included content

### Godot tutorial assets

The project in `godot/tutorial` follows the Brackeys beginner tutorial and includes material from the [Brackeys first-game-in-godot repository](https://github.com/Brackeys/first-game-in-godot). That repository is released under the [Unlicense](https://github.com/Brackeys/first-game-in-godot/blob/main/LICENSE).

### Unreal Engine template content

Parts of `unreal/GoneHunting/Content/Characters/Mannequins`, `Input`, `LevelPrototyping`, and `ThirdPerson` originated from Unreal Engine templates or examples. Epic permits distribution of Examples, including modified Examples, under the [Unreal Engine End User License Agreement](https://www.unrealengine.com/eula/unreal).

### AI-generated Balrog

The repository author generated the Balrog model and textures with Nano Banana Pro and Hunyuan 3D, then prepared the output with Photoshop and Blender. The [3D asset workflow](unreal/3d-asset-workflow.md) records the process. These files are identified here as AI-generated content.

[Google's Terms of Service](https://policies.google.com/terms?hl=en-US) state that Google does not claim ownership of original content generated through its services. Section 5.3 of the [Tencent Hunyuan 3D User Service Agreement](https://rule.tencent.com/rule/202501080004) states that rights in generated content remain with the user or lawful rights holder.

### Freesound audio

| Repository asset | Source | License |
| --- | --- | --- |
| `SFX_BalrogGrowl` | [Monster Roar 2.wav by quadraslayer](https://freesound.org/people/quadraslayer/sounds/398908/) | [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) |
| `SFX_DeerKill` | [Nasty Knife Stab 2.wav by Aris621](https://freesound.org/people/Aris621/sounds/478145/) | [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) |
| `SFX_Death` | [Grunt2 - Death Pain.wav by tonsil5](https://freesound.org/people/tonsil5/sounds/416838/) | [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) |
| `SFX_WolfHowl` | [Wolf Howl by NaturesTemper](https://freesound.org/people/NaturesTemper/sounds/398430/) | [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) |
| `SFX_QuotaMet` | [Congrats by Fupicat](https://freesound.org/people/Fupicat/sounds/607207/) | [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) |
| `SFX_Birds` | [Birds Singing 03.wav by DCPoke](https://freesound.org/people/DCPoke/sounds/387978/) | [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/) |

Attribution required by CC BY 4.0: "Nasty Knife Stab 2.wav" by Aris621, licensed under CC BY 4.0, via Freesound.

## Assets not included

Fab and Unreal Engine Marketplace licenses generally permit using purchased or free assets in a project, but do not permit publishing the raw assets as a reusable source collection. The following dependencies are therefore excluded from this repository:

| Dependency | Original content path |
| --- | --- |
| [temperate Vegetation: Spruce Forest](https://www.fab.com/listings/f8044501-17a2-498f-b198-5f1bc71ee87a) | `Content/PN_interactiveSpruceForest` |
| [MW Landscape Auto Material](https://www.fab.com/listings/6602874e-ef24-48c9-9055-a7ac07384696) | `Content/MWLandscapeAutoMaterial` |
| [Survival Character](https://www.fab.com/listings/1fbbd438-6206-463d-9939-1b6152a7403b) | `Content/Survival_Character` |
| [Bed](https://www.fab.com/listings/8928e27e-4a23-4dfe-8654-69dd57e4ab1f) | Imported materials and meshes under `Content/Fab/Bed` |
| [Wooden House](https://www.fab.com/listings/2a0dd0d8-933d-4b3b-be36-073149b0b3d6) | `Content/Fab/Wooden_House` |
| [Fab torch asset](https://www.fab.com/listings/93779b57-7ffa-4e60-9a69-254d8e952668) | Imported materials, meshes, and textures under `Content/Fab/Street_lighting_torch` |

The deer model, textures, and imported animation sequences remain excluded because their source is unknown. Media supplied with the [Unreal University bow and arrow tutorial](https://www.youtube.com/watch?v=sB25-mkmlDQ&t=354s) also remains excluded. Its [download page](https://www.unreal-university.com/pages/bowandarrow) and archive do not state redistribution terms. The gameplay Blueprints and other original integration work remain included.

Acquire any excluded dependencies from their publishers under your own account and license. Asset versions and import paths can differ, so restoring them may require reconnecting Unreal Engine references.
