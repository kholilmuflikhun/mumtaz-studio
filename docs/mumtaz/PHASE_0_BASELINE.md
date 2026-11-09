# Mumtaz Studio: Phase 0 baseline

Status: preparation in progress. This is an independent downstream product built from the TraceDraw open-source codebase.

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
- cargo fmt --all -- --check
- cargo test --workspace
- cargo build -p tracedraw
- cargo clippy --workspace --all-targets -- -D warnings
- Browser WebAssembly build using the existing upstream build process
- Desktop launch and file open/save smoke tests
- Malformed input regression tests
- License and attribution review

These checks are pending. Creating this file does not mean builds or tests have passed.

## CI validation follow-up

- Installed `rustfmt` in the pinned Rust toolchain used by the CI formatting gate.
- Scoped the formatting gate to `apps/tracedraw/build.rs`, the downstream Rust file changed for product metadata, to avoid reformatting unrelated upstream files.
- Updated Web app tests to assert the Mumtaz Studio splash text, PWA name, service-worker cache namespace, and fork-owned download/source/issues URLs.
- Full application test rerun is required after the final URL assertion correction; the previous run had 2,302 passing tests and one stale upstream-URL assertion failure.
- Desktop builds for Windows, macOS, and Linux, WebAssembly, Android, and Android Emulator passed in that previous run.
