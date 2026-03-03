# Setting Up a New Project for CI/CD

This guide walks through adding CI/CD mobile/desktop builds to a new React/Vite project.

## Prerequisites

- The project is a React/Vite web app
- You have access to Apple Developer and Google Play Console
- Org-level GitHub secrets are configured (see [SECRETS_REFERENCE.md](./SECRETS_REFERENCE.md))

## Step 1: Add Capacitor to Your Project

```bash
# Install Capacitor
npm install @capacitor/core @capacitor/cli @capacitor/android @capacitor/ios

# Initialize Capacitor
npx cap init "YourAppName" "com.yourorg.yourapp" --web-dir dist

# Add platforms
npx cap add android
npx cap add ios
```

Make sure your `package.json` has a `build:web` script that outputs to `dist/`.

## Step 2: Register Your App

### Apple App Store Connect
1. Go to [App Store Connect](https://appstoreconnect.apple.com/)
2. My Apps → "+" → New App
3. Set bundle ID to match your `capacitor.config.ts` `appId`
4. Fill in required metadata

### Google Play Console
1. Go to [Google Play Console](https://play.google.com/console/)
2. Create app → fill in details
3. Set up internal testing track
4. Note: First upload must be done before the API can manage releases

## Step 3: iOS Code Signing with Fastlane Match

```bash
# On a Mac with Fastlane installed
fastlane match appstore -a com.yourorg.yourapp

# This will:
# - Generate/fetch distribution certificate
# - Create provisioning profile
# - Store encrypted in the Pixygon/certificates repo
```

## Step 4: Android Keystore

Add a key alias for your app to the shared `pixygon-apps.jks` keystore:

```bash
keytool -genkeypair \
  -alias yourapp \
  -keyalg RSA -keysize 2048 \
  -validity 10000 \
  -keystore pixygon-apps.jks \
  -storepass "$KEYSTORE_PASSWORD" \
  -keypass "$KEY_PASSWORD" \
  -dname "CN=YourApp, O=Pixygon, C=NO"
```

Then update the base64 secret:
```bash
base64 -i pixygon-apps.jks | pbcopy
# Update ANDROID_KEYSTORE_BASE64 org secret
```

## Step 5: Copy Workflow Template

Copy the template workflow to your project:

```bash
# For mobile builds
cp templates/capacitor-workflow.yml your-project/.github/workflows/build-mobile.yml

# For desktop builds (if needed)
cp templates/electron-workflow.yml your-project/.github/workflows/build-desktop.yml

# For release promotion (if needed)
cp templates/promote-workflow.yml your-project/.github/workflows/promote-release.yml
```

## Step 6: Update the 4 Values

Edit your copied workflow and change:

| Value | Example | Description |
|-------|---------|-------------|
| `app-id` | `com.wattoshi.app` | Bundle ID / application ID |
| `app-name` | `Wattoshi` | Human-readable name |
| `android-key-alias` | `wattoshi` | Keystore alias from Step 4 |
| `cdn-path` | `wattoshi-mobile` | CDN folder name |

## Step 7: Test

```bash
# Test with workflow_dispatch first
# Go to Actions tab in GitHub → select workflow → Run workflow

# Then test with a tag
git tag v1.0.0
git push --tags
```

## Step 8: Verify

- [ ] Android AAB appears on Google Play internal track
- [ ] Android APK uploaded to CDN (if enabled)
- [ ] iOS IPA uploaded to TestFlight
- [ ] Version numbers are correct in both builds
- [ ] Apps install and run correctly from TestFlight / Play internal track

## Troubleshooting

### "No matching provisioning profiles found"
Run `fastlane match appstore -a com.yourorg.yourapp` to regenerate profiles.

### Android build fails with signing error
Verify the key alias matches what's in the keystore:
```bash
keytool -list -keystore pixygon-apps.jks -storepass "$PASSWORD"
```

### Google Play upload fails
The very first APK/AAB must be uploaded manually through the Play Console. After that, the API can manage releases.

### iOS build fails on code signing
Check that the bundle ID in `capacitor.config.ts` matches what's registered in App Store Connect and what was used with `fastlane match`.
