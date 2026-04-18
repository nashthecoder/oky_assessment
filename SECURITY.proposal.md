# Security Policy

## Supported versions

| Version | Supported |
| --- | --- |
| Latest release on `master` | Yes |
| Older releases | No — please update to the latest version |

---

## Reporting a vulnerability

**Do not report security vulnerabilities through public GitHub issues.**

If you discover a security vulnerability in Oky — including in the app, API, CMS, or any submodule — please report it privately:

**Email:** okyperiodtracker@gmail.com  
**Subject line:** `[SECURITY] Brief description`

Please include:

- A description of the vulnerability and its potential impact
- The affected component (app / API / CMS / submodule)
- Steps to reproduce or a proof of concept
- Any suggested remediation if you have one

You will receive an acknowledgement within **5 business days**. We aim to assess and respond to all security reports within **14 days**.

We ask that you:

- Give us reasonable time to investigate and patch before any public disclosure
- Do not access, modify, or delete user data during testing
- Do not perform denial-of-service attacks or automated scanning against production systems

---

## Privacy and data handling

Oky is designed with privacy as a core principle. Understanding the data architecture is important context for security reviewers:

- **No personally identifiable information (PII) is collected.** Users register with a username and PIN only — no email, phone number, or real name.
- **Period data is stored locally on-device by default.** Only anonymised, non-identifiable cycle data is optionally synced online for cross-device access.
- **The app is passcode-protected** and designed for use on shared devices.
- **The app works fully offline.** Network connectivity is only required for CMS content sync and optional cloud backup.
- **Push notifications** are handled via Firebase Cloud Messaging. No message content is stored server-side beyond the notification payload.

Any vulnerability that could expose cycle data, usage patterns, or user identity — even indirectly — should be treated as **high severity** given that the app is used by girls aged 10 and above in contexts where menstrual health may carry social risk.

---

## Scope

| In scope | Out of scope |
| --- | --- |
| App (React Native / Expo) | Third-party services (Firebase, ngrok, Expo) |
| API (`packages/api`) | Deployment infrastructure managed by partners |
| CMS (`packages/cms`) | Social engineering attacks |
| Submodules (resources, common) | Physical device attacks |
| Authentication and session handling | Scanner/automated testing against production |

---

## Disclosure policy

We follow **coordinated disclosure**. Once a patch is available, we will:

1. Release the fix
2. Credit the reporter (unless they prefer to remain anonymous)
3. Publish a brief summary of the vulnerability and fix in the changelog

---

## Known considerations

- The CMS uses session-based authentication with Passport.js. The default `PASSPORT_SECRET` in `.env.dist` is `!ChangeMe!` — deploying partners must change this before going to production.
- The API `APPLICATION_SECRET` in `.env.dist` is also a placeholder — must be changed before deployment.
- Firebase configuration files (`google-services.json`, `GoogleService-Info.plist`, `firebase-config.json`) contain sensitive credentials and must never be committed to a public repository.
