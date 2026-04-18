# Setup Log — Local Development Environment

This document records the actual steps taken to get the Oky repo running locally, including issues encountered and how they were resolved. It is used to validate and improve the contributor documentation.

**Machine:** MacBook, macOS 12.7.6 (Monterey), Intel
**Date:** April 2026
**Tester:** @nashthecoder

---

## Environment at start

| Tool | Version found | Required | Status |
| --- | --- | --- | --- |
| macOS | 12.7.6 Monterey | 12+ | OK |
| Node.js | 16.13.1 | 20.16.0 | Needs fix |
| nvm | 0.40.4 | Any | OK |
| Yarn | 1.22.22 | 1.x | OK |
| Docker Engine | 17.03.1-ce | 17.09+ | Blocker |
| docker-compose | 1.11.2 | 1.13.0+ | Blocker |
| Homebrew | 5.1.6 | Any | OK |
| Git | Installed | Any | OK |

---

## Step 1 — Fix Node version

**What the docs say:** install Node 20.16.0 via nvm.

**What happened:**

```bash
nvm install 20.16.0
nvm use 20.16.0
nvm alias default 20.16.0
```

Node 20.16.0 was already available via nvm. Set as default successfully.

**Gotcha:** `nvm alias default` only applies to new terminal sessions. The existing terminal still ran Node 16 until `nvm use 20.16.0` was run explicitly. Running `yarn` before doing this caused a failed install with Node engine errors from `@firebase/vertexai-preview`.

**Verification:** `node --version` → `v20.16.0` after running `nvm use 20.16.0`.

> **Doc gap found:** README did not mention that a terminal restart (or explicit `nvm use`) is required after setting the default. Added to proposal.

---

## Step 2 — Docker

**What the docs say:** install Docker Desktop and run `yarn dev`.

**What happened:** Docker Desktop 4.x was not available via the in-app update mechanism. The machine had Docker 17.03.1-ce-mac12 installed, which reported itself as the newest available version.

**Root cause:** Docker 17.03.1-ce-mac12 is a 2017 Docker Toolbox build. Its internal updater only shows updates within the same branch. Docker Desktop 4.x must be downloaded directly from docker.com — it is a separate product.

**Impact:** `docker-compose.yml` uses format version `3.4`, which requires Docker Engine 17.09+ and docker-compose 1.13+. Both installed versions are below these thresholds. `yarn dev` cannot be used.

**Workaround adopted:** Run PostgreSQL via Homebrew and start API/CMS directly without Docker. See [No-Docker path](#no-docker-path) below.

> **Doc gap found:** README listed Docker Desktop as required but gave no minimum version, no warning about old installations, and no alternative path. All three added to proposal.

---

## Step 3 — Clone and install

**What the docs say:** clone the repo, run `yarn` at root.

**What happened:** Repo was already cloned. First `yarn` attempt failed because the terminal was still on Node 16. After running `nvm use 20.16.0`, `yarn` succeeded.

```bash
nvm use 20.16.0
yarn
```

Postinstall hook automatically ran `cd app && yarn`.

> **Doc gap found:** The postinstall behaviour (auto-running `cd app && yarn`) is not documented. Added note to proposal.

---

## Step 4 — Environment files

**What the docs say:** run `yarn copy-config`.

**What happened initially:** `.env` files were created manually by copying `.env.dist` files, which resulted in an incomplete `packages/api/.env` — it only contained `DATABASE_URL` and `NODE_ENV` instead of the full set of required variables.

**Correct approach:**

```bash
yarn copy-config
```

This skips existing files, so it is safe to run even if `.env` files already exist.

> **Doc gap found:** README did not mention `yarn copy-config` — it was only in `docs/setup.md`. Manual copying is error-prone. Proposal README now uses `yarn copy-config` as the canonical step.

---

## Step 5 — Submodules

**What the docs say:** run `yarn modules`.

**What happened:** `yarn modules` failed with `fatal: already exists in the index` for all four submodule paths. The remove script deleted `.gitmodules` and `.git/modules/` but the paths were still tracked in the git index, causing `git submodule add` to fail.

**Fix:**

```bash
git rm --cached .k8s app/src/resources packages/core/src/common packages/delete-account 2>/dev/null
yarn modules
```

This clears the stale index entries and allows the pull script to re-add the submodules cleanly.

> **Doc gap found:** This failure mode is not documented anywhere. The `yarn modules` script is fragile when submodules have already been partially initialised (e.g. after `git clone` with default submodule behaviour). Added to proposal and flagged as a script improvement opportunity.

---

## Step 6 — Wrong branch

**What happened:** The entire setup above was performed on the `master` branch. The task specification requires the `for-tests` branch. This was identified after hitting avatar asset errors.

**Impact:** `master` has static imports of avatar customization assets (`friendAssets`, `BodyComponents`) that do not exist in the white-labelled resources submodule. These cause Metro bundler failures on first run.

**Root cause (code issue):** `USE_AVATAR_CUSTOMIZATION=true` is set by default in `.env.dist`, but the required asset files only exist in an optional submodule not pulled by `yarn modules`. The imports are static (not conditional on the env flag), so the app cannot bundle without them regardless of configuration.

**Fix applied on master (workaround):**

- Created stub `app/src/resources/assets/friendAssets.ts`
- Created stub `app/src/resources/assets/images/avatars/friend/display/bodies/BodyComponents.tsx`
- Created placeholder `app/src/resources/assets/app/favicon.png` and `icon.jpg`

**Switched to correct branch:**

```bash
git stash
git checkout for-tests
git stash pop
```

Stash pop produced conflicts on `.github/ISSUE_TEMPLATE/bug_report.md`, `feature_request.md`, and `pull_request_template.md` — the `for-tests` branch deleted these files. Resolved by accepting the deletions:

```bash
git rm .github/ISSUE_TEMPLATE/bug_report.md .github/ISSUE_TEMPLATE/feature_request.md .github/pull_request_template.md
```

> **Doc gap found:** The task specification links to the `for-tests` branch but the public README and setup docs make no mention of it. Contributors would not know which branch to use.

---

## Step 7 — Build core and re-pull submodules on for-tests

```bash
yarn workspace @oky/core compile
git rm --cached .k8s app/src/resources packages/core/src/common packages/delete-account 2>/dev/null
yarn modules
```

The `for-tests` branch has no references to `friendAssets` or `BodyComponents` — the avatar screens were restructured and no stub files are needed on this branch.

---

## Step 8 — Start API

CMS workspace name is `@oky/oky-cms`, not `@oky/cms`. Running `yarn workspace @oky/cms start` fails.

```bash
# Terminal 1
yarn workspace @oky/api watch
# Output: Application is now running.

# Terminal 2 — build core first, then start CMS
yarn workspace @oky/core compile
yarn workspace @oky/oky-cms start
# Output: Server started on port 5000
```

> **Doc gap found:** Workspace names are not documented anywhere. Added to proposal.

---

## Step 9 — Start app and Expo Go SDK mismatch

```bash
yarn dev:app
```

Metro started and QR code appeared. Scanning with Expo Go on Android produced a fatal error.

**Root cause:** The project uses Expo SDK 52. The installed Expo Go app on the Android device supports SDK 54 only. Newer versions of Expo Go drop support for older SDK versions.

**Attempted fix:** Using `--tunnel` flag via ngrok to bypass version check:

```bash
cd app && npx expo start -c --tunnel
```

> **Doc gap found:** README makes no mention of Expo SDK version compatibility with Expo Go, or that contributors need a specific version of Expo Go matching the project's SDK. This is a common blocker for first-time contributors using physical devices.

---

## No-Docker path

For contributors who cannot run Docker Desktop 4.x:

### Install PostgreSQL

```bash
brew install postgresql@14
brew services start postgresql@14
echo 'export PATH="/opt/homebrew/opt/postgresql@14/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

### Create database and user

```bash
createuser -s periodtracker
psql postgres -c "ALTER USER periodtracker WITH PASSWORD 'periodtracker';"
createdb -O periodtracker periodtracker
```

### Update .env files

In both `packages/api/.env` and `packages/cms/.env`, change `DATABASE_HOST=postgres` to `DATABASE_HOST=localhost`.

### Start services

```bash
# Terminal 1 — API
nvm use 20.16.0 && yarn workspace @oky/api watch

# Terminal 2 — CMS
nvm use 20.16.0 && yarn workspace @oky/oky-cms start

# Terminal 3 — App
yarn dev:app
```

### Physical device — IP address

The phone must reach the Mac's API over the local network. Get the Mac's IP:

```bash
ipconfig getifaddr en0
```

Update `app/.env`:

```bash
EXPO_PUBLIC_API_BASE_URL=http://<your-ip>:3000
EXPO_PUBLIC_API_BASE_CMS_URL=http://<your-ip>:5000
```

Restart `yarn dev:app`.

> **Important:** Metro detects and displays your current IP in the terminal output (`exp://192.168.x.x:8081`). Always use the IP shown there — it can change between sessions if your network changes. If you get "Failed to download remote update" in Expo Go, check that `app/.env` matches the IP Metro is showing.

---

## Step 10 — Expo Go SDK version and tunnel issues

**What happened:** Scanning the QR code with Expo Go gave a fatal error. Expo Go on the device supported SDK 54; the project uses SDK 52 — incompatible.

**Attempted fix — ngrok tunnel:**

```bash
cd app && npx expo start -c --tunnel
```

Expo installed `@expo/ngrok@^4.1.0` automatically but failed with:

```text
CommandError: failed to start tunnel
remote gone away
```

**Root cause:** ngrok's free tier now requires a registered account and authtoken. The tunnel approach is not viable without setup.

**Fix — install correct Expo Go version:**

Download the SDK 52 compatible Expo Go APK directly on the Android device:

```text
https://expo.dev/go?sdkVersion=52&platform=android&device=true
```

Install the APK (enable "install from unknown sources" if prompted), then scan the QR code.

> **Doc gap found:** README makes no mention of Expo SDK/Expo Go version compatibility. Contributors with a newer Expo Go (SDK 54+) will hit a fatal error on first scan. The correct Expo Go version must match the project SDK. Added to proposal.

---

## Step 11 — IP address mismatch between sessions

**What happened:** After restarting Metro, the displayed IP changed from `192.168.0.100` to `192.168.1.16` (network changed between sessions). The `app/.env` still had the old IP, causing "Failed to download remote update".

**Fix:** Always check the IP Metro shows in its output and update `app/.env` to match before scanning:

```bash
EXPO_PUBLIC_API_BASE_URL=http://192.168.1.16:3000
EXPO_PUBLIC_API_BASE_CMS_URL=http://192.168.1.16:5000
```

Then press `r` in Metro to reload.

> **Doc gap found:** The IP in `app/.env` must be updated every time the network changes. This should be called out explicitly in setup docs.

---

## Dependency version reference (from package.json files)

These are the actual versions used in the codebase — derived from reading each package's `package.json` on the `for-tests` branch. Use these when setting up, not the versions listed in the original README (which had none).

### Runtime and tooling

| Tool | Required version | Where specified | Notes |
| --- | --- | --- | --- |
| Node.js | **20.16.0** | docs/dependencies/index.md | Use nvm to manage |
| Yarn | **1.x** | docs/dependencies/index.md | `brew install yarn` |
| Docker Desktop | **4.x+** (Engine 17.09+) | docs/dependencies/index.md | compose v3.4 requires 17.09+ |
| PostgreSQL | **10+** (14 recommended) | docker-compose.yml | Homebrew path uses 14 |

### App (`app/`)

| Package | Version in code | Notes |
| --- | --- | --- |
| Expo SDK | **52.0.7** | Must match Expo Go version on device |
| React Native | **0.76.2** | |
| TypeScript | **~5.3.3** | |
| Expo Go (device) | **SDK 52 build** | Download from expo.dev/go?sdkVersion=52 — newer Expo Go (SDK 54) is incompatible |

### API (`packages/api/`)

| Package | Version in code | Notes |
| --- | --- | --- |
| TypeScript | **^4.5.4** | |
| ts-node | **^10.4.0** | |
| Express | **^4.17.2** | |
| TypeORM | **^0.2.41** | |
| nodemon | **^2.0.15** | Used for `watch` script (dev mode) |

### CMS (`packages/cms/`)

| Package | Version in code | Notes |
| --- | --- | --- |
| TypeScript | **4.7.4** | Pinned — do not upgrade without checking all workspaces |
| ts-node | **10.9.1** | |
| Express | **^4.17.2** | |
| TypeORM | **0.2.41** | Pinned |
| nodemon | **^2.0.15** | |

### TypeScript consistency note

The repo uses different TypeScript versions across packages (4.5.x in API, 4.7.x in CMS, 5.3.x in app). This is intentional — do not upgrade TypeScript in any package without verifying it does not break the others. Check with:

```bash
yarn list typescript
```

---

## Changes required for successful local setup

These are the actual file changes needed to get the repo running locally on the `for-tests` branch without Docker. Nothing here modifies application code — only config files and one script bug that needs fixing upstream.

---

### 1. `packages/api/.env` — replace incomplete file

**Why:** The file created by `yarn copy-config` from `.env.dist` only contained `DATABASE_URL` and `NODE_ENV`. The API requires the full set of TypeORM variables to connect to the database.

**Change:** Replace contents with the full variable set, pointing `DATABASE_HOST` to `localhost` instead of `postgres` (Docker hostname):

See [packages/api/.env.dist](./packages/api/.env.dist) for the template. Key change:

```
DATABASE_HOST=localhost   ← was: DATABASE_HOST=postgres
```

---

### 2. `packages/cms/.env` — fix database host

**Why:** The CMS `.env` points to `postgres` (a Docker service name) which does not exist in the no-Docker path.

**Change:** One line in [packages/cms/.env](./packages/cms/.env):

```
DATABASE_HOST=localhost   ← was: DATABASE_HOST=postgres
```

---

### 3. `app/.env` — set local network IP for physical device

**Why:** `localhost` in the app `.env` resolves to the phone itself, not your Mac. The app cannot reach the API or CMS without the Mac's actual network IP.

**Change:** Two lines in [app/.env](./app/.env):

```
EXPO_PUBLIC_API_BASE_URL=http://<your-ip>:3000
EXPO_PUBLIC_API_BASE_CMS_URL=http://<your-ip>:5000
```

Get your current IP from the Metro terminal output (`exp://x.x.x.x:8081`) or run `ipconfig getifaddr en0`. **This must be updated every time your network changes.**

---

### 4. `bin/modules/remove.sh` — script bug (upstream fix needed)

**Why:** `git clone` registers submodule paths in the git index automatically. `remove.sh` deletes the submodule files but does not clear the index, causing `pull.sh` to fail with `fatal: already exists in the index` for every submodule path.

**This is a bug in the script that affects every contributor on every branch.**

**Workaround (until fixed upstream):** Run this before `yarn modules`:

```bash
git rm --cached .k8s app/src/resources packages/core/src/common packages/delete-account 2>/dev/null
```

**Proposed fix in [bin/modules/remove.sh](./bin/modules/remove.sh):** add a loop to clear index entries before deleting files. Full proposed change documented in [PROPOSAL.md](./PROPOSAL.md#7-fix-yarn-modules-script--binmodulesremovesh).

> This is the only item that requires a code change. All other items above are config-only.

---

### 5. Expo Go version on device — install correct APK

**Why:** The project uses Expo SDK 52. Newer versions of Expo Go (SDK 53+) are incompatible and show a fatal error on launch.

**Change:** No code change needed. Install the correct Expo Go version on your device:

- **Android:** Download APK from `https://expo.dev/go?sdkVersion=52&platform=android&device=true`
- **iOS:** App Store version supports multiple SDKs — should work automatically

---

### 6. Start order matters — build `@oky/core` before CMS

**Why:** The CMS imports from `@oky/core` which must be compiled to `dist/` first. Without this step the CMS crashes immediately on start with a TypeScript module resolution error.

**Required command before starting CMS:**

```bash
yarn workspace @oky/core compile
```

No file changes needed — this is a missing step in the documented startup sequence. See [docs/run_project.md](./docs/run_project.md).

---

## Summary of all doc gaps found

| # | Gap | Where found | Fixed in |
| --- | --- | --- | --- |
| 1 | No minimum macOS version stated | README | README.proposal.md |
| 2 | No minimum Docker version or warning about old installs | README | README.proposal.md |
| 3 | No RAM / disk space requirements | README | README.proposal.md |
| 4 | `yarn copy-config` step missing from README | docs/setup.md only | README.proposal.md |
| 5 | `yarn modules` step missing from README | docs/modules.md only | README.proposal.md |
| 6 | postinstall `cd app && yarn` undocumented | nowhere | README.proposal.md |
| 7 | No-Docker alternative path not documented | nowhere | README.proposal.md |
| 8 | Expo Go + IP address step not in README | docs/run_project.md only | README.proposal.md |
| 9 | Terminal restart / `nvm use` required after setting default | nowhere | README.proposal.md |
| 10 | `yarn modules` fails if submodules already partially initialised | nowhere | README.proposal.md |
| 11 | `USE_AVATAR_CUSTOMIZATION=true` default requires optional submodule not pulled | nowhere | Code issue + README.proposal.md |
| 12 | CMS workspace name is `@oky/oky-cms` not `@oky/cms` | nowhere | README.proposal.md |
| 13 | `for-tests` branch not mentioned in setup docs | nowhere | PROPOSAL.md |
| 14 | Expo SDK version must match Expo Go app version on device | nowhere | README.proposal.md |
| 15 | `@oky/core` must be compiled before CMS can start | nowhere | README.proposal.md |
| 16 | ngrok tunnel requires registered account — not viable out of the box | nowhere | README.proposal.md |
| 17 | `app/.env` IP must be updated every time network changes — Metro shows current IP | nowhere | README.proposal.md |
| 18 | Task spec uses `for-tests` branch but README only references `master` | task brief | PROPOSAL.md |
