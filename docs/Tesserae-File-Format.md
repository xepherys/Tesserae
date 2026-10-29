# The Tesserae file format (`.tess`)

This describes the `.tess` document format as the current build writes and reads it. The
implementation is `src/Tesserae.Formats/Tess/` (`TessWriter`, `TessReader`, `TessValidation`,
`TessJsonModel`). When the format changes, change this file in the same commit.

| Fact              | Value                                                                  |
|-------------------|------------------------------------------------------------------------|
| Container         | ZIP archive (deflate), extension `.tess`                               |
| Format version    | `2` (`TessFormat.CurrentVersion`)                                      |
| Structure         | One `document.json` plus one PNG per cel under `cels/`                 |
| Determinism       | Two saves of the same document are byte-for-byte identical             |
| Identity          | Bank and slot ids are single bytes and are the RG8 index-texture bytes |

## 1. Container

A `.tess` file is a standard ZIP archive. Any zip tool can open it.

```
example.tess
├── document.json                                      the structure, palette and layer tree
└── cels/
    ├── 3f2a9c1e-5b7d-4e8f-9a0b-1c2d3e4f5a6b_0.png     one PNG per cel: <layer id>_<frame>.png
    ├── 7c8d9e0f-1a2b-4c3d-8e4f-5a6b7c8d9e0f_0.png
    └── …
```

- Every entry is written with `CompressionLevel.Optimal` and a **fixed timestamp** of
  2000-01-01T00:00:00Z. ZIP stamps the current time by default, which would make two saves of an
  unchanged document differ; the fixed stamp is what makes saves reproducible and diffable.
- Entries are written in a fixed order: `document.json` first, then cels sorted by entry name
  (ordinal).
- A cel entry is named `cels/{layerId:D}_{frameIndex}.png`, where `layerId` is the layer's GUID
  in `D` format (lower-case, hyphenated) and `frameIndex` is the zero-based frame.
- Reading opens the archive, requires `document.json`, parses and validates it, then reads every
  cel the layer tree names. A cel the tree names but the archive lacks is an error. Cels the
  tree does not name are ignored.

Content sniffing (`TessFormatHandler.CanReadContent`) accepts a stream when it is a readable
ZIP that contains `document.json`.

## 2. `document.json`

UTF-8 JSON, indented, **camelCase** property names, written by a source-generated
`System.Text.Json` context. Properties are always written, including defaults, except those
marked *omitted when null* below.

### 2.1 Top level

| Property        | Type                    | Meaning                                                                        |
|-----------------|-------------------------|--------------------------------------------------------------------------------|
| `format`        | string                  | Always `"tesserae-document"`. Any other value is rejected                      |
| `version`       | int                     | `2`. A file with a higher version is rejected ("written by a newer Tesserae")  |
| `width`         | int                     | Document width in pixels; must be positive                                     |
| `height`        | int                     | Document height in pixels; must be positive                                    |
| `tileGrid`      | object, *omitted when null* | **Version 1 only.** Read for compatibility, never written. See §2.2        |
| `grid`          | object or null          | The document grid: cell size and origin. See §2.2                              |
| `cellStatuses`  | array                   | Per-cell work status badges, sorted by `y` then `x`. See §2.3                  |
| `activeVariant` | int                     | Id of the variant the file opens in; falls back to the first variant if absent |
| `bankMaskLayer` | string, *omitted when null* | Name of the derived bank mask layer, or absent for none. See §2.7         |
| `bankNameScheme` | string, *omitted when null* | The bank name scheme template (`set-biome-material-recolor`), or absent when the names carry no structure. The same template BankMap.json carries as `bankNameScheme`. A template that no longer parses reads as none |
| `metadata`      | object                  | Document-level metadata. See §2.4                                              |
| `frames`        | array                   | One object per animation frame. See §2.5                                       |
| `palette`       | object                  | Variants and banks. See §2.6                                                   |
| `layers`        | array                   | The layer tree, top-level nodes in **stacking order, bottom first**. See §2.7  |

Example (abridged):

```json
{
  "format": "tesserae-document",
  "version": 2,
  "width": 32,
  "height": 32,
  "grid": { "originX": 0, "originY": 0, "cellWidth": 16, "cellHeight": 16 },
  "cellStatuses": [ { "x": 0, "y": 0, "status": "wip" } ],
  "activeVariant": 0,
  "bankMaskLayer": "Mask_Banks",
  "metadata": { "biomeTag": null, "maskRole": "None", "variantScope": [], "custom": {} },
  "frames": [ { "durationMs": 100 } ],
  "palette": { "variants": [ … ], "banks": [ … ] },
  "layers": [ … ]
}
```

### 2.2 Grid

```json
"grid": { "originX": 0, "originY": 0, "cellWidth": 16, "cellHeight": 16 }
```

| Property     | Meaning                                                            |
|--------------|--------------------------------------------------------------------|
| `originX`    | X of the grid's origin in document pixels (may be negative)        |
| `originY`    | Y of the grid's origin                                             |
| `cellWidth`  | Cell width in pixels; must be positive                             |
| `cellHeight` | Cell height in pixels; must be positive                            |

`grid` is `null` when the document has no grid. Version 1 files carried `tileGrid` with
`tileWidth` and `tileHeight` only; the reader maps it to a grid at origin (0, 0). If a version 1
file also carries the Aseprite importer's `grid.origin` custom metadata key (`"x,y"`), the
reader promotes it to the grid's origin and removes the key.

### 2.3 Cell statuses

```json
"cellStatuses": [ { "x": 16, "y": 0, "status": "complete" } ]
```

Each entry marks one grid cell, addressed by the document-pixel coordinate of the cell's
**top-left pixel**. The three built-in statuses are `wip`, `problem` and `complete`. Any other
name is preserved verbatim and shown as unknown, which is how custom statuses can arrive later
without a format break. "No status" is the absence of an entry.

Validation: statuses require a `grid`; each must have a non-empty name, sit on a full cell's
top-left pixel, and no two may share a coordinate. Entries are written sorted by `y`, then `x`.

### 2.4 Metadata

The same object appears on the document and on every layer node:

```json
"metadata": {
  "biomeTag": null,
  "maskRole": "None",
  "variantScope": [],
  "custom": { "aseprite.layer.flattenedTilemap": "true" }
}
```

| Property       | Type                 | Meaning                                                                      |
|----------------|----------------------|------------------------------------------------------------------------------|
| `biomeTag`     | string or null       | Free biome tag, for pipelines that group art by biome                        |
| `maskRole`     | string               | One of `None`, `Physics`, `Height`, `Normal`, `Z`, `Emissive`, `Occlusion`. Unknown values read as `None` |
| `variantScope` | int array            | Variant ids this node applies to; written sorted ascending                   |
| `custom`       | string → string map  | Arbitrary keys, written sorted by key (ordinal). Importers park facts here (for example the Aseprite reader's `aseprite.*` keys) |

### 2.5 Frames

```json
"frames": [ { "durationMs": 100 } ]
```

One object per frame, in order. A file with no frames is the reader's one accepted leniency:
a single 100 ms frame is synthesised instead of rejecting the file. The current editor paints
frame 0; the per-frame structure is carried so animation can arrive without a format change.

### 2.6 Palette

```json
"palette": {
  "variants": [
    { "id": 0, "name": "Default" },
    { "id": 1, "name": "Winter" }
  ],
  "banks": [
    {
      "id": 0,
      "name": "Bank 1",
      "notes": "",
      "maskColor": "808080",
      "includeInWang": true,
      "includeInRuleTiles": true,
      "locked": false,
      "ruleTileConnectsTo": [],
      "matchType": "None",
      "applicableLayers": [],
      "pattern": null,
      "slots": [
        {
          "id": 0,
          "label": "black",
          "colors": [
            { "variant": 0, "hex": "000000FF" },
            { "variant": 1, "hex": "1A1A2EFF" }
          ]
        }
      ]
    }
  ]
}
```

**Variants** are the document's colorways. Each has an integer `id` and a `name`. Ids are
unique and in the range 0–255. Order in the array is display order.

**Banks** are named groups of tuple slots. Fields:

| Property             | Type         | Meaning                                                                   |
|----------------------|--------------|---------------------------------------------------------------------------|
| `id`                 | int (0–255)  | The bank id. **This byte is the RG8 index texture's green channel**       |
| `name`               | string       | Display name                                                              |
| `notes`              | string       | Free text                                                                 |
| `maskColor`          | `RRGGBB`     | The color this bank's pixels take in a bank mask layer (no alpha)        |
| `includeInWang`      | bool         | Pipeline flag, round-tripped with XephTools' BankMap                      |
| `includeInRuleTiles` | bool         | Pipeline flag, round-tripped with XephTools' BankMap                      |
| `locked`             | bool         | The bank's definition refuses edits while true. Absent in files before 2026-10-23, read as `false` |
| `ruleTileConnectsTo` | int array    | Bank ids this bank's rule tiles connect to                                |
| `matchType`          | string       | `None`, `NamedLayer` or `Pattern`: which source layers the bank claims on import (XephTools `BankMatchType`). Unknown reads as `None` |
| `applicableLayers`   | string array | Layer names, for `NamedLayer`                                             |
| `pattern`            | string/null  | Substring of a layer name, for `Pattern`                                  |
| `slots`              | array        | The bank's tuple slots in display order                                   |

**Slots** are the paintable entries. A pixel references a slot by `(bank id, slot id)`, never by
color.

| Property | Type        | Meaning                                                                        |
|----------|-------------|--------------------------------------------------------------------------------|
| `id`     | int (0–255) | The slot id within its bank. **This byte is the RG8 index texture's red channel** |
| `label`  | string      | Display label                                                                  |
| `colors` | array       | One entry per variant that has a color, in the palette's variant order        |

A color entry is `{ "variant": <variant id>, "hex": "RRGGBBAA" }`. The hex is always eight
digits here: unlike BankMap.json, `.tess` never drops per-variant alpha. A slot with no entry
for a variant composites to nothing in that variant.

**Identity rules** (`TessValidation`, applied before anything is built):

- Variant ids are unique and 0–255.
- Bank ids are unique and 0–255.
- Slot ids are unique **within their bank** (bank 1 slot 0 and bank 2 slot 0 are different
  colors) and 0–255.
- A duplicate or out-of-range id rejects the whole file. These ids are stored as one byte each
  in the index texture, so a duplicate would silently change which color a painted pixel
  resolves to, and an out-of-range value would be truncated into a different, valid-looking id.

Ids are **preserved, not compacted**: deleting bank 1 of three leaves banks 0 and 2, so the
pixels that reference bank 2 keep meaning bank 2.

### 2.7 Layers

`layers` is the layer tree. Each node has a `kind` discriminator and the members that kind
uses; the others are present but unused (`children`, `cels`, `overrides` are `null` where they
do not apply; `width` and `height` are `0` for groups).

```json
{
  "kind": "indexed",
  "id": "3f2a9c1e-5b7d-4e8f-9a0b-1c2d3e4f5a6b",
  "name": "Terrain",
  "visible": true,
  "opacity": 1.0,
  "locked": false,
  "collapsed": false,
  "width": 32,
  "height": 32,
  "metadata": { "biomeTag": null, "maskRole": "None", "variantScope": [], "custom": {} },
  "children": null,
  "cels": [ { "frame": 0, "file": "cels/3f2a9c1e-5b7d-4e8f-9a0b-1c2d3e4f5a6b_0.png" } ],
  "overrides": [ … ]
}
```

| Property    | Type          | Meaning                                                                                |
|-------------|---------------|----------------------------------------------------------------------------------------|
| `kind`      | string        | `group`, `indexed`, `override` or `freeform`. Anything else rejects the file            |
| `id`        | GUID string   | Unique across the whole tree (overrides included). A blank id is given a fresh GUID on read; a duplicate rejects the file |
| `name`      | string        | Display name                                                                           |
| `visible`   | bool          | The file's own visibility (the eye in the Layers panel)                                |
| `opacity`   | double        | 0.0–1.0                                                                                |
| `locked`    | bool          | Locked against editing                                                                 |
| `collapsed` | bool          | Groups only: children folded in the Layers panel. `false` for other kinds and for files written before the field existed |
| `bankClaim` | string, *omitted when null* | The banks the layer is meant to hold: comma-separated terms, each a wildcard over the bank name (`gr-f-*`) or `segment=value` under `bankNameScheme` (`biome=f`). Absent for any bank |
| `width`     | int           | Cel width for pixel-bearing kinds; `0` for groups. A non-positive value reads as the document's width (files before the field existed) |
| `height`    | int           | As `width`                                                                             |
| `metadata`  | object        | See §2.4                                                                               |
| `children`  | array or null | Groups: child nodes, **bottom first**                                                  |
| `cels`      | array or null | Pixel-bearing kinds: `{ "frame": n, "file": "cels/…png" }` per frame, sorted by frame |
| `overrides` | array or null | Indexed layers: their variant override layers (each a node of kind `override`)        |

The four kinds:

| Kind       | Pixels                                            | Where it sits                                             |
|------------|---------------------------------------------------|-----------------------------------------------------------|
| `group`    | None                                              | Anywhere in the tree; holds `children`                    |
| `indexed`  | `(bank, slot)` references, one cel per frame      | Anywhere; may own `overrides`                             |
| `override` | `(bank, slot)` references                         | Only inside an indexed layer's `overrides`. Its painted pixels replace the owner's for the variants named in its `metadata.variantScope` during compositing |
| `freeform` | RGBA color                                       | Anywhere. Read-only in the editor until converted to indexed; what opening a PNG or a non-indexed Aseprite file produces |

**Order.** Stacking index 0 is the **bottom** of the stack; the Layers panel lists the same
nodes top-most first. Overrides are written in the order they were added.

**Bank mask layer.** When `bankMaskLayer` names a layer, that freeform layer is derived data:
the editor recomputes it after every edit from the top-most painted indexed pixel at each
position, painting that pixel's bank `maskColor`. It is saved like any other freeform layer so
that the Aseprite and RG8 pipelines, which read a mask layer, see it; anything painted on it by
hand is overwritten.

## 3. Cel PNGs

Every cel is an 8-bit RGBA PNG, encoded through Skia with **unpremultiplied** alpha
(`SkiaImageCodec`: `Rgba8888`, `Unpremul`). Its pixel size is the owning layer's `width` and
`height`, which may be smaller than the document.

### 3.1 Indexed and override cels

The pixels are **not colors**. Each one encodes a tuple reference, in exactly the layout the
RG8 index texture uses, so cel persistence and RG8 export share one encoder:

| Channel | Painted pixel                   | Unpainted pixel |
|---------|---------------------------------|-----------------|
| R       | slot id (0–255)                 | 0               |
| G       | bank id (0–255)                 | 0               |
| B       | 0                               | 0               |
| A       | the pixel's own alpha (1–255)   | 0               |

Alpha is the pixel's own, carried through unchanged from the art it was converted from or
painted at: the pixel renders at its slot's color's alpha in the variant **times** this alpha,
so a translucent pixel stays translucent in every variant and a slot a variant hides stays
hidden. Alpha 0 is an unpainted pixel. Opened in an image viewer, an indexed cel looks like a
near-black image with faint red and green, which is expected. Files written before 2026-10-23
hold `A = 255` for every painted pixel, which reads back as fully opaque.

### 3.2 Freeform cels

Straight RGBA color, top-down rows, unpremultiplied alpha.

### 3.3 Reuse across saves

Each surface carries a revision counter bumped by every pixel write. A save reuses a cel's last
PNG bytes while its revision is unchanged, and re-encodes only cels that changed, so autosave
of a large document is cheap. The archive's content is identical to a full re-encode.

## 4. Reading rules

The reader is strict by design: a file that cannot be loaded honestly is rejected with an
`InvalidDataException` naming the first problem, rather than half-built. In order:

1. The stream must be a readable ZIP containing `document.json`.
2. `document.json` must be valid JSON with `format` = `tesserae-document` and positive
   `width` and `height`.
3. Identity validation (§2.6 ids, §2.7 layer ids, §2.2–2.3 grid and statuses).
4. `version` must not exceed `2`.
5. Every cel named by the tree must exist in the archive and decode.

Two leniencies, both narrow: a missing frames list gets one default frame, and an
`activeVariant` that names no variant falls back to the first variant. Unknown `maskRole` and
`matchType` strings read as `None`; unknown cell status names are kept.

**Never saved.** The editor's selection, floating (lifted) pixels, per-view zoom, per-view
variant and per-view layer visibility overrides are session state and are not written. Panel
layouts live outside the document (see the user guide).

## 5. Version history

| Version | Change                                                                                             |
|---------|----------------------------------------------------------------------------------------------------|
| 1       | Initial format. Grid as `tileGrid` (size only), no cell statuses                                   |
| 2       | `grid` with an origin replaces `tileGrid`; `cellStatuses` added. The bump stops a version 1 build silently dropping both and making the loss permanent on its next save |
| 2 (additive) | `collapsed` on groups; `width`/`height` on pixel-bearing layers; `locked` on banks; `bankMaskLayer`. Each is optional on read with the harmless default, so no bump was needed |
| 2 (additive) | `bankNameScheme` at the top level and `bankClaim` on nodes (2026-10-24). Both omitted when null and optional on read |

## 6. Relationship to other formats

| Format                 | Direction      | Relationship to `.tess`                                                        |
|------------------------|----------------|--------------------------------------------------------------------------------|
| RG8 set (`*_Index.png`, `*_Atlas.png`, `*_BankMap.json`) | Export (one frame) and import | The index texture uses the same R = slot, G = bank, A = the pixel's alpha layout as an indexed cel; the atlas is built from `palette` |
| BankMap (`.json`, `.md`) | Export; JSON import | The `palette` section alone. Markdown mirrors XephTools' `BankMapIO.ExportToMarkdown` line for line |
| Aseprite (`.aseprite`) | Import only    | Indexed files become indexed layers with a bank per palette; RGBA files become freeform layers; tilemaps are flattened |
| PNG                    | Open           | One freeform layer                                                             |
| XephTools (Unity)      | Read by XephTools | `TessPixelSource` in `com.xephtools.common` reads a `.tess` as a layered, animated source resolved in one variant (`GetVariant` for the others), and `BankMapIO.ImportFromFile` reads its `palette` as banks. It is strict the way §4 is, and ignores cel entries the tree does not name |

Details of the RG8 and Aseprite mappings are in `docs/rg8-interop.md` and
`docs/aseprite-interop.md`.
