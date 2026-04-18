# Oky Period Tracker — Technical Assessment Tasks

**Candidate:** @nashthecoder
**Branch:** `for-tests`
**Repository:** https://github.com/Oky-period-tracker/periodtracker/tree/for-tests
**Date:** April 2026

---

## Overview

This folder documents the technical assessment tasks for the Oky Period Tracker project. Each task has a brief, a process log, and submission artefacts.

| Task | Description | Status | Process Log |
| --- | --- | --- | --- |
| [Task 1](#task-1) | Run app locally + add/swap Lottie animation | In Progress | [task1-log.md](./task1-log.md) |
| [Task 2](#task-2) | Urban/rural content targeting in CMS + app | Not Started | [task2-log.md](./task2-log.md) |
| [Task 3](#task-3) | Video — PR review + security briefing | Not Started | [task3-log.md](./task3-log.md) |

---

## Task 1

### Brief

> Pull and run the Oky white-labelled code. Add an animation or an avatar into the app that you have built from the white-labelled code. Swap out an existing animation for a new one.

### What to submit

- A video and/or screenshot of the simulator with the app running
- A video and/or screenshots of the simulator with the new Lottie animation running in the app

### Notes

- Android simulator is sufficient — no iOS build required
- Deploy locally using localhost / local network
- Use the white-labelled submodules by default — no need to create your own
- The Lottie assets can be found in the resources submodule

### Process

See [task1-log.md](./task1-log.md) for the full step-by-step account of setup, blockers encountered, and how they were resolved.

---

## Task 2

### Brief

> Modify the app and CMS to support content targeting based on where a user lives. When a user registers in the Oky app, they select whether they live in an urban or rural area. Make the encyclopedia display different content depending on that selection.

### What to build

- A CMS user can mark any encyclopaedia entry as visible to **Urban users only**, **Rural users only**, or **All users**
- The app displays only the relevant entries to each user based on their registration choice

### What to submit

A single continuous video showing:

1. A CMS user creating two pieces of content — one for rural users only, one for all users
2. A user registering in the app as a **rural user** and seeing both entries
3. Logging out (without stopping the video)
4. Registering as an **urban user** and seeing only one of the entries

### Process

See [task2-log.md](./task2-log.md) for implementation decisions, code changes, and testing notes.

---

## Task 3 (Optional)

### Brief

> Record a short video (10 minutes maximum) responding to two questions. Speak to camera or share your screen.

### Question 1 — PR Review

Review the pull request at:
`https://github.com/Oky-period-tracker/periodtracker/tree/bugfix/ios-profile-crash`

Tell us:
- What the change does and how it does it
- Any issues you would raise before approving — including anything relevant to an app used by girls aged 10 and above
- Whether you would approve it or request changes, and why

### Question 2 — Security Briefing

> You have identified a security issue in an Oky partner's latest release that poses a risk to user data. Describe how you would brief the Oky Business Owner (who is not a technologist) on the issue and your recommended action.

### What to submit

- A short video, 10 minutes maximum, speaking to camera

### Process

See [task3-log.md](./task3-log.md) for preparation notes and talking points.

---

## Repository and setup context

The full setup process — including every blocker encountered and how it was resolved — is documented in [SETUP_LOG.md](../SETUP_LOG.md) at the root of this repository.

Key setup notes:

- The correct branch for these tasks is `for-tests`, not `master`
- Docker 4.x is required — older versions do not support the compose file format
- A no-Docker alternative path (PostgreSQL via Homebrew) is documented in SETUP_LOG.md
- Expo SDK 52 Expo Go must be installed on the device — newer versions are incompatible
- `yarn modules` requires a one-time git index fix on first run — see SETUP_LOG.md step 5

All proposed improvements to the repository documentation and contributor experience are in [PROPOSAL.md](../PROPOSAL.md).
