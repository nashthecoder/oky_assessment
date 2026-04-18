# Documentation & Contributor Experience — Proposal

This document summarises all proposed changes to the Oky repository's documentation, contributor tooling, and GitHub configuration. Nothing described here has replaced existing files — all changes are staged as `.proposal` files for review before being applied.

---

## Why these changes

The current repository documentation is spread across multiple files with no clear entry point for new contributors. Setup steps are split across four separate docs pages, contributing guidance is minimal, and the GitHub issue templates — while they exist — are missing key fields and a new-language template entirely. For a project that is a verified Digital Public Good deployed in 11 countries, the open-source contributor experience should match that scale.

These proposals aim to:

- Make it possible for a new contributor to clone, run, and submit a PR without leaving the README
- Surface Oky's real-world impact (countries, languages, partners) to motivate contributors
- Add structured issue templates that make bug reports and translation requests actionable
- Align the README with DPG standard requirements (SDG alignment, privacy statement, license, Code of Conduct)

---

## Proposed file changes

### 1. README — [README.proposal.md](./README.proposal.md)

**Replaces:** [`README.md`](./README.md)

**What changed and why:**

| Area | Current state | Proposed change |
| --- | --- | --- |
| Header | Plain title, no badges | License, DPG, and SDG 3/5/10 badges — DPG standard visibility |
| Mission | One sentence | Short "Why Oky" block with girl-led, privacy, open principles |
| Repo structure | Not explained | Table mapping each package to its path and role |
| Prerequisites | Not listed | Table with exact versions (Node 20.16, Yarn 1.x, Docker) |
| Setup | Links to 4 separate docs | 5 numbered steps in one place, including env file copy commands |
| Service ports | Nowhere | Table: API :3000, CMS :5000, Adminer :8080 |
| Contributing | One link at the bottom | Table of contribution types with direct links to each template |
| Privacy & Security | Not mentioned | Explicit section — required for DPG standard |
| License | One line | Kept, with note on content licensing |

---

### 2. Contributing guide — [CONTRIBUTING.proposal.md](./CONTRIBUTING.proposal.md)

**Replaces:** [`CONTRIBUTING.md`](./CONTRIBUTING.md)

**What changed and why:**

| Area | Current state | Proposed change |
| --- | --- | --- |
| Ways to contribute | Code only implied | Explicit list: code, translations, docs, bugs, design |
| Setup | Links to README | Inline quick-start so the file stands alone |
| Branch workflow | Steps repeated twice, inconsistently | Single clean 12-step flow from fork to merged PR |
| PR creation | Not described | Step-by-step GitHub UI walkthrough (fork → compare → base branch → template) |
| Branch naming | Not mentioned | Convention table: `fix/`, `feature/`, `docs/`, `translation/`, `refactor/` |
| Spell checker | Listed but buried | Kept, with non-VS Code alternative clarified |
| Contact | Not included | Table: Discussions, partnership email, Code of Conduct reporting |

---

### 3. GitHub issue templates

#### Bug report — [`.github/ISSUE_TEMPLATE/bug_report.md`](./.github/ISSUE_TEMPLATE/bug_report.md)

> Already updated (not a proposal file — the original was thin enough to improve directly.)

**What changed:**

- Added **Affected area** checkboxes (app / API / CMS / core / docs)
- Added **Reproduction frequency** (always / sometimes / once)
- Added **Country / Language setting** field — important for a localised app
- Tightened section headers for scannability

#### Feature request — [`.github/ISSUE_TEMPLATE/feature_request.md`](./.github/ISSUE_TEMPLATE/feature_request.md)

> Already updated.

**What changed:**

- Added **Who is this for** checkboxes (girls / CMS editors / developers / deploying orgs)
- Added **user story format** prompt
- Added **Acceptance criteria** checklist
- Added **Affected area** checkboxes

#### Translation / localisation request — [`.github/ISSUE_TEMPLATE/translation_request.md`](./.github/ISSUE_TEMPLATE/translation_request.md)

> New file — did not exist before.

**Why:** Oky supports 13 languages across 11 countries. There was no structured way for translation contributors or implementing partners to request a new language or report a translation error. This template captures language code, region, estimated reach, and whether the reporter can help with the translation.

#### Issue template chooser config — [`.github/ISSUE_TEMPLATE/config.proposal.yml`](./.github/ISSUE_TEMPLATE/config.proposal.yml)

**Replaces:** nothing — new file.

**What it does:** When a contributor clicks "New Issue" on GitHub, instead of a blank form they see a chooser screen with all three templates plus four direct-contact links:

- GitHub Discussions (for questions)
- Partnership / deployment email (`hello@okyapp.info`)
- Code of Conduct reporting email
- Link to the translation guide (to read before opening a translation issue)

`blank_issues_enabled: false` prevents contributors from bypassing templates with an empty issue, which keeps the tracker clean and actionable.

**To apply:** rename `config.proposal.yml` → `config.yml`.

#### Pull request template — [`.github/pull_request_template.md`](./.github/pull_request_template.md)

> Already updated.

**What changed:**

- Removed Jira / Trello / Linear references (not used in this open-source project)
- Changed issue link format to `Closes #[issue number]` (standard GitHub syntax)
- Added `Translation / localisation` as a change type
- Checklist now links directly to `CONTRIBUTING.md` and references `yarn lint` / `yarn test`

---

## How to apply

When you are ready to go live with any of these changes:

| Proposal file | Apply by |
| --- | --- |
| `README.proposal.md` | Rename to `README.md` (backup old one first if needed) |
| `CONTRIBUTING.proposal.md` | Rename to `CONTRIBUTING.md` |
| `.github/ISSUE_TEMPLATE/config.proposal.yml` | Rename to `config.yml` |
| Issue templates & PR template | Already applied — no further action needed |

All changes can be applied in a single PR. Suggested PR title:

> `docs: improve contributor experience and DPG-align repository documentation`

---

## Setup validation log

[SETUP_LOG.md](./SETUP_LOG.md) documents the actual steps taken to run this repo locally on a MacBook (macOS 12.7.6), including every blocker hit and how it was resolved. It is the primary source for the README improvements above — each doc gap listed in the log has a corresponding fix in [README.proposal.md](./README.proposal.md).

---

### 6. Security policy — [SECURITY.proposal.md](./SECURITY.proposal.md)

**Replaces:** nothing — new file.

**Why:** DPG standard and good open source practice both require a documented vulnerability disclosure process. The current repo has none. Given Oky's user base (girls aged 10+, in contexts where menstrual health may carry social risk), any data exposure must be treated as high severity — this context is explicitly called out in the policy.

**What it covers:**

- Private reporting channel (`okyperiodtracker@gmail.com`) with response SLAs
- Privacy architecture summary so security reviewers understand what data exists and what exposure means
- Scope table (in / out)
- Known deployment risks — default secrets in `.env.dist` that partners must change before going to production
- Coordinated disclosure policy with reporter credit

**To apply:** rename `SECURITY.proposal.md` → `SECURITY.md`.

---

### 7. Fix `yarn modules` script — [bin/modules/remove.sh](./bin/modules/remove.sh)

**Replaces:** existing `bin/modules/remove.sh` — one line addition.

**The problem:**

`git clone` automatically registers submodule paths in the git index. When `yarn modules` runs, `remove.sh` correctly deletes the submodule files and directories — but does not remove those paths from the git index. When `pull.sh` then tries to re-add the submodules with `git submodule add`, git refuses because the paths are still registered in the index, producing:

```
fatal: 'app/src/resources' already exists in the index
```

This affects **every contributor on every branch** who runs `yarn modules` after a standard `git clone`. It is not user error — it is a missing step in the script.

**The fix — add one loop to `remove.sh`:**

Current `remove.sh`:
```bash
# Remove the modules from the git history
rm -rf ./.git/modules/
rm -rf .gitmodules

declare -a paths=($k8s_path $resources_path $common_path $delete_account_path $flower_path)

for path in "${paths[@]}"; do
    rm -rf $path
done
```

Proposed addition — remove paths from the git index before deleting files:
```bash
# Remove the modules from the git history
rm -rf ./.git/modules/
rm -rf .gitmodules

declare -a paths=($k8s_path $resources_path $common_path $delete_account_path $flower_path)

# Remove paths from git index so git submodule add can re-register them cleanly
for path in "${paths[@]}"; do
    git rm --cached "$path" 2>/dev/null || true
done

# Remove the actual files
for path in "${paths[@]}"; do
    rm -rf $path
done
```

The `2>/dev/null || true` means the command silently succeeds even if a path was never in the index — so the fix is safe for both fresh clones and re-runs.

**To apply:** edit `bin/modules/remove.sh` directly — this is a code fix, not a documentation change, and should be submitted as a PR to the main repository.

---

## What is not in scope here

These proposals focus on documentation and GitHub configuration only. The following are noted as future improvements but are not part of this proposal:

- Adding `README.md` files to `packages/api/`, `packages/cms/`, and `packages/core/` (none currently exist)
- Setting up GitHub Discussions if not already enabled on the repository
- Adding a `SECURITY.md` file with a formal vulnerability disclosure policy
- CI/CD badge (build status) once a public Actions workflow is confirmed
