<p align="center">
<img alt="LumaFin" src="docs/branding/lumafin-wordmark.png" width="520"/>
</p>
<h3 align="center">LumaFin for Samsung Tizen TVs</h3>

---

LumaFin is a personal fork of [jellyfin-tizen](https://github.com/jellyfin/jellyfin-tizen), the wrapper that packages the
Jellyfin web client as a Samsung Smart TV app. It bundles [LumaFin Web](https://github.com/bagodees/lumafin-web) (a
fork of jellyfin-web) and is the Tizen sibling of [LumaFin-AndroidTV](https://github.com/bagodees/LumaFin-AndroidTV).
It works with any Jellyfin server and is not an officially supported Jellyfin project. See [LICENSE](LICENSE).

## What's different from upstream

- **LumaFin branding** - name, launcher icon, splash and header logo. It has its own app id, so it installs alongside
  the official Jellyfin app instead of replacing it.
- **Landscape, thumbnail-first artwork** - cards use Thumb, then Backdrop, then Primary artwork as uniform 16:9 tiles.
- **Rating badges** - critic and community ratings on movie and series cards.
- **More home sections** - Latest Movies/Shows, Because You Watched, Collections, Watch Again, Genres, Favorites, a
  combined Continue Watching / Next Up row, and Recently Added / Latest rows for movies, shows, albums, artists, music
  videos, books and audiobooks. "Latest" rows skip items that haven't premiered yet. The layout is stored on the TV, so
  a session refresh or another Jellyfin client can't revert it.

## Building

Requirements: [Tizen Studio](https://developer.samsung.com/smarttv/develop/getting-started/setting-up-sdk/installing-tv-sdk.html)
with the TV extension and Certificate Manager, Git, and Node.js 24 (LumaFin Web's dependencies need `^22.11`, `^24.11`
or `>=26`).

1. Build LumaFin Web:
   ```sh
   git clone -b lumafin-main https://github.com/bagodees/lumafin-web.git
   cd lumafin-web
   npm ci --no-audit
   USE_SYSTEM_FONTS=1 npm run build:production
   ```
2. Prepare the interface (copies `lumafin-web/dist` into `www/`):
   ```sh
   cd LumaFin-Tizen
   JELLYFIN_WEB_DIR=../lumafin-web/dist npm ci --no-audit
   ```
3. Build and sign the widget with your certificate profile:
   ```sh
   tizen build-web -e ".*" -e gulpfile.babel.js -e README.md -e "node_modules/*" -e "package*.json" -e "yarn.lock" -e docs
   tizen package -t wgt -s <YOUR_PROFILE> -o . -- .buildResult
   ```

Tizen names the output `LumaFin.wgt`.

## Installing

Enable Developer Mode on the TV, connect with `sdb connect <TV_IP>` (or Tizen Studio's Device Manager), then:

```sh
tizen install -n LumaFin.wgt -t <TV_NAME>
```

Notes:

- The Tizen emulator's `tv-samsung` image rejects plain Tizen certificates
  (`Invalid certificate chain`). Sign with a Samsung certificate profile (needs a Samsung account and the Samsung
  Certificate Extension) for the emulator; a plain Tizen profile has worked for sideloading onto a real TV.
- Reinstalling over an existing install keeps your login. Uninstalling first clears it.

## Branching

`lumafin-main` is the default, active branch and carries all LumaFin changes. `master` tracks upstream
`jellyfin/jellyfin-tizen` unmodified.
