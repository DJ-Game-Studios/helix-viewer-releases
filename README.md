# Helix 3D Viewer

A standalone desktop viewer for `.helix` character packages from the Helix 3D asset pipeline.

Use it to inspect assembled characters, gear, textures, skeletons, and animations without opening the full game engine.

## Download

Download the latest stable build and a sample character from [Releases](https://github.com/DJ-Game-Studios/helix-viewer-releases/releases/latest).

The current stable release provides a standalone Windows executable. Sample `.helix` packages are available alongside it.

### Windows

1. Download `helix-viewer-standalone.exe` and a sample `.helix` file.
2. Place them in the same folder.
3. Drag the package onto the viewer, or launch it from a terminal:

```text
helix-viewer-standalone.exe drow_v2.helix
```

## Controls

| Input | Action |
| --- | --- |
| Right-drag | Orbit camera |
| Middle-drag | Pan camera |
| Scroll | Zoom |
| <kbd>F</kbd> / <kbd>Home</kbd> | Frame the model |
| <kbd>W</kbd> | Toggle wireframe |
| <kbd>F5</kbd> | Save a screenshot |
| <kbd>1</kbd>–<kbd>5</kbd> | Snap to standard camera views |

## The `.helix` format

A `.helix` file is a portable character package containing:

- a base GLTF model and geometry;
- attachable gear parts;
- color, normal, and specular textures;
- animation clips;
- skeleton metadata;
- a `manifest.json` describing how everything fits together.

The viewer reads the manifest to assemble the character, attach gear to the correct bones, apply scale, and expose animations.

Read the complete [Helix format specification](HELIX_FORMAT_SPEC.md).

## Blender workflow

[Helix Blender Tools](https://github.com/DJ-Game-Studios/helix-blender-addon) imports `.helix` packages directly into Blender for inspection and editing.

1. Download the addon from its [latest release](https://github.com/DJ-Game-Studios/helix-blender-addon/releases/latest).
2. In Blender, choose **Edit → Preferences → Add-ons → Install from Disk**.
3. Enable **Helix Blender Tools**.
4. Use **File → Import → Helix Package (.helix)**.

## Repository scope

This repository contains public viewer downloads, sample packages, and the format specification. The game runtime and asset-production pipeline remain private while under active development.

## Feedback

Report viewer or package problems through [GitHub Issues](https://github.com/DJ-Game-Studios/helix-viewer-releases/issues). Include the release tag, package filename, operating system, reproduction steps, and a screenshot when useful.

## License

[MIT](LICENSE)
