# Android TV APK Build Instructions

## Overview
This project now includes a GitHub Actions workflow to build Android TV-compatible APKs. Due to toolchain limitations in the Termux environment, the build is performed via CI/CD.

## What's Been Configured

### 1. GitHub Actions Workflow
- **File**: `.github/workflows/android-tv-build.yaml`
- **Features**:
  - Automated Android SDK and NDK setup
  - ARM64 target compilation (`aarch64-linux-android`)
  - Android TV manifest configuration
  - Production frontend build
  - APK and debug symbols artifact upload

### 2. Android TV Configuration
The workflow automatically configures the Android manifest with:
- Leanback (TV) feature declaration
- Touchscreen optional setting
- TV launcher intent filter (`LEANBACK_LAUNCHER`)
- Appropriate theme settings

## How to Build

### Option 1: Trigger Workflow via GitHub UI
1. Push the changes to your fork:
   ```bash
   # If you have a fork, add it as a remote
   git remote add fork https://github.com/YOUR_USERNAME/popcorntime.git
   git push fork dev
   ```

2. Go to your fork on GitHub
3. Navigate to **Actions** tab
4. Select **Android TV APK Build** workflow
5. Click **Run workflow** button
6. Wait for the build to complete (~10-15 minutes)
7. Download the APK from the **Artifacts** section

### Option 2: Trigger via GitHub CLI
```bash
# Install GitHub CLI if not already installed
gh workflow run android-tv-build.yaml

# Monitor the workflow
gh run list --workflow=android-tv-build.yaml

# Download artifacts once complete
gh run download <RUN_ID>
```

### Option 3: Automatic Trigger
The workflow automatically triggers on:
- Pushes to `dev` branch
- Changes to `apps/`, `crates/`, or `packages/` directories

## Build Artifacts

After a successful build, you'll find:
- **android-tv-apk**: The signed/unsigned APK file
- **android-debug-symbols**: Native debug symbols (optional)

## Installing on Android TV

1. Download the APK from GitHub Actions artifacts
2. Transfer to your Android TV device using:
   ```bash
   adb connect YOUR_TV_IP
   adb install -r app-release.apk
   ```
3. The app will appear in your TV's app launcher

## Signing Configuration (Optional)

For signed releases, add these secrets to your GitHub repository:
- `TAURI_SIGNING_PRIVATE_KEY`: Your Android signing key
- `TAURI_SIGNING_PRIVATE_KEY_PASSWORD`: Key password

## Troubleshooting

### Build Fails
- Check the Actions logs for specific errors
- Ensure all dependencies in `package.json` are up to date
- Verify Rust code compiles successfully

### APK Won't Install
- Enable "Install from Unknown Sources" on your TV
- Check the Android version compatibility
- Verify the APK is for ARM64 architecture

### App Doesn't Appear in TV Launcher
- Verify the manifest has the `LEANBACK_LAUNCHER` intent filter
- Check that `android.software.leanback` feature is declared as required

## Manual Build (On x86_64 Machine)

If you have access to a standard development machine:

```bash
# Install prerequisites
# - Rust via rustup
# - Node.js LTS
# - Android Studio with SDK/NDK
# - Configure ANDROID_HOME environment variable

# Clone the repository
git clone https://github.com/popcorntime/popcorntime.git
cd popcorntime

# Install dependencies
pnpm install

# Initialize Android target (one-time)
pnpm tauri android init

# Configure for Android TV (edit manifest manually)
# See workflow file for exact configuration

# Build the APK
pnpm prod:build-for-tauri
pnpm tauri android build -- --target aarch64-linux-android

# Find the APK
# Location: crates/popcorntime-tauri/gen/android/app/build/outputs/apk/
```

## Local Commit Status

The workflow has been committed locally:
- Branch: `dev`
- Commit: `feat: add GitHub Actions workflow for Android TV APK build`
- Status: Ready to push (pending repository permissions)

## Next Steps

1. Push to your fork or request merge to main repository
2. Trigger the workflow
3. Download and test the APK
4. Report any issues or improvements needed
