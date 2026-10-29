# Tesserae

A pixel art editor for indexed sprite sheets.

Tesserae is built for tilesets and sprite sheets whose colors live in **banks**: named groups of
color slots that a pixel refers to by index, so one sheet of art can be shown in any number of
**variants** (Spring, Summer, Autumn, Winter) or **biomes** (forest, desert, swamp) without a pixel
changing. It reads and writes its own `.tess` format, imports Aseprite files, and exports the RG8
index textures and BankMap palettes a game engine consumes.

**Status: alpha.** These builds are for feedback. Expect rough edges, and keep copies of files you
care about.

## Download

Releases are on the [Releases page](https://github.com/xepherys/Tesserae/releases). Each one is a
zip for Windows 10 or 11 (64-bit). Unzip anywhere and run `Tesserae.exe`. Nothing has to be
installed first: the .NET runtime is inside the zip.

Windows may warn that the publisher is unknown, since the alpha builds are not signed. The
executable is the one in the zip and nothing else.

## Documentation

- [User guide](docs/User-Guide.html) (download and open in a browser, or read the copy inside
  the release zip)
- [The `.tess` file format](docs/Tesserae-File-Format.md)
- [Third-party notices](docs/THIRD-PARTY-NOTICES.md)
- [Changelog](CHANGELOG.md)

## Reporting a problem

Open an [issue](https://github.com/xepherys/Tesserae/issues/new/choose). In Tesserae,
**Help ▸ About Tesserae… ▸ Copy Details** puts the version, runtime and operating system on the
clipboard; paste that into the report. **Help ▸ Open Logs Folder** opens the folder with the
application log and any crash bundles; attach the newest ones. If a particular file triggers the
problem and you can share it, attach that too.

Feedback that is not a bug (something awkward, something missing, something you expected to
work another way) is just as welcome. Use the feedback template.

## Where Tesserae keeps its files

Settings, panel layouts, autosave journals and logs live under `%APPDATA%\Tesserae`. Deleting that
folder resets Tesserae to defaults. It never contains your documents.

## License

The documentation in this repository is licensed under
[Creative Commons Attribution 4.0 International](LICENSE). The Tesserae application is
distributed for evaluation during the alpha; all rights to it are reserved. The open-source
components it is built from are listed, with their licenses, in the
[third-party notices](docs/THIRD-PARTY-NOTICES.md).
