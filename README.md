# SmatBMI — Expo + EAS + AdMob

A tiny BMI calculator you can ship to Google Play. Uses `react-native-google-mobile-ads` for banners (test IDs by default).

## Quick start (CMD-friendly)

```bat
:: 1) Install tooling
npm i -g expo-cli eas-cli

:: 2) Install deps
npm install

:: 3) (First time) configure EAS
eas login
eas build:configure

:: 4) Build dev APK (with test ads)
npm run build:android:dev

:: 5) Install APK on your phone and test
```

## Prepare for Play Store

1. Create your **AdMob** app + **Banner Ad Unit** in the AdMob console.
2. In `app.json`, replace the **androidAppId** (currently a Google test App ID) with your real ID.
3. In `components/BannerBlock.js`, replace `PROD_BANNER_ID` with your real Banner Ad Unit ID. Keep `TestIds.BANNER` in dev.
4. Update the Android package name in `app.json` at `expo.android.package` — e.g. `com.yourbrand.smatbmi`.
5. Replace the Privacy Policy URL inside `App.js` (and host `/privacy-policy.html` in this repo on GitHub Pages).
6. Build a **release bundle** and submit:
   ```bat
   npm run build:android
   eas submit -p android --latest
   ```

## Policies & data safety
- Use **Google test IDs** during development to avoid invalid traffic.
- On Play Console, tick **“Contains ads”** and complete **Data Safety** for Google Mobile Ads SDK.
- Show a clear **privacy policy** in-app and on the store listing.

## Tech
- Expo SDK 53, RN 0.79, Hermès
- `react-native-google-mobile-ads` (AdMob)
- AsyncStorage for simple prefs


## Stamp your IDs via EAS Secrets (recommended)

```bat
:: Replace values with your real IDs
eas secret:create --name ANDROID_PACKAGE --value com.dentech.smatbmi
eas secret:create --name ADMOB_ANDROID_APP_ID --value ca-app-pub-XXXX~YYYY
eas secret:create --name ADMOB_BANNER_UNIT_ID --value ca-app-pub-XXXX/ZZZZ
```

These are read in **app.config.js** at build-time to configure:
- Android package name
- AdMob App ID (plugin)
- AdMob Banner Unit ID (runtime via `Constants.expoConfig.extra`)

You can also set them as local env vars when running locally:
```bat
set ANDROID_PACKAGE=com.dentech.smatbmi
set ADMOB_ANDROID_APP_ID=ca-app-pub-XXXX~YYYY
set ADMOB_BANNER_UNIT_ID=ca-app-pub-XXXX/ZZZZ
npm run build:android
```


## AdMob test-only by default

This project is **locked to Google's official Android test IDs** out of the box:

- **App ID** (plugin default): `ca-app-pub-3940256099942544~3347511713`
- **Banner Unit ID** (runtime default): `ca-app-pub-3940256099942544/6300978111`

`BannerBlock` always uses the ID supplied via `extra.ADMOB_BANNER_UNIT_ID`; if none is supplied,
it falls back to the **test ID** above. When ready for real ads, set env/EAS secrets:

```bat
eas secret:create --name ADMOB_ANDROID_APP_ID --value ca-app-pub-XXXX~YYYY
eas secret:create --name ADMOB_BANNER_UNIT_ID --value ca-app-pub-XXXX/ZZZZ
```

Or locally:

```bat
set ADMOB_ANDROID_APP_ID=ca-app-pub-XXXX~YYYY
set ADMOB_BANNER_UNIT_ID=ca-app-pub-XXXX/ZZZZ
npm run build:android
```



## Google Play Service Account key (EAS Submit)

1) In **Play Console → Setup → API access**, link a Google Cloud project and create a **service account**, then create a **JSON key**.
2) In **Users & permissions**, grant the service account access to your app (at least the release/publishing permissions).
3) Save the JSON in `./keys/google-play-service-account.json` (ignored by git/EAS).
4) Submit with:
```bat
eas submit -p android --profile production --latest
```
The `eas.json` already points to `./keys/google-play-service-account.json` under `submit.production.android.serviceAccountKeyPath`.
