# .helix Package Format Specification

> Version 1 — 2026-04-12

## Overview

A `.helix` file is the standard distribution format for Helix hero assets. It's a ZIP archive containing a `manifest.json` at the root that describes all contents. Both the Blender addon and the helix_3d engine consume this format.

Future: `.helixc` will be a compiled/compressed variant with the same manifest structure.

## File Structure

```
hero.helix (ZIP archive)
├── manifest.json              # Required — describes everything
├── hero_base.gltf             # Required — base model with skeleton + animations
├── hero_base.bin              # Required — binary geometry data
├── hero_weapon.gltf           # Optional — gear parts
├── hero_weapon.bin
├── hero_*.png                 # Textures (color, normal, specmask, metalness, orm)
├── PACKAGE_CONTENTS.txt       # Human-readable file index
├── README_BLENDER.md          # Blender import guide
├── SKELETON_REPORT.txt        # Full bone listing + accessory joint matching
└── ANIMATION_LIST.txt         # All animations with index + channel count
```

## manifest.json Schema

```json
{
  "format_version": 1,

  "hero": "drow",
  "display_name": "Drow",

  "base_model": "drow_base.gltf",
  "model_scale_pct": 100,

  "parts": [
    {
      "file": "drow_weapon.gltf",
      "id": "weapon",
      "display_name": "Weapon",
      "attach_bone": "Bow1_0",
      "joint_count": 9
    }
  ],

  "bone_aliases": [
    ["arrow_root", "Bow1_0"]
  ],

  "skeleton": {
    "joint_count": 82,
    "joints": ["root", "pelvis", "spine1", "..."]
  },

  "animation_count": 76,

  "animations": ["idle", "run", "attack", "..."],

  "engine_animations": [
    {
      "name": "idle",
      "index": 50,
      "loop_type": "loop"
    },
    {
      "name": "death",
      "index": 32,
      "loop_type": "oneshot"
    }
  ],

  "animations_detail": [
    {
      "index": 0,
      "name": "cast2_silence_anim",
      "channels": 246,
      "additive": false
    },
    {
      "index": 1,
      "name": "@precision_aura",
      "channels": 246,
      "additive": true
    }
  ],

  "texture_count": 46,
  "total_files": 68
}
```

## Field Reference

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `format_version` | int | Always `1` for this spec |
| `hero` | string | Snake_case hero identifier (e.g., `"drow"`, `"ogre_magi"`) |
| `display_name` | string | Human-readable name (e.g., `"Drow"`, `"Ogre Magi"`) |
| `base_model` | string | Filename of the base GLTF (has skeleton + animations) |
| `model_scale_pct` | int | Scale as percentage. 100 = full size, 33 = one-third (Dota heroes) |
| `parts` | array | Gear parts list (can be empty) |
| `animation_count` | int | Total animations in the base model |
| `animations` | array[string] | All animation names from the base GLTF |
| `texture_count` | int | Number of PNG textures in the package |
| `total_files` | int | Total file count in the archive |

### Part Object

| Field | Type | Description |
|-------|------|-------------|
| `file` | string | GLTF filename for this part |
| `id` | string | Part identifier (hero prefix stripped, e.g., `"weapon"`) |
| `display_name` | string | Human-readable (e.g., `"Weapon"`) |
| `attach_bone` | string | Which bone on the base skeleton this part attaches to |
| `joint_count` | int | Number of joints in this part's sub-skeleton |

### Engine Animation Object

| Field | Type | Description |
|-------|------|-------------|
| `name` | string | Engine-recognized animation name (e.g., `"idle"`, `"run"`) |
| `index` | int | GLTF animation index in the base model |
| `loop_type` | string | `"loop"` or `"oneshot"` |

### Animation Detail Object

| Field | Type | Description |
|-------|------|-------------|
| `index` | int | GLTF animation index |
| `name` | string | Raw animation name from the GLTF |
| `channels` | int | Number of animation channels |
| `additive` | bool | True if animation name starts with `@` or contains "additive" |
| `engine_name` | string? | Present only if this animation maps to a known engine name |
| `loop_type` | string? | Present only if `engine_name` is set |

### Bone Aliases

Array of `[from, to]` pairs. When a gear part has a bone named `from` that doesn't exist in the base skeleton, map it to `to`.

### Skeleton Object

| Field | Type | Description |
|-------|------|-------------|
| `joint_count` | int | Number of joints in the base skeleton |
| `joints` | array[string] | Sorted list of all joint names |

## Recognized Engine Animations

| Name | Type | Priority | Note |
|------|------|----------|------|
| idle | loop | existing | Already exists for most heroes |
| run | loop | existing | Already exists for most heroes |
| walk | loop | 1 | Shift+WASD movement |
| attack | oneshot | existing | Already exists for most heroes |
| jump start | oneshot | 2 | Beginning of jump |
| jump in air | loop | 3 | Airborne state |
| jump landing | oneshot | 4 | Landing from jump |
| strafe left | loop | 5 | Lateral movement (future) |
| strafe right | loop | 6 | Lateral movement (future) |
| combat idle | loop | 7 | Combat stance (future) |
| run backward | loop | 8 | Backward movement (future) |
| death | oneshot | existing | Already exists for most heroes |
| victory | oneshot | existing | Already exists for most heroes |
| stun | oneshot | existing | Already exists for most heroes |
| spawn | oneshot | existing | Already exists for most heroes |

## How to Load (Engine)

1. Detect `.helix` extension
2. Extract ZIP to temp directory
3. Read `manifest.json`
4. Load `base_model` GLTF, apply `model_scale_pct / 100.0` as uniform scale
5. For each part in `parts[]`: load `file`, attach at `attach_bone` on base skeleton
6. For animations: use `engine_animations[]` for name→index mapping
7. Skip animations where `additive: true` in `animations_detail[]`
8. Apply `bone_aliases` if any part joint names don't match base

## How to Load (Blender)

The Helix Blender addon handles this automatically via **File > Import > Helix Package**.

Manual process:
1. Extract the `.helix` ZIP
2. Import `base_model` with `guess_original_bind_pose=False`
3. Import each gear part, reparent mesh onto base armature
4. Hide collision shapes (icosphere/physics meshes)
5. Apply scale from `model_scale_pct`

## Building Packages

```bash
# Single hero
make package HERO=drow

# Multiple heroes
./scripts/batch_package.sh drow axe lina

# All heroes
./scripts/batch_package.sh --all

# Only animated heroes
./scripts/batch_package.sh --animated
```
