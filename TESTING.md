# Testing Guide

## Quick Smoke Test

1. Download `helix-viewer` (or `helix-viewer.exe`) and `drow.helix` from the latest release
2. Run: `./helix-viewer drow.helix`
3. Verify:
   - Model loads with textures visible
   - Orbit camera works (right-click drag)
   - Animations play from sidebar
   - Gear parts are attached to skeleton

## What to Report

If something doesn't look right, file an issue with:

- **Platform** (Linux distro / Windows version)
- **GPU** (run `glxinfo | grep "OpenGL renderer"` on Linux)
- **Screenshot** (F5 in the viewer)
- **Which .helix file** you loaded
- **What happened** vs what you expected

## Known Limitations

- Gear parts may not be attached to the base skeleton yet (engine-side WIP)
- Some heroes have 0 engine-mapped animations (they still have raw animations playable by name)
- Scale may appear wrong for some heroes (check manifest `model_scale_pct`)

## Sample Packages

Each release includes sample `.helix` packages. The full library of 124 heroes is available from the [helix-tools](https://github.com/djmsqrvve/helix-tools) repo:

```bash
cd helix-tools
./scripts/batch_package.sh --all    # builds all 124
make catalog                         # generates catalog.json index
```

## Verifying Package Integrity

```bash
# From helix-tools repo
make verify-package FILE=drow.helix

# Or all packages
make verify-package
```
