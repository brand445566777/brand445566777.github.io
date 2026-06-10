# Pakistan Emergency Helpline - Release Build Instructions

## Overview

Your app has been fully configured with Google AdMob integration, mediation adapters, and Play Console compliance fixes. The native Android project has been generated and is ready for building.

## Prerequisites

Before building locally, ensure you have:

1. **Node.js 18+** and **npm** or **pnpm**
2. **Java 17 or higher** (required for Android Gradle plugin)
3. **Android SDK** (API 35 - Android 15)
4. **Android NDK 27.1.12297006**
5. **Gradle** (will be downloaded automatically)

### Install Java 17

**macOS:**
```bash
brew install openjdk@17
export JAVA_HOME=$(/usr/libexec/java_home -v 17)
```

**Ubuntu/Linux:**
```bash
sudo apt-get install openjdk-17-jdk
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
```

**Windows:**
- Download from [Oracle JDK 17](https://www.oracle.com/java/technologies/javase/jdk17-archive-downloads.html)
- Set `JAVA_HOME` environment variable

### Install Android SDK

Use Android Studio or command-line tools:
```bash
# Using Android Studio: Preferences → SDK Manager → Install API 35

# Or using command-line tools:
sdkmanager "platforms;android-35"
sdkmanager "build-tools;36.0.0"
sdkmanager "ndk;27.1.12297006"
```

## Build Steps

### 1. Navigate to Project Directory

```bash
cd pakistan-emergency-helpline
```

### 2. Install Dependencies

```bash
npm install
# or
pnpm install
```

### 3. Generate Native Android Project

```bash
npx expo prebuild --clean --platform android
```

This creates the `android/` directory with all native configuration.

### 4. Build Release APK (Faster for Testing)

```bash
cd android
./gradlew assembleRelease
```

Output: `android/app/build/outputs/apk/release/app-release.apk`

### 5. Build Release AAB (For Google Play Store - RECOMMENDED)

```bash
cd android
./gradlew bundleRelease
```

Output: `android/app/build/outputs/bundle/release/app-release.aab`

### Environment Variables

If you encounter Java version issues, explicitly set:

```bash
export JAVA_HOME=/path/to/java17
export ANDROID_SDK_ROOT=/path/to/android/sdk
export ANDROID_NDK_ROOT=/path/to/android/ndk
```

## Build Output Locations

After successful build:

**APK (for direct installation):**
```
android/app/build/outputs/apk/release/app-release.apk
```

**AAB (for Google Play Store):**
```
android/app/build/outputs/bundle/release/app-release.aab
```

## Signing the Release Build

### Option 1: Use Expo's Built-in Signing (Recommended)

```bash
eas build --platform android --type app-bundle
```

This uses Expo's managed signing and generates a signed .aab directly.

### Option 2: Manual Signing

If you have a keystore file:

```bash
jarsigner -verbose -sigalg SHA256withRSA -digestalg SHA-256 \
  -keystore your-keystore.jks \
  android/app/build/outputs/bundle/release/app-release.aab \
  your-key-alias
```

## Troubleshooting

### "Android Gradle plugin requires Java 17"

```bash
export JAVA_HOME=/path/to/java17
./gradlew assembleRelease
```

### "SDK location not found"

Create `android/local.properties`:
```properties
sdk.dir=/path/to/android/sdk
ndk.dir=/path/to/android/ndk
```

### Build Hangs or Times Out

- Increase Gradle heap: `export GRADLE_OPTS="-Xmx4g"`
- Clear Gradle cache: `./gradlew clean`
- Disable Gradle daemon: `./gradlew --no-daemon assembleRelease`

### Out of Memory

```bash
export GRADLE_OPTS="-Xmx4g -XX:MaxMetaspaceSize=1024m"
./gradlew bundleRelease
```

## What's Included in This Build

### AdMob Integration
- ✅ Google Mobile Ads SDK
- ✅ Meta (Facebook) Audience Network adapter
- ✅ Unity Ads adapter
- ✅ AppLovin MAX adapter
- ✅ IronSource adapter
- ✅ Google UMP SDK for consent management
- ✅ Real AdMob App ID: `ca-app-pub-3617790148719581~6917999502`
- ✅ Banner Ad Unit: `ca-app-pub-3617790148719581/8154584193`
- ✅ Native Ad Unit: `ca-app-pub-3617790148719581/2428090151`

### Play Console Compliance
- ✅ Removed FOREGROUND_SERVICE_MEDIA_PLAYBACK permission
- ✅ Removed BOOT_COMPLETED receiver
- ✅ Added com.google.android.gms.permission.AD_ID
- ✅ Android 15 Edge-to-Edge support
- ✅ Android 16 resizability ready

### Components
- ✅ BannerAd.tsx - Bottom banner ads with error handling
- ✅ NativeAdvancedAd.tsx - Native ads for FlatList integration
- ✅ UMP Context - GDPR/CCPA consent management

## Upload to Google Play Store

1. Go to [Google Play Console](https://play.google.com/console)
2. Create new app or select existing
3. Go to **Release → Production**
4. Upload the signed `.aab` file
5. Fill in store listing, privacy policy, content rating
6. Submit for review

## Support

For issues with:
- **Expo**: https://docs.expo.dev
- **Android Gradle Plugin**: https://developer.android.com/build
- **Google AdMob**: https://support.google.com/admob

## Next Steps

1. Build locally using instructions above
2. Test on Android device or emulator
3. Sign with your production keystore
4. Upload to Google Play Store
5. Monitor AdMob dashboard for ad performance

---

**Build Date:** April 2, 2026
**Expo SDK:** 54
**React Native:** 0.81.5
**Android Target:** API 35 (Android 15)
