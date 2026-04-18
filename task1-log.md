# Task 1 — Process Log

## Brief

Pull and run the Oky white-labelled code. Add an animation or avatar into the app. Swap out an existing animation for a new one.

---

## Environment

| Item | Detail |
| --- | --- |
| Machine | MacBook, macOS 12.7.6 (Monterey), Intel |
| Branch | `for-tests` |
| Device | Android phone via Expo Go (SDK 52) |
| API | Running locally — Node/Express, no Docker |
| CMS | Running locally — Node/Express, no Docker |
| Database | PostgreSQL 14 via Homebrew |

---

## Setup summary

Full setup detail is in [SETUP_LOG.md](../SETUP_LOG.md). Summary of what was required:

1. Node switched to 20.16.0 via nvm
2. PostgreSQL installed via Homebrew (Docker version too old)
3. API `.env` fixed — `DATABASE_HOST=localhost`
4. CMS `.env` fixed — `DATABASE_HOST=localhost`
5. `yarn` installed dependencies
6. `yarn copy-config` created env files
7. Git index cleared, then `yarn modules` pulled submodules
8. `@oky/core` compiled before CMS start
9. API started: `yarn workspace @oky/api watch`
10. CMS started: `yarn workspace @oky/oky-cms start`
11. App started: `yarn dev:app`
12. Expo Go SDK 52 APK installed on Android device
13. `app/.env` updated with local network IP
14. App confirmed running on Android device via Expo Go

---

## Lottie animation task

### Finding existing animations

_To be completed_

### Swap process

_To be completed_

### New animation added

_To be completed_

---

## Artefacts

| Item | Link / Location |
| --- | --- |
| Screenshot — app running | _to be added_ |
| Video — app running | _to be added_ |
| Screenshot — new Lottie running | _to be added_ |
| Video — new Lottie running | _to be added_ |
