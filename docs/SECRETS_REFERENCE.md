# GitHub Secrets Reference

All secrets should be set at the **organization level** so they're shared across all repos. Set these in each org (Pixygon, Wattoshi, kikortet, etc.) that uses the workflows.

**Settings → Secrets and variables → Actions → Organization secrets**

## Apple / iOS Secrets

| Secret | Description | How to Get |
|--------|-------------|------------|
| `APP_STORE_CONNECT_API_KEY_ID` | App Store Connect API Key ID | App Store Connect → Users and Access → Integrations → App Store Connect API → Keys. Create a key with "Admin" role. Note the Key ID. |
| `APP_STORE_CONNECT_API_ISSUER_ID` | API Issuer ID | Same page as above, shown at the top. |
| `APP_STORE_CONNECT_API_KEY_CONTENT` | Base64-encoded `.p8` key file | Download the `.p8` key file, then: `base64 -i AuthKey_XXXXXXXXXX.p8 \| tr -d '\n'` |
| `APPLE_TEAM_ID` | Apple Developer Team ID | [developer.apple.com](https://developer.apple.com/account) → Membership Details → Team ID |

## Fastlane Match Secrets

| Secret | Description | How to Get |
|--------|-------------|------------|
| `MATCH_PASSWORD` | Encryption passphrase for Match | Choose a strong passphrase. Used to encrypt/decrypt certs in the certificates repo. |
| `MATCH_GIT_URL` | URL to certificates repo | `https://github.com/Pixygon/certificates.git` |
| `MATCH_GIT_TOKEN` | PAT with repo access | Create a GitHub PAT (classic) with `repo` scope that can access the certificates repo. |

## Google Play Secrets

| Secret | Description | How to Get |
|--------|-------------|------------|
| `GOOGLE_PLAY_SERVICE_ACCOUNT_JSON` | Google Play API service account JSON | 1. Go to [Google Cloud Console](https://console.cloud.google.com/) → APIs & Services → Credentials. 2. Create Service Account. 3. Enable Google Play Developer API. 4. Go to [Play Console](https://play.google.com/console/) → Settings → API access → Link the service account. 5. Grant "Release manager" permission. 6. Create and download a JSON key. Paste the entire JSON content. |

## Android Signing Secrets

| Secret | Description | How to Get |
|--------|-------------|------------|
| `ANDROID_KEYSTORE_BASE64` | Base64-encoded `.jks` keystore | `base64 -i pixygon-apps.jks \| tr -d '\n'` |
| `ANDROID_KEYSTORE_PASSWORD` | Keystore password | The password used when creating the keystore with `keytool`. |
| `ANDROID_KEY_PASSWORD` | Individual key password | The key password used with `-keypass` in `keytool`. |

**Note:** The key alias is passed per-project as a workflow input, not a secret.

## macOS / Electron Secrets

| Secret | Description | How to Get |
|--------|-------------|------------|
| `MAC_CERTIFICATE_BASE64` | Base64-encoded Developer ID `.p12` certificate | Export from Keychain Access: Certificates → Developer ID Application → Export as .p12. Then: `base64 -i cert.p12 \| tr -d '\n'` |
| `MAC_CERTIFICATE_PASSWORD` | `.p12` export password | The password you set when exporting the .p12. |
| `APPLE_ID` | Apple ID email (for notarization) | Your Apple ID email address. |
| `APPLE_APP_SPECIFIC_PASSWORD` | App-specific password | [appleid.apple.com](https://appleid.apple.com/) → Sign-In and Security → App-Specific Passwords → Generate. |

## CDN Secrets

| Secret | Description | How to Get |
|--------|-------------|------------|
| `BUNNY_API_KEY` | Bunny.net storage API key | [bunny.net](https://bunny.net/) → Storage → your zone → FTP & API Access → Password. |
| `BUNNY_STORAGE_ZONE` | Storage zone name | The name of your Bunny.net storage zone. |

## Per-Project Secrets (Optional)

These can be set at the repo level if they differ per project:

| Secret | Description |
|--------|-------------|
| `VITE_BASE_URL` | Backend API URL for build-time env vars |

## Setting Org Secrets via CLI

```bash
# Set a secret for an organization
gh secret set SECRET_NAME --org Pixygon --body "secret-value"

# Set from a file
gh secret set ANDROID_KEYSTORE_BASE64 --org Pixygon < keystore.b64

# List org secrets
gh secret list --org Pixygon
```

## Secret Visibility

When setting org secrets, grant access to **all repositories** or select specific repos that need them. All project repos using these workflows need access to the secrets.

```bash
# Grant to all repos
gh secret set SECRET_NAME --org Pixygon --visibility all --body "value"

# Grant to specific repos
gh secret set SECRET_NAME --org Pixygon --visibility selected \
  --repos "wattoshi.com,Norsats.no,kikortet.no" --body "value"
```
