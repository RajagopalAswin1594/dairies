# Dairies — AI Study Companion

**Privacy-first Android app** that converts encrypted user notes into provenance-verified flashcards, summaries, difficulty-tiered tests, doubt chat with source citations, and short TTS lectures.

---

## Quick start

1. Clone
  ```bash
   git clone https://github.com/RajagopalAswin1594/dairies.git
   cd dairies
  ```
2. Open in Android Studio (Arctic Fox or later).
3. Configure Firebase and add `google-services.json` to `android/app/`.
4. Add secrets to GitHub (see CI section).

---

## Features

- Encrypted uploads: PDF, DOCX, TXT, JPG, PNG  
- AI-generated flashcards, summaries, and difficulty-tiered tests (source‑anchored)  
- Doubt chat with source citations and confidence flags  
- Short TTS lectures (pleasant female voice) with server quotas and caching  
- Focus mode (blocks notifications except calls) and Pomodoro timer  
- Offline review cache (Room) and progress tracking (marks, weak areas)  
- Gamification: points, streaks, milestone badges  
- Server‑enforced quotas and device limits (2 devices/account)

---



## Architecture (high level)

- **Android (Kotlin + Jetpack Compose)** — client, Room cache, Android Keystore  
- **Firebase** — Auth, App Check, Firestore, Storage  
- **Cloud Functions / Cloud Run** — AI orchestrator, quota enforcement, LLM proxy  
- **TTS** — cloud TTS or Android TTS fallback; audio cached in Storage  
- **Monitoring** — Crashlytics, Cloud Monitoring, Sentry

---



## Repo layout

```
/android/           # Android app (Kotlin)
/functions/         # Cloud Functions (Node/TS) AI orchestrator
/infra/             # IaC (Terraform / gcloud scripts)
/docs/              # PRD, compliance, Firestore rules, documents are categorized by features and modules
/.github/           # CI workflows, issue templates
```

---



## Development notes

- Keep `google-services.json` out of VCS.  
- Store keystore and API keys in GitHub Secrets.  
- Use App Check in dev and prod.  
- Run `./gradlew lint test` before opening PRs.  
- Use Android WorkManager for background tasks and prefetching audio.

---



## Contributing

- Fork → feature branch `feat/<short-desc>` → PR to `develop`.  
- Require 1 reviewer and passing CI for `main` merges.  
- Follow Kotlin style and run static analysis (Detekt, Lint).  
- Use small, focused PRs; include screenshots or recordings for UI changes.

---



## Security & Privacy

- English-only at launch.  
- Parental consent flow required for minors; no targeted ads for minors.  
- Client-side encryption for sensitive fields; keys stored in Android Keystore.  
- App Check and strict Firestore rules required for all AI and storage endpoints.

---



## CI / CD

- See `.github/workflows/` for:
  - Android CI (lint, unit tests, assemble)
  - Play Store release workflow (signed AAB upload)
- Add secrets: `ANDROID_KEYSTORE_BASE64`, `KEYSTORE_PASSWORD`, `KEY_ALIAS`, `KEY_PASSWORD`, `PLAY_STORE_JSON`

---



## License

Apache-2.0 — see `LICENSE` file.

---



## Contact

Project: Dairies — AI Study Companion  
Repo: `https://github.com/RajagopalAswin1594/dairies`  
For issues or support, open an issue in this repository.