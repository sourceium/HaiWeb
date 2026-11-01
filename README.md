# HelloWeb

Turn a website or an HTML page into a real, **debug-signed** Android APK, fully on the phone.
Open the app, tap **New app**, walk through the wizard, then **Build APK** and **Install**.

## Wizard (9 steps)
1. **App info**: name, package (suggested from the name), version code and name
2. **Content source**: Web URL, or paste / import an HTML page that is bundled in the app
3. **Display**: orientation, page theme, fullscreen, keep screen on, JavaScript, zoom
4. **Colors**: presets, live preview, 7 tunable colors (hex, RGB sliders, palette)
5. **Permissions**: searchable list, declared in the generated app's manifest
6. **App icon**: default, picked image, or generated letter (3 shapes)
7. **Splash screen**: colors, logo (icon / image / none), app name, duration
8. **Navigation**: toolbar, pull to refresh, back behaviour, exit confirmation, progress bar,
   external links, bottom tabs (2 to 4)
9. **Review**: summary with Edit shortcuts, then Build APK

Material 3 look, forest-green palette (light + dark) in `res/values*/colors.xml`, platform views only
(no libraries).

## How the build works
No compiler, AAPT2 or library is needed:
1. `assets/shell.apk` is a ready-made WebView app. Its manifest holds placeholders for package, label, version.
2. `AxmlPatcher` rewrites the compiled manifest (string pool, versionCode) and **adds one
   `uses-permission` element per chosen permission**.
3. `ApkPatcher` rebuilds the zip: manifest, icon, `assets/wbconfig.json` (all wizard choices, schema 2),
   `assets/splash.png` and `assets/www/index.html` when used.
4. `DebugKey` makes an RSA-2048 debug key once per device; `V2Signer` adds an APK Signature Scheme v2 block.
5. `ApkProvider` hands the file to the system installer.

## Important: the shell must read the new config
`shell.apk` bundled in this project is the **old** shell: it only reads `url`.
With it, name, package, version, icon, URL, HTML (loaded as a bundled page) and permission declarations work;
splash, colors, toolbar, tabs, orientation and the other options are written to the config but ignored.
The new runtime is in `project/shell/`. Build it and replace `app/src/main/assets/shell.apk`
(steps in `shell/README.md`).

## Update (v1.0)
- Fullscreen is on by default.
- `shell/` has the improved runtime: keyboard fix in fullscreen, fullscreen video, in-app downloads, cookies, wide viewport, pause/resume.
- `assets/shell.apk` is still the OLD shell. Build `HelloWeb-Shell.caproj` (or `shell/`) as a debug APK and replace it.

## Notes
- Every app is signed with this device's debug key, so rebuilding the same package updates the earlier install.
- Output folder: Android/data/com.HelloWeb/files/apk/
- Not Play Store ready (debug signature, debuggable).
