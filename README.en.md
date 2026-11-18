# Starshot

[简体中文](README.md) | [English](README.en.md)

A local-first screenshot, annotation, pinning and image-styling app for macOS and Windows. Built with Rust, Tauri 2, Vue 3 and TypeScript. This is a standalone product, not a desktop pet or clipboard history manager.

[Project site](https://stardreamtech.github.io/projects/starshot/) · [Releases and installers](https://github.com/StarDreamTech/StarShot/releases) · [Verification notes](docs/verification.md)

## Capture and edit
- Freeze multiple displays on demand. Snap to window boundaries or pixel-based candidates; draw manually, resize in eight directions and nudge by 1 or 10 pixels. Use the magnifier for HEX values. Capture immediately or after 3, 5 or 10 seconds.
- Annotate, undo, redo, copy, save or pin directly below the selection. Open **More editing** for the full editor. Import PNG, JPEG or WebP; add arrows, shapes, brush strokes, highlights, labels, sequence numbers, mosaics, solid redactions, stickers or watermarks. Cropping is non-destructive.
- Optional local recognition suggests sensitive text to redact. Inspect the results before applying them: macOS supports Chinese and English, while Windows OCR primarily recognizes Latin text and numbers. Decorative backgrounds are excluded; missed detections are possible.
- Style the canvas with a gradient, flat color, transparency or local image. Adjust spacing, corners, borders and shadows; export PNG or JPEG at 1× or 2× from the same canvas used for preview.
- Pin an image above other windows; drag, scale, rotate, flip or fade it. Double-click to close; use the context menu to copy, save or send it back to the editor.
- [Scrolling capture](docs/scroll-capture.md) stitches downward or rightward. Start auto-scroll explicitly, pause with Escape or Space and use the accumulated preview to see the most recent frame. Manual capture remains available if scrolling fails.
- Choose among five themes or set custom colors. Styling presets omit the source image. Recent images normally stay in memory; recovery points can be saved manually, on exit or before an update. There is no persistent screenshot history.

macOS can use Accessibility permission to read control roles and geometry; absent permission or a failed lookup, window and pixel candidates remain available. Windows UI Automation and its helper ship as [source](src-tauri/src/window_regions/windows.rs), not a closed binary. See the [Windows fixture results](docs/windows-native-v3.md) for the difference between controlled tests and real cross-app coverage.

## Install and update
Apple Silicon Macs (macOS 12+) can use the organization tap:
```sh
brew install --cask stardreamtech/tap/starshot
```
The personal `cpython666/tap/starshot` cask is an alternative, not a second required install. Windows x64 installers and macOS disk images are on the [Releases page](https://github.com/StarDreamTech/StarShot/releases). See the [distribution notes](docs/homebrew.md) for signing and platform limitations: a Tauri update signature is not Apple notarization or a Windows publisher signature. Windows bundles offline OCR models; macOS uses system Vision.

In Settings, check for an update, wait for download and signature verification, then choose **Install and restart** or defer. Checking alone does not replace the app. Installation backs up the current session and old app. Clients on 0.1.0 need a manual upgrade; 0.3.2 clients check the personal repository's update channel, not just the organization repository. See the [release guide](docs/releases.md).

## Run from source
Requires Node.js 22.12+, Rust 1.90 and the Tauri platform build tools.
```sh
npm ci
npm run models:prepare  # Windows build only
npm run dev             # browser preview: http://127.0.0.1:1430
```
For the desktop app, use `npm run tauri dev`. The browser preview uses synthetic capture regions; real desktop capture and always-on-top pins need the native app. On macOS, grant Screen Recording permission for your first capture; reopening the app may be necessary. Startup does not capture the screen or read the clipboard. Close hides the main window; Quit from the tray exits the app.

`Cmd/Ctrl+Shift+2` starts capture and `Cmd/Ctrl+Alt+3` pins the most recent original. In the editor use `Cmd/Ctrl+S` to export, `Cmd/Ctrl+Z` to undo and `Cmd/Ctrl+Shift+Z` to redo. If global shortcuts conflict, use the buttons or tray instead.

## Checks and packaging
```sh
npm run version:check
npm test
npm run build
cargo test --locked --manifest-path src-tauri/Cargo.toml --no-default-features
cargo test --locked --manifest-path src-tauri/Cargo.toml --lib
cargo check --locked --manifest-path src-tauri/Cargo.toml
cargo fmt --manifest-path src-tauri/Cargo.toml -- --check
npm run tauri -- build --bundles app,dmg  # macOS
npm run tauri -- build --bundles nsis     # Windows
```
[CI](.github/workflows/verify.yml) builds macOS Apple Silicon and Windows x64 artifacts without releasing them or capturing real screens. The [product notes](docs/product.md) distinguish tested features from remaining work, including complex scrolling pages, cross-display selections, real-world control detection, Windows Chinese OCR, pin groups and GIF. [Interaction research](docs/screenshot-research.md) informed the design; assets and implementation are original.
