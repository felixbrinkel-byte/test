# CLAUDE.md — AI Assistant Guide for CoCrafter

This file documents the codebase structure, conventions, and development workflows for AI assistants working in this repository.

---

## Project Overview

**CoCrafter** is a German-language B2B construction subcontractor marketplace prototype. It connects general contractors (who post projects) with subcontractors (specialized trade professionals) who browse and apply to projects.

The application is a **single-file static SPA** — all HTML, CSS, and JavaScript live in `index.html`. There is no backend, no build system, and no external dependencies beyond a Google Fonts CDN import.

---

## Repository Structure

```
/
├── index.html      # Entire application (HTML + CSS + JS, ~1,050 lines)
├── README.md       # Minimal placeholder README
└── CLAUDE.md       # This file
```

---

## Technology Stack

| Layer      | Technology                              |
|------------|-----------------------------------------|
| Markup     | HTML5 (semantic)                        |
| Styling    | CSS3 with custom properties (variables) |
| Logic      | Vanilla JavaScript (ES6+)               |
| Fonts      | Google Fonts — Montserrat (CDN)         |
| Backend    | None (all data is client-side mockup)   |
| Build tool | None (open directly in a browser)       |

---

## Application Architecture

The app is a **multi-screen SPA** with four screens toggled by showing/hiding `<div>` elements.

### Screens

| Screen ID          | Description                                         |
|--------------------|-----------------------------------------------------|
| `screen-landing`   | Marketing landing page with CTA                     |
| `screen-register`  | Multi-field subcontractor registration form         |
| `screen-dashboard` | Subcontractor dashboard (Projects, Profile, Applications tabs) |
| `screen-admin`     | Admin view for managing projects and applications   |

### Global State (JavaScript)

```js
let currentScreen = 'landing';  // Active screen name
let isAdmin = false;             // Whether admin view is active
let profileCompletion = 20;      // Profile completeness percentage (0–100)
let profileData = {              // Which profile sections are complete
  company: false,
  details: false,
  docs: false,
  refs: false
};
let currentProfileTab = 0;       // Active tab index in profile modal
let uploads = {};                // Simulated file upload state
```

### Sample Data

```js
const projects = [/* 12 construction project objects */];
// Each project: { title, loc, gewerk, budget, time, tags, hot? }
```

### Key Functions

| Function                  | Purpose                                               |
|---------------------------|-------------------------------------------------------|
| `showScreen(name)`        | Switch between the four main screens                  |
| `updateNav()`             | Refresh navigation based on screen and user role      |
| `toggleView()`            | Toggle between admin and subcontractor views          |
| `submitRegistration()`    | Validate and process the registration form            |
| `toggleChip(el)`          | Select/deselect trade chips (Gewerke)                 |
| `showDashTab(tab)`        | Switch dashboard tabs                                 |
| `renderProjects()`        | Render and filter the project grid                    |
| `openProfileModal()`      | Open the profile completion wizard                    |
| `showProfileTab(i)`       | Navigate profile wizard tabs                          |
| `saveProfile()`           | Save profile data and recalculate completion %        |
| `updateProfileBanner()`   | Update the circular progress indicator                |
| `simulateUpload(id)`      | Simulate a file upload with progress                  |
| `openApplyModal(i)`       | Open the application modal for a project              |
| `submitApplication()`     | Submit a project application                          |
| `showToast(msg)`          | Display a transient toast notification                |

---

## CSS Conventions

- **Custom properties** are defined on `:root` and used via `var(--name)`. Key variables include `--orange`, `--white`, `--dark`, `--gray`, etc.
- **Class naming** follows a BEM-inspired style: `.modal-header`, `.project-grid`, `.gewerk-chip`.
- **Layout** uses Flexbox and CSS Grid throughout.
- **Responsive** breakpoints via media queries (primarily mobile-first adjustments).
- **Indentation:** 4 spaces.

---

## JavaScript Conventions

- **Naming:** camelCase for all variables and functions.
- **Section headers:** Uppercase comments, e.g., `// === STATE ===`, `// === SCREEN MANAGEMENT ===`.
- **Event binding:** Inline `onclick` attributes in HTML (not `addEventListener`).
- **DOM access:** `document.getElementById()` and `querySelector()` — no library abstractions.
- **No modules:** All code is in a single `<script>` block; no ES modules or imports.
- **Semicolons:** Inconsistently present — do not enforce either way when editing.
- **Validation:** Uses `alert()` for form errors (prototype-appropriate, not production).

---

## Trade Options (Gewerke)

The 12 supported construction trades hard-coded in the registration form and project data:

`Rohbau`, `Trockenbau`, `Elektro`, `SHK`, `Maler`, `Fliesen`, `Dachdecker`, `Zimmerer`, `Tiefbau`, `Gerüstbau`, `Estrich`, `Abbruch`

---

## Development Workflow

### Running the App

No build step required. Open `index.html` directly in any modern browser:

```bash
# macOS / Linux
open index.html
# or serve via a simple HTTP server
python3 -m http.server 8080
```

### Making Changes

1. Edit `index.html` directly — HTML, CSS, and JS are all in this one file.
2. Refresh the browser to see changes.
3. There is no hot-reload; manual refresh is required.

### Testing

There is no automated test suite. Manually verify:

- Landing page renders and CTA navigates to registration
- Registration form validates all required fields
- Trade chip selection works (at least one required)
- Distance slider updates its displayed value
- Successful registration navigates to the dashboard
- Dashboard tabs (Projects, Profile, Applications) switch correctly
- Profile modal opens, tabs navigate, and saving updates the completion %
- Project cards blur when profile < 60% complete
- Apply modal opens, fills, and submits correctly
- Toast notifications appear and auto-dismiss
- Admin toggle switches between admin and subcontractor views

### Git Workflow

- **Main branch:** `master`
- **Feature branches:** Named `claude/<description>-<id>` for AI-assisted work
- **Commits** are GPG-signed via SSH key
- **PRs** are used to merge feature branches into master

```bash
# Standard workflow
git checkout -b claude/<feature-name>-<session-id>
# ... make changes ...
git add index.html
git commit -m "Descriptive message"
git push -u origin <branch-name>
```

---

## Key Constraints and Limitations

- **Single file:** Keep all code in `index.html` unless there is a compelling reason to split (e.g., a dedicated `style.css` or `app.js`). Do not create new files without explicit instruction.
- **No dependencies:** Do not add npm packages, CDN libraries, or external APIs without explicit instruction.
- **No backend:** All data is in-memory and lost on page refresh. Do not implement persistence unless asked.
- **German language:** All UI text is German. Keep additions consistent in language.
- **Prototype quality:** Prefer working over polished. `alert()` for errors and simulated uploads are acceptable.

---

## Future Development Areas (Not Yet Implemented)

- Persistent storage (localStorage or a real backend)
- Real user authentication
- Actual file upload handling
- Email validation / verification flow
- Multi-language (i18n) support
- Automated tests
- CI/CD pipeline
- Production build optimization (minification, bundling)
