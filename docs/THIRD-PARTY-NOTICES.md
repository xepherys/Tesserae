# Third-party notices

Tesserae is built on open-source software. This file lists every package that ships in a
Tesserae build (the runtime closure of `Tesserae.App`, including transitive dependencies), the
license each one is distributed under, and the tooling used only to build and test. Versions
are those pinned in `Directory.Packages.props` and resolved by NuGet on 2026-10-23.

License identifiers are SPDX expressions taken from each package's NuGet metadata, except where
a note says otherwise. The full text of each license is linked in the final section.

**Keep this file current:** when a package is added, removed or its license changes, update the
tables below in the same change. `dotnet list src/Tesserae.App/Tesserae.App.csproj package
--include-transitive` lists the runtime closure; each package's `.nuspec` in the NuGet cache
carries its license expression.

## Runtime dependencies (shipped with the application)

| Package                                  | Version          | License     | Copyright / author                       | Project                                        |
|------------------------------------------|------------------|-------------|------------------------------------------|------------------------------------------------|
| Avalonia                                 | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.Desktop                         | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.Themes.Fluent                   | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.Fonts.Inter                     | 12.1.4           | MIT (¹)     | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.Skia                            | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.Win32                           | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.X11                             | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.Native                          | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.FreeDesktop                     | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.FreeDesktop.AtSpi               | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.HarfBuzz                        | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.Remote.Protocol                 | 12.1.4           | MIT         | 2013–2026 The AvaloniaUI Project         | https://avaloniaui.net/                        |
| Avalonia.Angle.Windows.Natives           | 2.1.27548.20260419 | BSD-3-Clause (²) | 2018 The ANGLE Project Authors      | https://avaloniaui.net/                        |
| Avalonia.BuildServices                   | 11.3.2           | MIT         | 2023–2025 The AvaloniaUI Project         | https://github.com/AvaloniaUI/Avalonia.BuildServices |
| MicroCom.Runtime                         | 0.11.6           | MIT         | 2021 Nikita Tsukanov                     | https://github.com/kekekeks/MicroCom           |
| Tmds.DBus.Protocol                       | 0.94.1           | MIT         | Tom Deseyn                               | https://github.com/tmds/Tmds.DBus              |
| Dock.Avalonia                            | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| Dock.Avalonia.Themes.Fluent              | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| Dock.Model                               | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| Dock.Model.Mvvm                          | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| Dock.Serializer.SystemTextJson           | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| Dock.Settings                            | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| Dock.MarkupExtension                     | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| Dock.Controls.DeferredContentControl     | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| Dock.Controls.ProportionalStackPanel     | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| Dock.Controls.Recycling                  | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| Dock.Controls.Recycling.Model            | 12.1.0.12        | MIT         | 2026 Wiesław Šoltés                      | https://github.com/wieslawsoltes/Dock          |
| CommunityToolkit.Mvvm                    | 8.4.2            | MIT         | .NET Foundation and Contributors         | https://github.com/CommunityToolkit/dotnet     |
| Microsoft.Extensions.DependencyInjection | 10.0.0           | MIT         | Microsoft Corporation                    | https://dot.net/                               |
| Microsoft.Extensions.DependencyInjection.Abstractions | 10.0.0 | MIT    | Microsoft Corporation                    | https://dot.net/                               |
| Serilog                                  | 4.4.0            | Apache-2.0  | Serilog Contributors                     | https://serilog.net/                           |
| Serilog.Sinks.File                       | 7.0.0            | Apache-2.0  | Serilog Contributors                     | https://github.com/serilog/serilog-sinks-file  |
| SkiaSharp                                | 3.119.4          | MIT (³)     | Microsoft Corporation                    | https://github.com/mono/SkiaSharp              |
| SkiaSharp.NativeAssets.Win32             | 3.119.4          | MIT (³)     | Microsoft Corporation                    | https://github.com/mono/SkiaSharp              |
| SkiaSharp.NativeAssets.Linux             | 3.119.4          | MIT (³)     | Microsoft Corporation                    | https://github.com/mono/SkiaSharp              |
| SkiaSharp.NativeAssets.macOS             | 3.119.4          | MIT (³)     | Microsoft Corporation                    | https://github.com/mono/SkiaSharp              |
| SkiaSharp.NativeAssets.WebAssembly       | 3.119.4          | MIT (³)     | Microsoft Corporation                    | https://github.com/mono/SkiaSharp              |
| HarfBuzzSharp                            | 8.3.1.3          | MIT (⁴)     | Microsoft Corporation                    | https://github.com/mono/SkiaSharp              |
| HarfBuzzSharp.NativeAssets.Win32         | 8.3.1.3          | MIT (⁴)     | Microsoft Corporation                    | https://github.com/mono/SkiaSharp              |
| HarfBuzzSharp.NativeAssets.Linux         | 8.3.1.3          | MIT (⁴)     | Microsoft Corporation                    | https://github.com/mono/SkiaSharp              |
| HarfBuzzSharp.NativeAssets.macOS         | 8.3.1.3          | MIT (⁴)     | Microsoft Corporation                    | https://github.com/mono/SkiaSharp              |
| HarfBuzzSharp.NativeAssets.WebAssembly   | 8.3.1.3          | MIT (⁴)     | Microsoft Corporation                    | https://github.com/mono/SkiaSharp              |

The application also runs on the **.NET 10 runtime** (Microsoft, MIT), which a
framework-dependent build expects to be installed and a self-contained build carries with it.

### Fonts embedded in the application

| Font          | Version / source                                                    | License                        | Copyright                                                            |
|---------------|---------------------------------------------------------------------|--------------------------------|----------------------------------------------------------------------|
| Pixelify Sans | `src/Tesserae.App/Assets/Fonts/PixelifySans.ttf`, the variable      | SIL Open Font License 1.1      | 2021 The Pixelify Sans Project Authors                               |
|               | font from google/fonts `ofl/pixelifysans` (fetched 2026-10-23)      | (`PixelifySans-OFL.txt` beside | (https://github.com/eifetx/Pixelify-Sans)                            |
|               |                                                                     | it)                            |                                                                      |

The interface's default font (View ▸ Pixel Font). The OFL allows bundling and redistribution with the
application; the font's reserved name is not used for any derived font.

### Notes on bundled components

1. **Inter font.** `Avalonia.Fonts.Inter` is MIT, but the font it embeds, *Inter* by Rasmus
   Andersson, is licensed under the **SIL Open Font License 1.1**. The OFL permits bundling the
   font in an application and redistributing it, with its copyright notice and license kept
   with it; the font may not be sold on its own. https://github.com/rsms/inter
2. **ANGLE.** The package's NuGet metadata carries no license expression; the `LICENSE` file
   inside it is the **BSD-3-Clause** license of The ANGLE Project Authors (Google). Windows only:
   it is the OpenGL ES to Direct3D translation layer Avalonia renders through.
3. **Skia.** SkiaSharp's managed code is MIT. The native library it bundles, Google's **Skia**,
   is BSD-3-Clause, and Skia in turn bundles further BSD and MIT licensed components (libpng,
   zlib, expat and others). SkiaSharp's own `LICENSE.txt` and the Skia `LICENSE` files cover
   these.
4. **HarfBuzz.** HarfBuzzSharp's managed code is MIT. The bundled **HarfBuzz** text-shaping
   library is under the "Old MIT" license (a permissive MIT-style license).

## Build and test tooling (not shipped)

| Package                   | Version  | License     | Project                                        |
|---------------------------|----------|-------------|------------------------------------------------|
| Avalonia.Headless.XUnit   | 12.1.4   | MIT         | https://avaloniaui.net/                        |
| Microsoft.NET.Test.Sdk    | 17.14.1  | MIT         | https://github.com/microsoft/vstest            |
| xunit.v3                  | 3.2.2    | Apache-2.0  | https://xunit.net/                             |
| xunit.runner.visualstudio | 3.1.4    | Apache-2.0  | https://xunit.net/                             |
| coverlet.collector        | 6.0.4    | MIT         | https://github.com/coverlet-coverage/coverlet  |

## License summary

| License                  | What it asks of a redistributor                                                      |
|--------------------------|--------------------------------------------------------------------------------------|
| MIT                      | Keep the copyright notice and the permission notice with the software                |
| BSD-3-Clause             | Keep the copyright notice and disclaimer; do not use the authors' names to endorse   |
| Apache-2.0               | Keep the notices (and any `NOTICE` file); state significant changes; patent grant    |
| SIL Open Font License 1.1| Keep the font's copyright and license; do not sell the font by itself                |

None of these licenses is copyleft: none requires Tesserae's own source to be published, and
all permit commercial use.

## License texts

- MIT: https://opensource.org/license/mit
- BSD-3-Clause: https://opensource.org/license/bsd-3-clause
- Apache-2.0: https://www.apache.org/licenses/LICENSE-2.0
- SIL Open Font License 1.1: https://openfontlicense.org/open-font-license-official-text/

A release build should carry the copyright notices above and these license texts beside the
executable. Interchange formats Tesserae reads and writes (Aseprite's `.aseprite`, XephTools'
RG8 set and BankMap) are file formats, not code, and carry no license obligation here.
