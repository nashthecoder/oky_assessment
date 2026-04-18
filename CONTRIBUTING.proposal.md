# Contributing to Oky

Thank you for taking the time to contribute. Oky is an open-source project used by girls across 11 countries — every contribution, big or small, makes a real difference.

Please read this guide before you start. It covers everything from filing your first issue to getting a pull request merged.

> By participating in this project you agree to our [Code of Conduct](./CODE_OF_CONDUCT.md).  
> Questions? Email **hello@okyapp.info** or join our community Slack (see below).

---

## Ways to contribute

You don't have to write code to contribute. We welcome:

- **Bug reports** — something broken in the app, API, or CMS
- **Feature suggestions** — ideas for improvements
- **Translations** — new languages or fixes to existing ones (we support 13 languages and counting)
- **Documentation** — clearer setup guides, fixing outdated steps, new how-tos
- **Code** — bug fixes, new features, tests, refactors
- **Design feedback** — from a girl-centred or accessibility perspective

---

## Before you start

1. Check [open issues](https://github.com/Oky-period-tracker/periodtracker/issues) — your idea or bug may already be tracked
2. For significant changes, open an issue first and discuss the approach before writing code
3. Join the community Slack and introduce yourself with your GitHub username — link in your welcome email after contacting hello@okyapp.info
4. Read [About Oky](./AboutOky.md) to understand the project's values and design principles

---

## Reporting a bug

Use the [Bug Report template](https://github.com/Oky-period-tracker/periodtracker/issues/new?template=bug_report.md).

A good bug report includes:
- Which part of the app is affected (app / API / CMS)
- Exact steps to reproduce the problem
- What you expected vs what actually happened
- Your device, OS, and app version
- Screenshots or logs if you have them

---

## Requesting a feature

Use the [Feature Request template](https://github.com/Oky-period-tracker/periodtracker/issues/new?template=feature_request.md).

Frame your request around the user it helps (e.g. "As a girl using the app in offline mode, I want..."). Include acceptance criteria — what does "done" look like?

---

## Requesting a translation or reporting a translation error

Use the [Translation Request template](https://github.com/Oky-period-tracker/periodtracker/issues/new?template=translation_request.md).

Before translating, read the [Translations guide](./docs/localisation/translations.md) to understand how strings are structured and where they live in the codebase.

---

## Setting up locally

Follow the [Local Setup guide in the README](./README.md#local-setup). The short version:

```bash
git clone https://github.com/Oky-period-tracker/periodtracker.git
cd periodtracker
yarn install
cp packages/api/.env.dist packages/api/.env
cp packages/cms/.env.dist packages/cms/.env
cp app/.env.dist app/.env
yarn dev        # starts API + CMS + database via Docker
yarn dev:app    # starts the Expo mobile dev server (new terminal)
```

---

## Making a code contribution — step by step

### 1. Fork the repository

Click **Fork** at the top right of the [repository page](https://github.com/Oky-period-tracker/periodtracker).  
This creates your own copy at `https://github.com/<your-username>/periodtracker`.

### 2. Clone your fork

```bash
git clone https://github.com/<your-username>/periodtracker.git
cd periodtracker
```

### 3. Add the upstream remote

This lets you pull in future changes from the main repo:

```bash
git remote add upstream https://github.com/Oky-period-tracker/periodtracker.git
```

### 4. Create a branch

Always branch off `master`. Use a descriptive name:

```bash
git checkout master
git pull upstream master
git checkout -b fix/calendar-prediction-off-by-one
```

Branch naming conventions:

| Prefix | Use for |
|---|---|
| `fix/` | Bug fixes |
| `feature/` | New features |
| `docs/` | Documentation only |
| `translation/` | Localisation changes |
| `refactor/` | Code cleanup with no behaviour change |

### 5. Install the spell checker (VS Code)

Install [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) to catch typos before you commit.  
Not using VS Code? Use the [cspell npm package](https://www.npmjs.com/package/cspell) instead.

### 6. Make your changes

- Write clean, readable code — see [Recommended reading](./docs/code/recommended_reading.md)
- If adding a significant feature that not all deployments will use, implement it as an [optional submodule](./docs/code/optional_submodules.md)
- Add or update tests for your changes
- Update any relevant documentation

### 7. Run checks locally

Before committing, make sure everything passes:

```bash
yarn lint       # check code style
yarn test       # run all tests
yarn compile    # TypeScript type check
```

### 8. Commit your changes

Write a clear, concise commit message in the imperative mood:

```bash
git add <specific files>
git commit -m "Fix calendar prediction off-by-one error for irregular cycles"
```

Avoid `git add .` or `git add -A` — only stage the files you intentionally changed.

### 9. Keep your branch up to date

Before opening a PR, pull in the latest changes and rebase to keep a clean history:

```bash
git pull upstream develop --rebase
```

Resolve any conflicts, then continue:

```bash
git rebase --continue
```

### 10. Push your branch

```bash
git push origin fix/calendar-prediction-off-by-one
```

### 11. Open a Pull Request on GitHub

1. Go to your fork on GitHub: `https://github.com/<your-username>/periodtracker`
2. You'll see a banner — click **"Compare & pull request"**
3. Set the **base repository** to `Oky-period-tracker/periodtracker` and **base branch** to `develop`
4. Fill in the [PR template](./.github/pull_request_template.md):
   - Link the related issue (`Closes #123`)
   - Describe what changed and why
   - List the steps a reviewer can use to test your change
   - Add screenshots if the change is visual
5. Click **"Create pull request"**

### 12. Respond to review feedback

A maintainer will review your PR. They may request changes — this is normal and expected.  
Push new commits to the same branch to update the PR. Do not open a new PR for the same change.

Once approved, a maintainer will merge your PR into `develop`. It will be included in the next release to `master`.

---

## Code style

- Language: TypeScript
- Formatter: Prettier (`yarn format`)
- Linter: TSLint (`yarn lint`)
- Tests: Jest

We use [husky](https://typicode.github.io/husky/) pre-commit hooks — these run automatically when you commit. If a hook fails, fix the issue before committing (do not use `--no-verify`).

---

## Additional guidelines

- **Tests** — write tests for new behaviour and make sure existing tests pass
- **Documentation** — update docs if your change affects setup, configuration, or user-facing behaviour
- **Sensitive data** — never commit secrets, API keys, `.env` files, or personal data
- **Optional features** — if a feature is deployment-specific, implement it as an optional submodule so other deployments are not affected

---

## Project structure reminder

| Package | Path | Notes |
|---|---|---|
| App | `app/` | React Native — Expo |
| API | `packages/api/` | Node / Express |
| CMS | `packages/cms/` | Express + TypeORM |
| Core | `packages/core/` | Shared logic |

See [Understanding routes](./docs/code/routes.md) for how the API and CMS are structured.

---

## Contact

| Purpose | Contact |
|---|---|
| General questions | [GitHub Discussions](https://github.com/Oky-period-tracker/periodtracker/discussions) |
| Partnership / deployment | hello@okyapp.info |
| Code of Conduct violation | okyperiodtracker@gmail.com |

---

Thank you for contributing to Oky. Your work helps girls around the world feel informed and in control.
