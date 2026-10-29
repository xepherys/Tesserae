# Changelog

## 0.1.0-alpha.1 (2026-10-29)

The first alpha. This is a feedback build: it is used daily on real tilesets, and it is also young. Keep copies of files you care about, and report anything that surprises you.

**What is here**

- Indexed layers whose pixels name a bank and slot, freeform RGBA layers, groups, and mask layers that paint only with their own colors.
- Variants (one sheet shown in several colorways) and per-view biomes, where every bank of another biome is drawn with its counterpart's colors.
- Frames, a timeline with tags, onion skinning, and Aseprite import of layers, frames and tags.
- The usual tools, pixel-perfect: pencil, eraser, shade, fill, eyedropper, move, gradient, line, curve, rectangle, ellipse, path, marquee, lasso and magic wand, with mirroring, brush shapes, snap to grid, shrinkwrap and selection expand, contract, border and invert.
- A palette panel for variants, banks and slots, with a bank filter, ramp generation and audit in Oklab, bank merge and split, and a color picker with a screen eyedropper.
- A grid with cell status and indices, tiled mode, a seam check, focus mode, alpha lock, and a variant wall that shows the cell under the pointer in every variant.
- RG8 export and import, BankMap import and export, PNG, and the `.tess` format described in the file format document.
- Autosave every two minutes with recovery on the next launch, and a crash bundle in the logs folder.

**Known gaps**

- Windows x64 only in this build.
- The build is unsigned, so Windows warns about an unknown publisher.
- No pixel-perfect option on the pencil yet.
- A mask layer is made from Layer Properties, not from the New Layer menu.

**Reporting**

Help â–¸ About Tesseraeâ€¦ â–¸ Copy Details gives the lines a report needs; Help â–¸ Open Logs Folder opens the folder to attach from. The user guide is in the zip and in `docs/`.

