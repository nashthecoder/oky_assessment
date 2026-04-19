# Oky CMS — User Guide

The CMS (Content Management System) is the admin interface used to create and manage all content displayed in the Oky app. It runs locally at `http://localhost:5000`.

> This guide covers every section of the CMS. Screenshots should be added inline — placeholders are marked `[SCREENSHOT: description]`.

---

## Getting in

### First-time setup

Before logging in for the first time, the database must be initialised and a temporary admin user inserted. See the [Local Setup guide](./README.proposal.md#without-docker-macos--postgresql-via-homebrew) for the full steps.

### Login

Navigate to `http://localhost:5000`. You will see the login page.

`[SCREENSHOT: Login page]`

| Field | Value |
| --- | --- |
| Username | `admin` (or your created username) |
| Password | `admin` (or your password) |

> **After first login:** immediately go to `/user-management`, create a new user with a strong password, then delete the `admin` account.

---

## Navigation

The CMS uses a persistent **sidebar** on every page with links to all sections.

`[SCREENSHOT: Sidebar navigation]`

| Sidebar link | Section |
| --- | --- |
| Add a new user | User Management *(superAdmin only)* |
| Data Management | Import/export content and translations |
| Avatar Messages | In-app messages from the Oky avatar |
| Did you knows | Short fact cards shown in the app |
| Encyclopedia | Full articles organised by category |
| Help centers | Help/support contact information |
| About | About Oky content |
| Privacy Policy | Privacy policy text |
| Terms and Conditions | Terms and conditions text |
| Notifications | Push notifications to app users |
| Quiz | Quiz questions |
| Survey | Survey questions |
| User analytics | Anonymised usage data |
| User Messages | Messages submitted by app users |

---

## Sections

### Encyclopedia

The encyclopedia is the main content library. It is organised as **Categories → Subcategories → Articles**.

`[SCREENSHOT: Encyclopedia page showing categories list]`

#### Categories

- Go to `/encyclopedia` to see all categories
- Click a category to view its subcategories
- Click **Add** to create a new category — provide a title and select a locale

`[SCREENSHOT: Category detail with subcategories list]`

#### Subcategories

- Each category contains subcategories
- Click a subcategory to view its articles

`[SCREENSHOT: Subcategory detail with articles list]`

#### Articles

- Click **Add** on a subcategory page to create a new article
- The article editor includes a rich text field, locale selector, and age restriction settings
- Set **Age Restriction Level** to control which users see the article (All / Age-restricted)

`[SCREENSHOT: Article editor]`

---

### Did You Knows

Short fact cards that appear in the app. Each entry has a body text and a locale.

`[SCREENSHOT: Did You Knows list page]`

- Click **Add** to create a new entry
- Each entry is locale-specific

`[SCREENSHOT: Did You Know editor]`

---

### Avatar Messages

Messages delivered by the Oky avatar within the app. Tied to specific app events or dates.

`[SCREENSHOT: Avatar Messages list]`

- Click **Add** to create a new message
- Select the trigger event and locale

`[SCREENSHOT: Avatar Message editor]`

---

### Quiz

Multiple-choice quiz questions shown in the app.

`[SCREENSHOT: Quiz list page]`

- Click **Add** to create a question
- Add answer options and mark the correct answer
- Set age restriction if needed

`[SCREENSHOT: Quiz editor]`

---

### Survey

Survey questions collecting anonymised responses from app users.

`[SCREENSHOT: Survey list page]`

- Click **Add** to create a survey question
- View responses via the User Analytics section

`[SCREENSHOT: Survey editor]`

---

### Notifications

Push notifications sent to app users.

`[SCREENSHOT: Notifications list page]`

- Click **Add** to schedule a notification
- Set title, body, locale, and send date

`[SCREENSHOT: Notification editor]`

---

### Help Centers

Contact information displayed in the app's help/support section. Entries are region-specific.

`[SCREENSHOT: Help Centers list]`

- Click **Add** to add a help centre entry
- Provide name, phone, URL, and region

`[SCREENSHOT: Help Center editor]`

---

### About

The "About Oky" content shown in the app.

`[SCREENSHOT: About page editor]`

---

### Privacy Policy / Terms and Conditions

Locale-specific legal text displayed in the app.

`[SCREENSHOT: Privacy Policy editor]`

- Each locale has its own entry
- Rich text editor for formatting

---

### Data Management

Tools for importing and exporting content and translations.

`[SCREENSHOT: Data Management page]`

| Button | What it does |
| --- | --- |
| Download content spreadsheet | Exports all content not yet in the `.ts` files |
| Generate content `.ts` file | Downloads a ready-to-use TypeScript content file |
| Generate app translations sheet | Exports app UI string translations |
| Generate CMS translations sheet | Exports CMS UI string translations |
| Upload spreadsheet | Imports content from a spreadsheet |

> Always download the content spreadsheet **before** generating the `.ts` file — see [Updating content](../docs/localisation/updating_content.md).

---

### User Analytics

Anonymised, aggregated usage data from app users. No personally identifiable information is stored.

`[SCREENSHOT: Analytics dashboard]`

---

### User Messages

Messages submitted by app users via the in-app suggestion feature.

`[SCREENSHOT: User Messages list]`

---

### User Management

*(superAdmin role only)*

Create, edit, and delete CMS users.

`[SCREENSHOT: User Management page]`

| Field | Notes |
| --- | --- |
| Username | Unique login name |
| Password | Set a strong password — no minimum enforced by the app |
| Role | `superAdmin` — full access including user management; `contentManager` — content only, no user management |
| Language | Default locale for this user's CMS session |

> **After first login** create a real superAdmin user here and delete the temporary `admin` account.

---

## Roles

| Role | Access |
| --- | --- |
| `superAdmin` | All sections including User Management |
| `contentManager` | All content sections — cannot manage users |

---

## Logging out

Click the **Logout** link in the header or navigate to `/logout`.
