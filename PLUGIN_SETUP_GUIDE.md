# AdMob Mediation Plugins Setup Guide

Complete guide for fixing "Ads Not Showing" issues and configuring IronSource, Unity Ads, and AdMob mediation.

## Overview

This project includes two production-ready Expo Config Plugins:

1. **admob-mediation-plugin.js** - Configures AdMob with IronSource and Unity Ads mediation
2. **play-console-compliance-plugin.js** - Ensures Google Play Console compliance (Android 15+)

## Plugin Features

### admob-mediation-plugin.js

**What it does:**
- Injects Maven repositories for IronSource and Unity Ads
- Adds gradle dependencies for all mediation adapters
- Configures AndroidManifest.xml with AdMob App ID
- Adds IronSource and Unity Ads configuration
- Handles Unity Ads activities and intent filters
- Manages permissions (AD_ID, INTERNET, ACCESS_NETWORK_STATE)
- Removes policy-violating permissions

**Mediation Partners Configured:**
- Google AdMob (Primary)
- IronSource with AdMob Adapter
- Unity Ads
- Meta Audience Network
- AppLovin MAX

**Gradle Dependencies Added:**
```gradle
implementation 'com.ironsource.sdk:mediationsdk:8.1.0'
implementation 'com.ironsource.adapters:admobadapter:4.3.48'
implementation 'com.ironsource.adapters:unityadsadapter:4.3.41'
implementation 'com.google.android.gms:play-services-ads:22.6.0'
implementation 'com.google.android.ump:user-messaging-platform:2.1.0'
```

**Maven Repositories Added:**
```gradle
maven { url 'https://android-sdk.is.com/' }
maven { url 'https://unity3d.bintray.com/unity3d' }
```

### play-console-compliance-plugin.js

**What it does:**
- Enforces Android 15+ (API 35+) compliance
- Removes FOREGROUND_SERVICE_MEDIA_PLAYBACK permission
- Removes BOOT_COMPLETED receivers
- Ensures AD_ID permission is present
- Enables edge-to-edge display
- Enables back-invoked callback
- Supports Android 16 foldable devices
- Sets proper SDK versions

**Compliance Fixes:**
- ✅ compileSdkVersion: 35 (Android 15)
- ✅ targetSdkVersion: 35 (Android 15)
- ✅ minSdkVersion: 24
- ✅ AD_ID permission required
- ✅ No FOREGROUND_SERVICE_MEDIA_PLAYBACK
- ✅ No BOOT_COMPLETED receivers
- ✅ Edge-to-edge enabled
- ✅ Foldable device support

## Configuration Steps

### Step 1: Update IronSource and Unity Credentials

Edit `plugins/admob-mediation-plugin.js` and replace:

```javascript
const IRONSOURCE_APP_KEY = 'YOUR_IRONSOURCE_APP_KEY'; // Replace with actual key
const UNITY_GAME_ID = 'YOUR_UNITY_GAME_ID'; // Replace with actual Game ID
```

**How to get these values:**

**IronSource App Key:**
1. Go to [IronSource Dashboard](https://platform.ironsrc.com/)
2. Navigate to Setup → Apps
3. Select your app
4. Copy the App Key

**Unity Game ID:**
1. Go to [Unity Ads Dashboard](https://ads.unity.com/)
2. Navigate to Projects
3. Select your project
4. Copy the Game ID

### Step 2: Verify app.json Configuration

Ensure your `app.json` includes:

```json
{
  "expo": {
    "android": {
      "minSdkVersion": 24,
      "targetSdkVersion": 35,
      "compileSdkVersion": 35,
      "versionCode": 18,
      "googleServicesFile": "./google-services.json",
      "edgeToEdgeEnabled": true,
      "permissions": [
        "POST_NOTIFICATIONS",
        "com.google.android.gms.permission.AD_ID",
        "INTERNET",
        "ACCESS_NETWORK_STATE"
      ]
    },
    "plugins": [
      "expo-router",
      [
        "react-native-google-mobile-ads",
        {
          "androidAppId": "ca-app-pub-3617790148719581~6917999502",
          "iosAppId": "ca-app-pub-3617790148719581~6917999502"
        }
      ],
      "./plugins/admob-mediation-plugin.js",
      "./plugins/play-console-compliance-plugin.js",
      ...
    ]
  }
}
```

### Step 3: Update AdMob Configuration

Edit `lib/admob.ts` and replace test Ad Unit IDs with production IDs:

```typescript
export const AD_UNIT_IDS = {
  BANNER_ANDROID: 'YOUR_PRODUCTION_BANNER_ID',
  BANNER_IOS: 'YOUR_PRODUCTION_BANNER_ID_IOS',
  INTERSTITIAL_ANDROID: 'YOUR_PRODUCTION_INTERSTITIAL_ID',
  INTERSTITIAL_IOS: 'YOUR_PRODUCTION_INTERSTITIAL_ID_IOS',
  // ... other ad types
};
```

### Step 4: Build for Production

```bash
# Prebuild native code
eas build --platform android --profile production --prebuild

# Or build directly
NODE_ENV=production eas build --platform android --profile production
```

## Troubleshooting

### Ads Still Not Showing

**Check 1: Verify Ad Unit IDs**
- Ensure you're using production Ad Unit IDs (not test IDs)
- Ad Unit IDs must be approved in AdMob Console

**Check 2: Verify Permissions**
- Ensure AD_ID permission is in AndroidManifest.xml
- Check device has INTERNET permission

**Check 3: Check Mediation Configuration**
- Verify IronSource App Key is correct
- Verify Unity Game ID is correct
- Check adapters are enabled in AdMob Console

**Check 4: Verify Test Device**
- Add your device to test devices in AdMob Console
- Check device logs for AdMob errors

### Build Errors

**Error: "minSdkVersion mismatch"**
- Ensure minSdkVersion is 24 or higher
- Check all dependencies support your minSdkVersion

**Error: "Maven repository not found"**
- Verify internet connection
- Check Maven URLs are correct:
  - `https://android-sdk.is.com/`
  - `https://unity3d.bintray.com/unity3d`

**Error: "Duplicate permissions"**
- The plugins use `.some()` to check for existing permissions
- Should not add duplicates
- If duplicates appear, run `eas build --platform android --profile production --prebuild` to regenerate

### Manifest Issues

**Issue: Activities not appearing**
- Unity Ads activities are automatically added by the plugin
- Check AndroidManifest.xml for:
  - `com.unity3d.ads.adunit.AdUnitActivity`
  - `com.unity3d.ads.adunit.WebViewActivity`

**Issue: Permissions not injected**
- Plugin uses safe checking with `.some()` method
- Permissions are added if not already present
- Check AndroidManifest.xml for:
  - `com.google.android.gms.permission.AD_ID`
  - `android.permission.INTERNET`
  - `android.permission.ACCESS_NETWORK_STATE`

## Plugin Code Structure

### admob-mediation-plugin.js

```javascript
withIronSourceMavenRepo()     // Add Maven repos
  ↓
withMediationDependencies()   // Add gradle dependencies
  ↓
withAdMobManifestConfig()     // Configure manifest
  ↓
withAdMobMediationComplete()  // Compose all steps
```

### play-console-compliance-plugin.js

```javascript
withAndroidManifest()         // Fix permissions, receivers, attributes
  ↓
withAppBuildGradle()          // Set SDK versions
  ↓
withPlayConsoleCompliance()   // Compose all steps
```

## Plugin Execution Order

The plugins execute in this order:

1. `expo-router`
2. `react-native-google-mobile-ads` (official plugin)
3. `admob-mediation-plugin.js` (custom - adds repos and dependencies)
4. `play-console-compliance-plugin.js` (custom - ensures compliance)
5. `expo-splash-screen`
6. `expo-build-properties`
7. `expo-asset`

**Important:** Custom plugins run AFTER the official plugin, so they can override and enhance its configuration.

## Verification Checklist

Before submitting to Google Play Console:

- [ ] Plugin syntax verified (both .js files)
- [ ] app.json valid JSON
- [ ] Android SDK versions: min=24, target=35, compile=35
- [ ] IronSource App Key updated
- [ ] Unity Game ID updated
- [ ] Ad Unit IDs are production IDs
- [ ] Permissions include AD_ID
- [ ] No FOREGROUND_SERVICE_MEDIA_PLAYBACK permission
- [ ] No BOOT_COMPLETED receivers
- [ ] Edge-to-edge enabled
- [ ] Build succeeds with `eas build --platform android --profile production --prebuild`
- [ ] APK/AAB generated successfully
- [ ] Tested on physical device with test ads

## Testing Ads

### Using Test Ad Unit IDs

The app comes with Google's test Ad Unit IDs. To test:

1. Keep test Ad Unit IDs in `lib/admob.ts`
2. Build and install on device
3. Ads should show immediately (no approval needed)

### Switching to Production

1. Update Ad Unit IDs in `lib/admob.ts` with production IDs
2. Ensure Ad Units are approved in AdMob Console
3. Rebuild and test
4. Submit to Google Play Console

## Support

For issues:

1. Check plugin logs: `eas build --platform android --profile production --prebuild --verbose`
2. Review AndroidManifest.xml in generated native code
3. Check build.gradle for dependency conflicts
4. Verify AdMob Console settings
5. Check IronSource and Unity Ads dashboards

## Additional Resources

- [Expo Config Plugins Documentation](https://docs.expo.dev/config-plugins/introduction/)
- [Google Mobile Ads SDK](https://developers.google.com/admob)
- [IronSource Documentation](https://developers.ironsrc.com/)
- [Unity Ads Documentation](https://docs.unity.com/ads/)
- [Google Play Console Policies](https://play.google.com/about/developer-content-policy/)
- [Android 15 Behavior Changes](https://developer.android.com/about/versions/15)
