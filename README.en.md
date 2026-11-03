# Op. 1.000

Windows release candidate for public distribution. The internal updater version is `1.0.0`; the release tag is `v1.0.0`. See the [preflight report](docs/RELEASE_1_000.ja.md) for checks and publication requirements.

Op. is a free, independently developed music and video player with a light or dark interface and a spectrum visualizer.

## Download

Windows installers are published on the [GitHub Releases page](https://github.com/yanai00/op./releases). Development builds in this repository are not automatically published.

Windows is the currently verified desktop platform. macOS and Linux use the Tauri foundation but still need platform build and device testing. Mobile releases are not available yet.

## Features

- Local music and video libraries, playlists, favorites and backups
- Native playback, EQ, automatic volume adjustment and gapless playback
- A draggable mini player with left/right edge docking across displays
- Light and dark themes, and English / Japanese display languages

All features remain free. Experimental exclusive output has not passed long-duration physical bit-perfect verification; see [native engine notes](NATIVE_ENGINE.md).

## Language

Choose English or 日本語 in the Windows installer, or change it in **Settings → Appearance → Language**. Changing the display language does not restart playback. Interactive installation saves the chosen language; silent updates preserve your existing preference.

## Development

Install Node.js, Rust and the [Tauri prerequisites](https://v2.tauri.app/start/prerequisites/), then run `npm install`.

- `npm run desktop:dev`: run the desktop application
- `npm run check` and `npm test`: frontend validation
- `npm run desktop:build`: build a locally signed updater installer (requires the maintainer's signing key)

See [the Japanese README](README.md), [native engine notes](NATIVE_ENGINE.md) and [language/support implementation notes](docs/LANGUAGE_SUPPORT.ja.md) for details.



## License

Op. Original Material is provided under the [Op. Freeware License](LICENSE.txt). Personal and business use are free; support is optional. General permission to modify, redistribute or reuse the original source in other products is not granted. Third-party licenses, statutory rights and applicable platform terms remain separate.

See the [third-party notices and source locations](licenses/THIRD_PARTY_NOTICES.txt). This license does not change repository visibility.

<!-- op-support:start -->
## ☕ Support Op.

Op. is a free, independently developed media player, built out of a passion for music and sound.

If you enjoy using Op., consider buying me a coffee.

Your support is always optional. Every feature remains free.

[☕ Buy me a coffee](https://buymeacoffee.com/shirone_astral)
<!-- op-support:end -->
