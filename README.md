# 4TViscosity — Bike 4T Engine Oil Blend Calculator

An offline Android app that helps you **mix two or more 4‑stroke motorcycle engine oils** of different viscosity grades to fill your sump to its exact capacity, and estimates the **viscosity of the resulting blend**.

Enter your engine's oil capacity, pick oils from a built‑in database of real product grades, set how much of each you're adding, and the app shows the running total, how much is left to fill, and the estimated blended viscosity at 100 °C — mapped to the nearest SAE weight (30 / 40 / 50 / 60).

The whole calculator runs locally in a `WebView`; **no internet connection is required** after install.

---

## Features

- **Fill‑to‑capacity tracker** — set total capacity (litres or ml), add oils with quantities, and see Added / Capacity / To‑fill at a glance with a colour‑coded fill bar and status (under / full / over).
- **Real datasheet viscosities** — each grade carries its kinematic viscosity at 100 °C (cSt, ASTM D445) taken from the manufacturer's product data sheet.
- **Blend estimation** — the blended warm viscosity is computed with the **Refutas equation** (viscosity‑blending index), weighted by volume fraction.
- **SAE weight‑band legend** — shows the SAE J300 100 °C viscosity ranges and highlights the band your blend falls into.
- **Light & dark theme** — follows the system theme automatically.

---

## Oil database

Viscosities are typical values from each maker's product data sheet (ASTM D445 @ 100 °C). Numbers vary by market and formulation revision.

| Brand / Range | Type | Grades (cSt @ 100 °C) |
|---|---|---|
| **Motul 7100 4T** | 100% Synthetic (Ester) | 10W‑30 (10.5) · 10W‑40 (13.2) · 10W‑50 (17.9) · 15W‑50 (18.0) · 10W‑60 (23.9) |
| **Motul 5100 4T** | Semi‑Synthetic (Technosynthèse) | 10W‑40 (14.0) · 15W‑50 (18.3) |
| **Castrol POWER1 Ultimate 4T** | Full Synthetic | 10W‑40 (14.1) · 10W‑60 (23.4) |
| **Castrol POWER1 4T** | Semi‑Synthetic | 10W‑40 (13.5) |
| **Shell Advance Ultra 4T** | 100% Synthetic (PurePlus) | 10W‑40 (14.2) · 15W‑50 (20.5) |
| **TVS TRU4 Synthetic** | Full Synthetic | 10W‑30 (10.19) |

> **Note on TVS TRU4 Synthetic:** TVS does not publish a datasheet with a kinematic‑viscosity figure. The value used (10.19 cSt @ 100 °C, 67.35 @ 40 °C) comes from an independent lab test ("The Sportztourer"). TVS TRU4 Synthetic is marketed as API SL / JASO MA2, 10W‑30, and is reportedly manufactured by Bharat Petroleum.

---

## How the blend math works

Kinematic viscosities do **not** average linearly, so the app uses the industry‑standard **Refutas method**:

1. Convert each oil's viscosity `v` (cSt) to a Viscosity Blending Index:
   `VBI = 14.534 · ln(ln(v + 0.8)) + 10.975`
2. Blend by volume fraction `xᵢ`:
   `VBI_blend = Σ (xᵢ · VBIᵢ)`
3. Convert back to kinematic viscosity:
   `v_blend = exp(exp((VBI_blend − 10.975) / 14.534)) − 0.8`

The result is matched to the nearest SAE J300 high‑temperature grade:

| SAE grade | KV @ 100 °C (cSt) |
|---|---|
| SAE 30 | 9.3 – 12.5 |
| SAE 40 | 12.5 – 16.3 |
| SAE 50 | 16.3 – 21.9 |
| SAE 60 | 21.9 – 26.1 |

---

## Install (prebuilt APK)

A ready‑to‑install debug build is included: **[`ViscosityCalculator-v1.0.apk`](ViscosityCalculator-v1.0.apk)**

1. Download the APK to your Android phone (Android 8.0 / API 26 or newer).
2. Open it — Android will ask to allow installing from this source; enable it, then tap **Install**.
3. Launch **Viscosity Calculator**.

> The APK is **debug‑signed**, so Android flags it as coming from an "unknown source." That is normal for a sideloaded app. For public / Play Store distribution, build a release APK signed with your own keystore (see below).

---

## Build from source

Requirements: JDK 17+ and the Android SDK (platform 34, build‑tools 34). No local Gradle install needed — the Gradle wrapper is included.

```bash
# point the build at your Android SDK (or set the ANDROID_HOME env var)
echo "sdk.dir=/path/to/Android/sdk" > local.properties

# debug build
./gradlew assembleDebug
#   -> app/build/outputs/apk/debug/app-debug.apk

# release build (unsigned until you configure signing)
./gradlew assembleRelease
```

Opening the project in **Android Studio** works too — just let it sync Gradle and hit Run.

---

## Project structure

```
.
├── app/
│   ├── build.gradle
│   └── src/main/
│       ├── AndroidManifest.xml
│       ├── assets/index.html        # the entire calculator (HTML/CSS/JS)
│       ├── java/.../MainActivity.java   # thin WebView host
│       └── res/                     # launcher icon, strings
├── build.gradle                     # root; AGP 8.5.2
├── settings.gradle
├── gradle/wrapper/                  # Gradle 8.9 wrapper
└── ViscosityCalculator-v1.0.apk     # prebuilt debug APK
```

The app is intentionally a lightweight native shell around a single self‑contained `index.html`. To change the oil data or UI, edit `app/src/main/assets/index.html` and rebuild — no other code changes needed.

---

## Disclaimer

This tool estimates the **warm (100 °C) viscosity** of a mixture. It does **not**:

- predict the cold‑start **"W"** (winter) rating,
- predict the **JASO MA/MA2** wet‑clutch friction rating, or
- judge **additive‑package compatibility** between different oils or brands.

Manufacturers (Motul, Castrol, Shell) state their 4T oils may be mixed, but blending full‑ and semi‑synthetic oils averages their additive packs. **For anything critical, follow your motorcycle manufacturer's recommended single grade.** Use at your own risk.

---

## License

Released under the [MIT License](LICENSE).
