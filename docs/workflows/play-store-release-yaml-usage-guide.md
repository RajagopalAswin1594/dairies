# Play Store Release Workflow — Usage Guide

This guide explains how to use [`.github/workflows/play-store-release.yml`](../.github/workflows/play-store-release.yml) to build a signed Android App Bundle (AAB) and upload it to Google Play.

## Overview

The workflow:

1. Checks out the repository
2. Sets up JDK 11
3. Decodes the release keystore from a base64 secret
4. Configures signing credentials via environment variables
5. Builds a release AAB with `./gradlew bundleRelease`
6. Uploads the AAB as a GitHub Actions artifact
7. Uploads the AAB to Google Play (default track: **internal**)

## Triggers

| Trigger | When it runs |
|---------|----------------|
| `push` to `main` | Automatically on every push to `main` |
| `workflow_dispatch` | Manually from the Actions tab; you can choose the Play Store track |

### Manual run

1. Open the repository on GitHub
2. Go to **Actions** → **Play Store Release**
3. Click **Run workflow**
4. Select the track: `internal`, `alpha`, `beta`, or `production` (default: `internal`)
5. Click **Run workflow**

## Repository secrets

Add these secrets under **Settings → Secrets and variables → Actions → New repository secret**:

| Secret | Required | Description |
|--------|----------|-------------|
| `ANDROID_KEYSTORE_BASE64` | Yes | Base64-encoded release keystore file |
| `KEYSTORE_PASSWORD` | Yes | Password for the keystore |
| `KEY_ALIAS` | Yes | Alias of the signing key |
| `KEY_PASSWORD` | Yes | Password for the signing key |
| `PLAY_STORE_JSON` | Yes | Google Play service account JSON (full file contents) |
| `GRADLE_PROPERTIES` | Optional | Extra Gradle properties (e.g. SDK/signing overrides) if your project needs them |

### Encode the keystore

**Linux / macOS:**

```bash
base64 -i release.keystore | tr -d '\n' > keystore.b64
```

**Windows (PowerShell):**

```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("release.keystore")) | Set-Content -NoNewline keystore.b64
```

Paste the contents of `keystore.b64` into the `ANDROID_KEYSTORE_BASE64` secret.

### Play Console service account

1. In [Google Cloud Console](https://console.cloud.google.com/), create (or use) a service account
2. Download its JSON key
3. In [Google Play Console](https://play.google.com/console/) → **Users and permissions**, invite that service account email with permission to upload/release apps
4. Paste the entire JSON key file into the `PLAY_STORE_JSON` secret

## What the workflow expects

- An Android Gradle project with a working `./gradlew` wrapper
- Release signing that reads these env vars (or equivalent):
  - `RELEASE_STORE_FILE`
  - `RELEASE_STORE_PASSWORD`
  - `RELEASE_KEY_ALIAS`
  - `RELEASE_KEY_PASSWORD`
- Output AAB at: `app/build/outputs/bundle/release/app-release.aab`

If your module or output path differs, update the **Upload artifact** and **Upload to Play Store** steps accordingly.

## Build command

By default the workflow runs:

```bash
./gradlew bundleRelease --no-daemon --stacktrace
```

- Use `bundleRelease` for Play Store AAB uploads (recommended)
- Switch to `assembleRelease` if you need an APK instead (and update artifact/upload paths)

Adjust `bundleRelease` / `assembleRelease` as needed for your project.

## Tracks

| Track | Typical use |
|-------|-------------|
| `internal` | Internal testing (default) |
| `alpha` | Closed alpha |
| `beta` | Open/closed beta |
| `production` | Production release |

On push to `main`, the track defaults to `internal` when no `workflow_dispatch` input is present.

## Artifacts

Successful builds upload:

- **Name:** `app-release-aab`
- **Path:** `app/build/outputs/bundle/release/app-release.aab`

Download it from the workflow run’s **Artifacts** section if you need a local copy.

## Notes

- Add these repository secrets: `ANDROID_KEYSTORE_BASE64`, `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`, `PLAY_STORE_JSON` (service account JSON), `GRADLE_PROPERTIES` (optional).
- This workflow builds a signed AAB and uploads to Google Play (internal track by default).
- Adjust `bundleRelease` / `assembleRelease` as needed.
- Never commit the keystore, passwords, or service account JSON to the repository.
- Ensure the Play Console app listing exists and the service account has upload rights before the first upload.
