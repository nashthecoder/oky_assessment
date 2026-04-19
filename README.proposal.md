# Oky — Period Tracker

[![License: GPL-3.0](https://img.shields.io/badge/License-GPL--3.0-blue.svg)](./LICENSE)
[![Digital Public Good](https://img.shields.io/badge/Digital%20Public%20Good-Verified-brightgreen)](https://digitalpublicgoods.net/)
[![SDG 3](https://img.shields.io/badge/SDG-3%20Good%20Health-4C9F38)](https://sdgs.un.org/goals/goal3)
[![SDG 5](https://img.shields.io/badge/SDG-5%20Gender%20Equality-FF3A21)](https://sdgs.un.org/goals/goal5)
[![SDG 10](https://img.shields.io/badge/SDG-10%20Reduced%20Inequalities-DD1367)](https://sdgs.un.org/goals/goal10)

**Oky is the world's first period tracker app designed with and for girls.**
Built for girls aged 10–19, it works offline, stores no personal data, and is free and open-source for any organisation to adapt and deploy.

> New here? Start with [Local Setup](#local-setup).
> Want to contribute? Jump to [Contributing](#contributing).

---

## Why Oky

- **Girl-led** — designed with girls in Mongolia and Indonesia
- **Privacy-first** — no personal data collected, passcode protected, works offline
- **Accessible** — lightweight, supports shared phones, includes disability considerations
- **Open** — free to adapt, localise, and deploy under GPL-3.0

---

## What's in this repo

This is a **monorepo**. All packages are managed together with Yarn workspaces.

| Package | Path | What it does |
| --- | --- | --- |
| **App** | `app/` | React Native mobile app (Expo) |
| **API** | `packages/api/` | Node/Express REST API — port 3000 |
| **CMS** | `packages/cms/` | Content Management System — port 5000 |
| **Core** | `packages/core/` | Shared business logic (used by app + API) |
| **Delete Account** | `packages/delete-account/` | Static page for account deletion |

---

## System requirements

### Minimum machine specs

| Requirement | Minimum |
| --- | --- |
| **macOS** | 12 (Monterey) or later |
| **RAM** | 8 GB (16 GB recommended) |
| **Disk space** | 10 GB free (dependencies + Docker images) |
| **Architecture** | Intel or Apple Silicon (M1/M2/M3) |

> **M1/M2/M3 Mac?** You will need Rosetta for some steps. See [macOS M1 setup notes](./docs/dependencies/m1_macos.md).

---

## Version reference

All versions are derived from the project's `package.json` files. Use these — not guesses.

### Tooling

| Tool | Required version | Notes |
| --- | --- | --- |
| Node.js | **20.16.0** | Use nvm — other versions will break the install |
| Yarn | **1.x** | Do not use Yarn 2/3/4 — this repo uses Yarn 1 workspaces |
| Docker Desktop | **4.x** (Engine 17.09+) | Compose file v3.4 requires 17.09+. Old Docker Toolbox will not work |
| PostgreSQL | **14** (10+ minimum) | Only needed for the no-Docker path |

### App

| Package | Version | Notes |
| --- | --- | --- |
| Expo SDK | **52** | Expo Go on your device must also be SDK 52 — see step 6 |
| React Native | **0.76.2** | |
| TypeScript | **~5.3.3** | |
| iOS deployment target | **12.0** | Minimum iOS version the app supports |
| Android | Managed by Expo | No manual SDK version needed for Expo Go development |

### API (`packages/api`)

| Package | Version | Notes |
| --- | --- | --- |
| TypeScript | **^4.5.4** | |
| Express | **^4.17.2** | |
| TypeORM | **^0.2.41** | |
| ts-node | **^10.4.0** | |

### CMS (`packages/cms`)

| Package | Version | Notes |
| --- | --- | --- |
| TypeScript | **4.7.4** | Pinned — do not upgrade without checking all packages |
| Express | **^4.17.2** | |
| TypeORM | **0.2.41** | Pinned |
| ts-node | **10.9.1** | |

> **TypeScript versions differ across packages** — this is intentional. Do not upgrade TypeScript in any single package without verifying it across the monorepo: `yarn list typescript`

---

## Prerequisites

Install these before starting. All are required unless marked optional.

| Tool | Version | Install |
| --- | --- | --- |
| [Homebrew](https://brew.sh/) | Any | `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` |
| [nvm](https://github.com/nvm-sh/nvm) | Any | `brew install nvm` |
| [Node.js](https://nodejs.org/) | **20.16.0** | `nvm install 20.16.0` |
| [Yarn](https://yarnpkg.com/) | 1.x | `brew install yarn` |
| [Git](https://git-scm.com/) | Any | Pre-installed on macOS |
| [Docker Desktop](https://www.docker.com/products/docker-desktop/) | **4.x or later** | Download from docker.com |

> **Docker version matters.** Docker 17.x or earlier will not work — the compose file requires Docker Engine 17.09+ and docker-compose 1.13+. Download Docker Desktop 4.x directly from [docker.com/products/docker-desktop](https://www.docker.com/products/docker-desktop) — do not rely on in-app update prompts from older versions.

For viewing the app, install **one** of:

- **[Expo Go](https://expo.dev/go)** on your physical phone — easiest, no simulator needed
- Android Emulator via Android Studio
- iOS Simulator via Xcode (macOS only)

After installing nvm, add it to your shell by adding these lines to `~/.zshrc`:

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"
```

Then restart your terminal and verify: `node --version` should print `v20.16.0`.

---

## Local Setup

Follow these steps in order.

### 1. Set up SSH and clone the repo

Add an SSH key to your GitHub account if you haven't already — [GitHub SSH guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent).

Then clone:

```bash
git clone https://github.com/Oky-period-tracker/periodtracker.git
cd periodtracker
```

### 2. Install dependencies

From the repo root — this installs all workspace packages including the app:

```bash
yarn
```

> The postinstall hook automatically runs `cd app && yarn` for you.

### 3. Set up environment files

This one command copies all `.env.dist` templates into the correct `.env` files:

```bash
yarn copy-config
```

> Do not manually copy `.env` files — `yarn copy-config` ensures all required variables are present. The defaults work for local Docker development.

### 4. Pull submodules

Assets and translations are stored in Git submodules. Pull them with:

```bash
yarn modules
```

> This downloads the default white-labelled assets and translations. Do not skip this step — the app will be missing content without it.

> **First-run gotcha:** If `yarn modules` fails with `fatal: already exists in the index` for any submodule path, clear the stale git index entries first, then retry:
> ```bash
> git rm --cached .k8s app/src/resources packages/core/src/common packages/delete-account 2>/dev/null
> yarn modules
> ```
> This happens when git has already registered submodule paths during clone. The `remove.sh` script clears the files but not the index — running the above fixes it.

### 5. Start the backend

#### With Docker (recommended)

Make sure Docker Desktop is running, then:

```bash
yarn dev
```

This starts everything via Docker Compose:

| Service | Local URL |
| --- | --- |
| API | <http://localhost:3000> |
| CMS | <http://localhost:5000> |
| PostgreSQL | `localhost:5432` (auto-configured) |
| Adminer (DB UI) | <http://localhost:8080> |

Wait until the logs show the API and CMS are ready before starting the app.

#### Without Docker (macOS — PostgreSQL via Homebrew)

> **When would a developer not have Docker?**
>
> - Their machine has an old Docker Toolbox install (pre-2017) — the in-app updater does not offer Docker Desktop 4.x, which must be downloaded separately from docker.com
> - Corporate or institutional machines where IT policy blocks Docker Desktop installation
> - Low-spec machines (under 8 GB RAM) where Docker Desktop runs too slowly to be practical
> - Developers who prefer a native stack and have PostgreSQL already installed
>
> In all these cases, running PostgreSQL via Homebrew and starting services directly is a fully supported alternative.

If Docker Desktop 4.x is not available, run the services directly.

**One-time database setup:**

```bash
brew install postgresql@14
brew services start postgresql@14
createuser -s periodtracker
psql postgres -c "ALTER USER periodtracker WITH PASSWORD 'periodtracker';"
createdb -O periodtracker periodtracker
```

**Update `.env` files** — change `DATABASE_HOST=postgres` to `DATABASE_HOST=localhost` in both:

- `packages/api/.env`
- `packages/cms/.env`

**One-time database schema setup:**

The Docker path handles this automatically. Without Docker, run these once after PostgreSQL is running:

```bash
# 1. Create the schema
psql -U periodtracker -d periodtracker -c "CREATE SCHEMA periodtracker;"

# 2. Create all tables
psql -U periodtracker -d periodtracker -f sql/create-tables.sql

# 3. Run migrations in order
for f in $(ls sql/[0-9]*.sql | sort); do
  psql -U periodtracker -d periodtracker -f "$f"
done

# 4. Insert the temporary CMS admin user (username: admin, password: admin)
psql -U periodtracker -d periodtracker -c "INSERT INTO \"periodtracker\".\"user\" (\"id\", \"username\", \"password\", \"lang\", \"date_created\", \"type\") VALUES (-1, 'admin', '\$2b\$10\$cslKchhKRBsWG.dCsspbb.mkY9.opLl1t1Oxs3j2E01/Zm3llW/Rm', 'en', NOW(), 'superAdmin');"
```

> **Errors like "already exists" from the migration files are safe to ignore** — `create-tables.sql` is the consolidated schema and already includes those columns and tables.

**Load seed content (optional but recommended):**

The resources submodule includes English encyclopedia content, quizzes, surveys, and did-you-knows. Load it with one command — the app will have real content immediately:

```bash
psql -U periodtracker -d periodtracker -f app/src/resources/translations/content/insert-content-en.sql
```

> For other languages, the equivalent file is `insert-content-<locale>.sql` in the same directory. Run `yarn modules` first to ensure the resources submodule is present.

> **IMPORTANT — secure the CMS after first login:** go to `http://localhost:5000`, log in as `admin`/`admin`, create a new user with a strong password via `/user-management`, then delete the `admin` account.

**Start all three services** — run this once to open each in its own Terminal window:

```bash
osascript \
  -e 'tell application "Terminal" to do script "cd $(pwd) && nvm use 20.16.0 && yarn workspace @oky/api watch"' \
  -e 'tell application "Terminal" to do script "cd $(pwd) && nvm use 20.16.0 && yarn workspace @oky/core compile && yarn workspace @oky/oky-cms start"' \
  -e 'tell application "Terminal" to do script "cd $(pwd) && nvm use 20.16.0 && yarn dev:app"'
```

Or start them manually in three separate terminals:

```bash
# Terminal 1 — API (port 3000)
nvm use 20.16.0 && yarn workspace @oky/api watch

# Terminal 2 — CMS (port 5000) — compile core first, then start
nvm use 20.16.0 && yarn workspace @oky/core compile && yarn workspace @oky/oky-cms start

# Terminal 3 — App (Metro)
nvm use 20.16.0 && yarn dev:app
```

> **`@oky/core` must be compiled before the CMS starts.** The CMS imports from `@oky/core` — if `dist/` does not exist the CMS will crash immediately with a module resolution error. The commands above handle this automatically.

> **CMS workspace name:** use `@oky/oky-cms`, not `@oky/cms` — the latter will fail with "Unknown workspace".

Wait until the API logs `Application is now running` and the CMS logs `Server started on port 5000` before starting the app.

### 6. Prepare Expo Go on your device

This project uses **Expo SDK 52**. The version of Expo Go on your device must match — newer versions (SDK 53+) are not compatible and will show a fatal error on launch.

**Check your Expo Go version** by opening it and looking at the version number. If it does not say SDK 52, install the correct version:

| Platform | How to get SDK 52 Expo Go |
| --- | --- |
| Android | Download APK: `https://expo.dev/go?sdkVersion=52&platform=android&device=true` — install it (enable "install from unknown sources" if prompted) |
| iOS | Use the App Store version — iOS Expo Go supports multiple SDKs and should work automatically |

> **Android note:** You may need to uninstall your current Expo Go before installing the SDK 52 APK.

### 7. Start the mobile app

In a **new terminal**:

```bash
yarn dev:app
```

Metro will start and display a QR code. Note the IP address shown in the Metro output — it looks like `exp://192.168.1.x:8081`. You will need this IP in the next step.

Then press:

- `i` — open in iOS Simulator
- `a` — open in Android Emulator
- `s` — switch to Expo Go on your device

### 8. Connect your physical device

**Your phone and Mac must be on the same WiFi network.**

The app `.env` must point to your Mac's local IP — not `localhost`, which only works on the Mac itself. Use the IP Metro showed in its output:

```bash
# Get your Mac's current IP
ipconfig getifaddr en0
```

Update `app/.env`:

```bash
EXPO_PUBLIC_API_BASE_URL=http://<your-ip>:3000
EXPO_PUBLIC_API_BASE_CMS_URL=http://<your-ip>:5000
```

Then press `r` in the Metro terminal to reload, and scan the QR code with Expo Go.

> **IP changes between sessions.** If you switch networks or restart your Mac, your LAN IP may change. Run `ipconfig getifaddr en0` (Mac) to get the current IP, or check the address Metro prints in its output (`exp://x.x.x.x:8081`). Update `app/.env` to match before scanning.

**Done.** The app should be running on your device.

---

## Common commands

| Command | What it does |
| --- | --- |
| `yarn dev` | Start API + CMS + database via Docker |
| `yarn dev:app` | Start the Expo mobile dev server |
| `yarn test` | Run all tests |
| `yarn lint` | Lint all packages |
| `yarn format` | Format code with Prettier |
| `yarn compile` | TypeScript compile check across all packages |

---

## Project structure

```text
periodtracker/
├── app/                         # React Native app (Expo)
├── packages/
│   ├── api/                     # Express REST API
│   ├── cms/                     # Express CMS
│   ├── core/                    # Shared business logic
│   └── delete-account/          # Static account deletion page
├── docs/                        # Extended documentation
├── sql/                         # Database migrations & seeds
├── docker-compose.yml           # Production Docker config
├── docker-compose.override.yml  # Dev overrides (hot-reload, Adminer)
└── package.json                 # Root workspace + shared scripts
```

---

## Contributing

We welcome contributions of all kinds — not just code.

| Contribution type | Where to start |
| --- | --- |
| Report a bug | [Bug report template](https://github.com/Oky-period-tracker/periodtracker/issues/new?template=bug_report.md) |
| Suggest a feature | [Feature request template](https://github.com/Oky-period-tracker/periodtracker/issues/new?template=feature_request.md) |
| Add or fix a translation | [Translation request template](https://github.com/Oky-period-tracker/periodtracker/issues/new?template=translation_request.md) |
| Contribute code | Read [CONTRIBUTING.md](./CONTRIBUTING.md) — full step-by-step guide |
| Ask a question | [GitHub Discussions](https://github.com/Oky-period-tracker/periodtracker/discussions) |
| Partner / deploy Oky | Email <hello@okyapp.info> |

Please read our [Code of Conduct](./CODE_OF_CONDUCT.md) before participating.

---

## Documentation

| Topic | Link |
| --- | --- |
| Installing dependencies | [docs/dependencies/index.md](./docs/dependencies/index.md) |
| Full project setup | [docs/setup.md](./docs/setup.md) |
| Running locally | [docs/run_project.md](./docs/run_project.md) |
| Testing | [docs/tests.md](./docs/tests.md) |
| Translations & localisation | [docs/localisation/translations.md](./docs/localisation/translations.md) |
| Updating CMS content | [docs/localisation/updating_content.md](./docs/localisation/updating_content.md) |
| Deployment & architecture | [docs/deployment/deployment_overview.md](./docs/deployment/deployment_overview.md) |
| API & CMS routes | [docs/code/routes.md](./docs/code/routes.md) |
| Git submodules | [docs/modules.md](./docs/modules.md) |
| Recommended reading | [docs/code/recommended_reading.md](./docs/code/recommended_reading.md) |

---

## Privacy & Security

Oky collects no personally identifiable information. Period data is stored on-device; only anonymised data is optionally synced. See [SECURITY.md](./SECURITY.md) for the full policy and how to report vulnerabilities.

---

## License

[GPL-3.0](./LICENSE) — free to use, modify, and distribute with attribution.
Content (encyclopedia articles, translations) may be requested separately — contact the Oky team.

---

*Built by the Oky team and open-source contributors worldwide.*
*Oky is a verified [Digital Public Good](https://digitalpublicgoods.net/).*
