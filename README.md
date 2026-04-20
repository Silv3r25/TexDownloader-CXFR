[![TexLoader](https://img.shields.io/badge/REQUIRES-TexLoader%20CXFR-blue?style=for-the-badge)](https://github.com/Silv3r25/TexLoader-CXFR)
[![Releases](https://img.shields.io/github/v/release/Silv3r25/TexDownloader?include_prereleases&label=DOWNLOAD&style=for-the-badge)](https://github.com/Silv3r25/TexDownloader/releases)
![Downloads](https://img.shields.io/github/downloads/Silv3r25/TexDownloader/total?label=TOTAL%20DOWNLOADS&style=for-the-badge)
![Views](https://komarev.com/ghpvc/?username=Silv3r25&label=VIEWS&style=for-the-badge&color=brightgreen)

# TexDownloader CXFR v1.0.0

An in-game texture pack browser and downloader extension for **CarX Drift Racing Online** (Kino/KSL). Browse, preview, and install community texture packs directly from the game without leaving the menu — fully integrated with [TexLoader CXFR](https://github.com/Silv3r25/TexLoader-CXFR)

## Features

- **In-Game Pack Browser** — Press `Ctrl+T` to open a clean UI listing all community packs organized by map
- **One-Click Install** — Download and auto-apply any pack with a single click, no manual file handling
- **Live Pack Catalog** — Manifest served from Cloudflare R2 CDN, always up to date with new community packs
- **Per-Pack Metadata** — See author, pack size, description, tags, and creation date before downloading
- **SHA-256 Integrity Check** — Every download is verified against its manifest hash to prevent corruption or tampering
- **Smart Map Display Names** — Internal scene names automatically translated to readable names (e.g. `ebisu` → *East Touge*, `fiorano` → *Springstone*)
- **Installed Pack Management** — Apply already-installed packs or delete them directly from the UI, with automatic restoration when deleting the active pack
- **Anti-Abuse Protection** — 5-second cooldown between downloads and a 50-downloads-per-session cap to protect the CDN
- **Secure by Design** — Base URL whitelist, path traversal protection, and input validation on every manifest entry
- **Soft Dependency on TexLoader** — Runs as a standalone extension; gracefully detects whether TexLoader is loaded and disables features if not

## Installation

1. Install [Kino](https://kino.carxtech.com/) for CarX Drift Racing Online
2. Install [TexLoader CXFR](https://github.com/Silv3r25/TexLoader-CXFR) — required dependency
3. Download `TexDownloader_CXFR.kse` from [Releases](https://github.com/Silv3r25/TexDownloader/releases)
4. Place it in `kino/extensions/`
5. Launch the game

## Usage

1. Load any map in CarX
2. Press **Ctrl+P** to open TexLoader — you'll see a new **TEXDOWNLOADER** button
3. Click it (or press **Ctrl+T** directly) to open the pack browser
4. Browse packs organized by map, click one to see details and preview
5. Click **DOWNLOAD** — the pack is fetched, verified, installed into the correct map folder, and auto-applied
6. For already installed packs, use **APPLY** or **DELETE** directly from the UI

### Pack Browser Workflow

```
TexLoader menu (Ctrl+P)
    └─> TEXDOWNLOADER button
          └─> Pack list grouped by map
                ├─> DOWNLOAD  → fetch + verify + install + auto-apply
                ├─> APPLY     → load already-installed pack
                └─> DELETE    → remove .zip + switch to Default if active
```

## How It Works

TexDownloader fetches a manifest JSON hosted on a Cloudflare R2 bucket, listing every pack available in the community library with its URL, size, SHA-256 hash, and metadata. When you click download:

1. The `.zip` is streamed to `kino/mods/TexLoaderFix/Textures/<mapname>/<packid>.zip`
2. The file is hashed and compared to the manifest's SHA-256
3. TexLoader's API is called via reflection to refresh its pack list and load the new pack
4. The loading screen appears, textures are swapped, and you're ready to drift

If TexLoader is not installed, the extension detects this on startup and gracefully disables download features, so there's no crash risk.

## Security

Every download goes through multiple safety layers:

- **HTTPS only** — Plain HTTP manifest URLs are rejected
- **Base URL whitelist** — Only the official R2 bucket hostname is accepted; any redirect elsewhere is blocked
- **Path traversal protection** — `..`, absolute paths, and suspicious characters are filtered in manifest `packFile` / `previewFile` fields
- **Map name validation** — Only alphanumeric + underscore + space allowed (matches TexLoader folder convention)
- **XSS-lite sanitization** — HTML tags and `<script` blocked in pack name/author/description fields
- **SHA-256 verification** — Every downloaded `.zip` is rehashed and compared to the manifest value before install
- **Rate limiting** — 5-second cooldown and 50-downloads-per-session hard cap

## Submitting Your Pack

Want your texture pack in the community library? See the [TexLoader CXFR pack creation workflow](https://github.com/Silv3r25/TexLoader-CXFR#creating-texture-packs) to build your pack, then reach out on the [Discord](https://discord.gg/hpR8NvwUYK) to submit it for review. Accepted packs are added to the manifest and instantly appear in every user's in-game browser.

## Credits

- **S!LVER** — TexDownloader, TexLoader CXFR, TexDumper
- **djekanovic** — Scorpio CarX Texture Replacer
- Community pack creators

## Related Projects

- [TexLoader CXFR](https://github.com/Silv3r25/TexLoader-CXFR) — Main texture replacement mod (required)
- [TexDumper CXFR](https://github.com/Silv3r25/TexDumper-CXFR) — Extract map textures as reference for creating packs
- [Scorpio CarX Texture Replacer](https://github.com/djekanovic/carx-texture-replacer) — Desktop tool for batch texture replacement
