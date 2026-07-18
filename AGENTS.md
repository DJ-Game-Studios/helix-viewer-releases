# Agent Instructions

This is a **public releases repository** with restricted scope. It distributes pre-built binaries and sample packages only.

## What belongs here

- Pre-compiled viewer binaries (Linux + Windows)
- Sample `.helix` hero packages for testing
- Documentation (README, format spec, testing guide)
- GitHub Release assets and changelogs

## What does NOT belong here

- Source code (lives in
  [helix-viewer-wgpu](https://github.com/DJ-Game-Studios/helix-viewer-wgpu))
- Build scripts or CI pipelines that compile from source
- The full 124-hero package library (too large — ship 2-3 samples per release)
- Game data, engine internals, or private configuration

## Release Process

1. Build the viewer binary in `helix-viewer-wgpu`:
   `cargo build --release -p helix-viewer-wgpu --features egui`
2. Copy binary to this repo or upload directly as a GitHub Release asset
3. Include 2-3 sample `.helix` packages (build with `make package HERO=drow` in helix-tools)
4. Tag with semver: `v0.1.0`, `v0.2.0`, etc.
5. Write release notes describing what changed

## Creating a Release

```bash
gh release create v0.1.0 \
  --title "v0.1.0 - Initial Viewer Release" \
  --notes "$(cat <<'EOF'
## Helix 3D Viewer v0.1.0

First public release of the standalone model viewer.

### Features
- Load .helix hero packages
- Orbit camera, zoom, pan
- Animation playback from sidebar
- Screenshot (F5), wireframe (W)

### Sample Packages
- drow.helix (8 gear parts, 76 animations)
- axe.helix (5 gear parts, 75 animations)
EOF
)" \
  helix-viewer \
  drow.helix \
  axe.helix
```

## Related Repos

| Repo | What to do there |
|------|-----------------|
| helix-viewer-wgpu | Build the viewer binary, implement viewer features |
| helix_3d | Flagship runtime and owner of the viewer's declared `helix-water` integration |
| helix-tools | Build `.helix` packages, update format spec |
| This repo | Distribute binaries + sample packages |
