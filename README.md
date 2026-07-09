# Lab Inventory App (v0.1 pilot -- HTML/Capacitor, local-only)

A single-page HTML/JS app (`www/index.html`, plus two small bundled
libraries for QR generation and scanning) wrapped by Capacitor into a
real Android app, built into an APK by GitHub Actions -- no Android
Studio, no Python, nothing to install locally except a GitHub account.

## What works right now

- New unit entry: photo, product type (fridge / AC / chest freezer /
  water dispenser), the BOM-level spec fields we defined per type,
  remarks.
- QR code generated on save, scoped to that unit forever (scanning it
  always resolves the same record -- the physical sticker never needs
  reprinting).
- Scan a QR (via a photo of it -- no live camera stream yet, see
  "Not yet built" below) to open a unit and: edit details, assign to
  a test chamber/station (with slot-occupancy checking), move to
  storage, link an AC IDU to its ODU, discard, or view full history.
- History: every field change ever made, logged locally.

**Data currently lives only in the device's local storage** -- there
is no server yet (that's next). Each device is self-contained for now;
nothing syncs between devices until we wire the backend back in.

## How to get an APK

1. Create a new GitHub repository and push this whole folder to it
   (everything except `node_modules/` and `android/app/build/`, which
   `.gitignore` already excludes).

   ```bash
   git init
   git add .
   git commit -m "Initial lab inventory app"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<repo-name>.git
   git push -u origin main
   ```

2. Go to the repo on GitHub -> the **Actions** tab. A workflow called
   "Build APK" will already be running (it triggers automatically on
   push, or you can click "Run workflow" manually).

3. Once it finishes (green check, a few minutes), open that run and
   scroll to **Artifacts** -- download `lab-inventory-debug-apk`. It's
   a zip containing `app-debug.apk`.

4. Copy the APK to your Android phone/tablet (email it to yourself,
   Google Drive, USB, whatever's easiest) and install it. You'll need
   to allow "install from unknown sources" for whichever app you used
   to open it, since this isn't distributed through the Play Store.

No local Android SDK, no Gradle, no Java setup on your PC -- GitHub's
runner does all of that.

## Editing the app

Everything user-facing is in `www/index.html` -- structure, styling,
and logic all in one file, same pattern as RefDesign. Edit it, push to
`main`, and the next Actions run produces an updated APK automatically.
`www/qrcode.lib.js` and `www/jsqr.lib.js` are vendored libraries (MIT
licensed) -- don't need to touch those.

## Not yet built (next steps, in likely order)

- **Live camera QR scanning** -- right now scanning works by taking a
  photo and decoding it, which is a bit clunky. A Capacitor camera/
  barcode plugin would give a live viewfinder instead; needs adding a
  native plugin dependency, which is a bit more involved than the pure
  web code here.
- **Backend + sync** -- this app is currently local-only per device.
  Bringing back a server (whether that ends up being the FastAPI one
  we built, or something else) and wiring `fetch()` calls into this
  same HTML file is the next big piece, using the field-level diff +
  conflict logic already designed.
- **App icon / splash screen** -- currently using Capacitor's defaults.
- **Release signing** -- this workflow builds a debug APK (fine for
  installing on your own test devices). A signed release build needs
  a keystore, which we'd add as a GitHub Actions secret when you're
  ready to distribute more broadly.
