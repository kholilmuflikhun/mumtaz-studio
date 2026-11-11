# Mumtaz Studio: Phase 0 baseline

Status: Phase 0 foundation and initial branding validation passed. This is an independent downstream product built from the TraceDraw open-source codebase.

## Verified fork baseline
- Repository: https://github.com/kholilmuflikhun/mumtaz-studio
- Base branch: main
- Implementation branch: feat/mumtaz-studio-foundation
- Workspace version at inspection: 0.42.1
- Rust edition: 2021
- Minimum Rust version: 1.89
- License: MIT OR Apache-2.0
- Shared Rust application: native egui/eframe and WebAssembly browser target.

## Architecture guardrails
1. Preserve the Rust engine and existing workspace crates.
2. Document mutations must use tracedraw_core::Command through Engine::run.
3. Treat all user files and input as untrusted. Do not panic on malformed input.
4. Keep document/render logic shared across desktop and browser.
5. Do not introduce Electron or Tauri without an explicit architecture decision.
6. Keep upstream copyright, licenses, attribution, and provenance.
7. Do not claim the downstream product is endorsed by upstream contributors.
8. Do not require accounts or cloud connectivity for local editing.

## Required verification before release
- CI formatting gate for the downstream Rust build script (`rustfmt --check --edition 2021 apps/tracedraw/build.rs`)
- cargo test --workspace
- cargo build -p tracedraw
- cargo clippy --workspace --all-targets -- -D warnings
- Browser WebAssembly build using the existing upstream build process
- Desktop launch and file open/save smoke tests
- Malformed input regression tests
- License and attribution review

The complete PR CI matrix passed on commit `197fe8fcb74143b5fa9c2630122cb5bee72e3040`, including workspace tests, app tests, Clippy, WebAssembly/PWA, Android, Android Emulator, and Windows/macOS/Linux desktop builds. This does not constitute a production release or a manual end-user launch/file round-trip test.

## CI validation follow-up

- Installed `rustfmt` in the pinned Rust toolchain used by the CI formatting gate.
- Scoped the formatting gate to `apps/tracedraw/build.rs`, the downstream Rust file changed for product metadata, to avoid reformatting unrelated upstream files.
- Updated Web app tests to assert the Mumtaz Studio splash text, PWA name, service-worker cache namespace, and fork-owned download/source/issues URLs.
- Updated the web test expectations to match the Mumtaz Studio splash text, PWA name, service-worker cache namespace, and fork-owned URLs.
- The full CI matrix passed on the commit recorded above.

## Platform display-name rebranding

- Linux desktop launcher display name and description now identify Mumtaz Studio; executable command, icon identifier, MIME types, and startup class remain unchanged.
- Android launcher/activity labels now identify Mumtaz Studio; package ID and native library name remain unchanged for compatibility.
- macOS bundle display/name and document type descriptions now identify Mumtaz Studio; bundle identifier, executable, UTI identifiers, and file extensions remain unchanged.
- These platform metadata changes are awaiting the next CI run before being marked validated.


## Packaging and installer identity follow-up

- Windows Inno Setup and MSIX display labels now use Mumtaz Studio. The upstream installer language-selection behavior from PR #1786 is retained; only user-facing product strings were changed.
- Windows ProgID `TraceDraw.Document`, installer language registry storage, executable names, and artifact filename conventions remain stable for this migration stage.
- Linux AppImage bootstrap retries transient download failures, uses HTTP fail-on-status handling, validates the downloaded executable's ELF magic, and only then moves it into the build cache.
- macOS DMG volume and application bundle display name now use Mumtaz Studio; internal executable and icon filenames remain unchanged.
- Current packaging changes are pending CI validation. The previous Linux ARM64 package failure was caused by a transient HTTP 504 response returning HTML in place of linuxdeploy.
