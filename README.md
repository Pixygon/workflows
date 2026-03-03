# Pixygon Reusable Workflows

Shared GitHub Actions workflows for building and publishing mobile (iOS/Android) and desktop (Electron) apps across multiple Pixygon projects.

## Reusable Workflows

| Workflow | Description | Runner |
|----------|-------------|--------|
| `build-capacitor-android.yml` | Build, sign, and upload Android AAB to Google Play + APK to CDN | `ubuntu-latest` |
| `build-capacitor-ios.yml` | Build, sign with Fastlane Match, and upload to TestFlight | `macos-latest` |
| `build-electron.yml` | Build Electron apps for Linux/Windows/macOS, upload to CDN + GitHub Release | Matrix |
| `promote-release.yml` | Promote internal/TestFlight builds to production | `ubuntu/macos` |

## Composite Actions

| Action | Description |
|--------|-------------|
| `setup-node` | Setup Node.js with npm caching |
| `extract-version` | Extract version from git tag or package.json, compute version code |

## Quick Start

1. Read [docs/SETUP_NEW_PROJECT.md](docs/SETUP_NEW_PROJECT.md)
2. Copy a template from `templates/` to your project
3. Change 4 values
4. Push a tag

## Templates

- `templates/capacitor-workflow.yml` - Mobile builds (iOS + Android)
- `templates/electron-workflow.yml` - Desktop builds (Linux/Windows/macOS)
- `templates/promote-workflow.yml` - Promote builds to production

## Architecture

```
Pixygon/workflows (this repo, public)
  └── Reusable workflows + composite actions

Pixygon/certificates (private)
  └── Fastlane Match iOS certs & profiles

Per-project repos
  └── Thin workflow files (~20 lines) calling reusable workflows
```

## Documentation

- [Setting up a new project](docs/SETUP_NEW_PROJECT.md)
- [Secrets reference](docs/SECRETS_REFERENCE.md)
