# Leaflet — Android app package

This folder is a complete installable web app (PWA):

```
index.html    the app
manifest.json  app name, icons, colors — what makes it "installable"
sw.js          service worker — caches the app so it opens offline
icons/         app icons (192, 512, 512 maskable, 64 favicon)
```

Notes are stored in IndexedDB on the device, so everything works fully offline
once the app has been opened once — no chat/account dependency, no server.

## 1. Try it locally first

From inside this folder:

```
python3 -m http.server 8080
```

Open `http://localhost:8080` in Chrome on your computer. Service workers and
installability work on `localhost` without HTTPS, so this is enough to confirm
everything (install prompt, offline reload, icons) before you deploy anywhere.

## 2. Put it on the open web (needed for your phone)

Android's "Add to Home screen" / install prompt requires the app to be served
over HTTPS from a real URL — `localhost` only works for testing on the same
machine. Any static host works; these are free and take a couple of minutes:

- **GitHub Pages** — push this folder to a repo, enable Pages on the `main`
  branch.
- **Netlify / Vercel / Cloudflare Pages** — drag-and-drop the folder in their
  dashboard, or connect a repo.

Once it's live, open the URL on your Android phone in Chrome. Chrome will
either show an "Install app" banner automatically, or you can tap the
**⋮ menu → Add to Home screen / Install app**. It then behaves like any other
installed app: its own icon, launches full-screen (no browser chrome), works
offline.

This alone satisfies "Android app" for most people — it's what Twitter/X,
Starbucks, and most retail apps' Android install prompts actually are under
the hood.

## 3. If you want an actual `.apk` / `.aab` file

Once the app is hosted (step 2), you can generate a real signed Android
package with no native build tools:

1. Go to **pwabuilder.com** and enter your hosted URL.
2. It reads `manifest.json`, scores the app's installability, and lets you
   generate an **Android package** — this produces a Trusted Web Activity
   app (a thin native wrapper that launches your PWA full-screen) as a
   downloadable `.apk` for sideloading or an `.aab` ready for the Play Store.
   It will generate a signing key for you if you don't have one — keep it
   somewhere safe, you'll need the same key for future updates.
3. Sideload the `.apk` on your phone (enable "Install unknown apps" for
   whichever app you download it through), or submit the `.aab` to Google
   Play if you want it store-listed.

If you'd rather control the native wrapper yourself instead of using
PWABuilder's hosted service, Google's **Bubblewrap** CLI does the same thing
locally (`npx @bubblewrap/cli init --manifest=https://yourdomain/manifest.json`),
but needs the Android SDK and a JDK installed on your machine.

## Updating the app later

Bump `CACHE_NAME` in `sw.js` (e.g. `leaflet-shell-v2`) whenever you change
`index.html`, so installed devices pick up the new version instead of serving
a stale cached copy.
