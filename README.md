# Helix 3D Viewer — Releases

Public distribution repository for **Helix 3D Viewer** binaries and `.helix` sample packages.

## Downloads

Check the [Releases](https://github.com/djmsqrvve/helix-viewer-releases/releases) page for the latest builds.

| Artifact | Platform | Description |
|----------|----------|-------------|
| `helix-viewer` | Linux x86_64 | Standalone 3D model viewer |
| `helix-viewer.exe` | Windows x86_64 | Standalone 3D model viewer |
| `drow.helix` | Cross-platform | Sample hero package (Drow Ranger — 8 gear parts, 76 animations) |
| `axe.helix` | Cross-platform | Sample hero package (Axe — 5 gear parts, 75 animations) |

## Quick Start

### Linux

```bash
# Download viewer + a sample package
chmod +x helix-viewer
./helix-viewer drow.helix
```

### Windows

```
helix-viewer.exe drow.helix
```

### Controls

| Input | Action |
|-------|--------|
| Right-click drag | Orbit camera |
| Middle-click drag | Pan camera |
| Scroll wheel | Zoom |
| `F` / `Home` | Frame model (reset view) |
| `W` | Toggle wireframe |
| `F5` | Screenshot |
| `1-5` | Camera snap views (Front/Back/Left/Right/Top) |

## What is a .helix File?

A `.helix` file is a single-file hero package containing everything needed to view a 3D character:

- **Base model** (GLTF + binary geometry)
- **Gear parts** (armor, weapons, cape, etc.) with skeleton attachment data
- **Textures** (color, normal, specular maps)
- **Animations** (idle, run, attack, death, etc.)
- **manifest.json** — metadata describing all contents

The viewer reads `manifest.json` to know how to load the model, attach gear, apply scale, and play animations.

See [HELIX_FORMAT_SPEC.md](HELIX_FORMAT_SPEC.md) for the full format specification.

## Building the Viewer from Source

The viewer lives in the [helix_3d](https://github.com/djmsqrvve/helix_3d) repository:

```bash
cd helix_3d
cargo build --release -p helix-3d-viewer

# Binary at target/release/helix-3d-viewer
```

### Cross-compile for Windows (from Linux)

```bash
# Prerequisites
sudo apt-get install -y gcc-mingw-w64-x86-64
rustup target add x86_64-pc-windows-gnu

# Build
cargo build --release -p helix-3d-viewer --target x86_64-pc-windows-gnu
```

## Building .helix Packages

Hero packages are built from the [helix-tools](https://github.com/djmsqrvve/helix-tools) pipeline:

```bash
cd helix-tools

# Single hero
make package HERO=drow

# All 124 heroes
./scripts/batch_package.sh --all

# Verify packages
make verify-package
```

## .helix Format — Quick Reference

```json
{
  "format_version": 1,
  "hero": "drow",
  "display_name": "Drow",
  "base_model": "drow_base.gltf",
  "model_scale_pct": 100,
  "parts": [
    {"file": "drow_weapon.gltf", "id": "weapon", "attach_bone": "Bow1_0"}
  ],
  "engine_animations": [
    {"name": "idle", "index": 50, "loop_type": "loop"},
    {"name": "run", "index": 49, "loop_type": "loop"}
  ],
  "skeleton": {"joint_count": 82},
  "animation_count": 76,
  "texture_count": 46
}
```

## Related Repositories

| Repo | Role |
|------|------|
| [helix_3d](https://github.com/djmsqrvve/helix_3d) | Viewer + engine source (Rust/Bevy 0.18) |
| [helix-tools](https://github.com/djmsqrvve/helix-tools) | Asset pipeline — builds `.helix` packages |
| [dj-engine-releases](https://github.com/djmsqrvve/dj-engine-releases) | DJ-Engine binaries + `.djpak` games |
| [helix_standardization](https://github.com/djmsqrvve/helix_standardization) | Canonical TOML game data |
| [Helix2000](https://github.com/djmsqrvve/Helix2000) | 2D MMORPG (React/Phaser/Colyseus) |

## License

MIT License — see [LICENSE](LICENSE).
