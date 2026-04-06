---
name: ui-mockup-first
description: Generates visual mockups of web views BEFORE writing any logic or backend code. Works with any stack (HTML, Laravel, EJS, HBS). Scans the project first to detect stack, layouts, and components, then builds the mockup using what's already there. Use when the user wants to see a screen or flow before building it, or before adding a feature to an existing module. Triggers: "show me how it would look", "mockup of", "design the view for", "before coding I want to see", "screen for [module]", "view for [module]".
---

# UI Mockup First

## Philosophy

**See it first, then build it.**

Two situations where this matters:

**Building something new** — The dev codes controllers, models, routes, and views — and the client says "a field is missing" or "that's not the flow I wanted". Real code has to be undone.

**Adding to something that exists** — You want to add a button, a form, a new action to an existing module. Moving fast means things slip: a missing input, a skipped validation, a flow that doesn't connect right, a UI that breaks the pattern the rest of the app follows. You catch it after the code is written.

The fix is the same: generate only the views first. Iterate cheaply. Build once it's approved.

---

## Step 0 — Read the project before generating anything

Before writing a single line, scan what already exists. This prevents a mockup that looks nothing like the real app.

### Detect the stack

Check in this order:

```
composer.json          → Laravel (look for "laravel/framework")
package.json           → Node project; check for express, fastify, hapi, etc.
                         also check for EJS, Handlebars, Nunjucks, Pug as view engines
*.blade.php files      → Laravel Blade
*.ejs files            → EJS
*.hbs / *.handlebars   → Handlebars
*.njk files            → Nunjucks
*.pug files            → Pug
No server files found  → plain HTML
```

**If Laravel is detected → prefer the `ui-mockup-first-laravel` skill.** It handles Blade, components, and routes more specifically. Use this skill for Laravel only if that one isn't available.

### Detect the CSS framework

```
package.json / composer.json  → tailwindcss, bootstrap, bulma, foundation, etc.
tailwind.config.js            → confirms Tailwind; note any custom colors or fonts
Any existing HTML/template    → check <link> tags and class names in use
```

Default if nothing found: Tailwind via CDN.

### Detect existing layouts and partials

Look for shared templates the project already uses:

```
Laravel:    resources/views/layouts/,  resources/views/components/
EJS:        views/layouts/, views/partials/, views/_layout.ejs
HBS:        views/layouts/, views/partials/
Nunjucks:   templates/base.njk, templates/layout.njk
Pug:        views/layout.pug, views/mixins/
HTML:       layout.html, base.html, _includes/, partials/
```

**If a shared layout exists → extend or include it in the mockup.**
**If shared components or partials exist → reuse them.**

### After scanning, report before generating:

```
Stack detected: Node + EJS
CSS: Tailwind (package.json)
Layout found: views/layouts/main.ejs — will extend this
Partials found: views/partials/sidebar.ejs, views/partials/navbar.ejs — will include
Generating mockup in: views/mockup/[module]/
```

If the project is new/empty, state that and proceed with sensible defaults.

---

## Step 1 — Identify the needed views

For a typical module:

- `index` → list or table of the resource
- `create` → create form
- `edit` → edit form (can share structure with create)
- `show` / `detail` → read-only view of a record
- `[special-name]` → any non-CRUD view the user describes

For adding a feature to an existing module: identify only the affected views — don't regenerate the whole module, just what changes.

If ambiguous, ask at most 2 questions before generating.

---

## Step 2 — Generation rules

### ✅ DO include
- The stack's native template syntax (Blade `@foreach`, EJS `<% %>`, HBS `{{#each}}`, etc.)
- The CSS framework detected in Step 0 — never mix frameworks
- The existing layout/partials found in Step 0 — extend or include them
- Existing components or shared elements — reuse them, don't duplicate
- **Realistic** fake data adapted to the module's domain (see Step 4)
- Navigation between mockup views
- Intent comments marking what would be functional: `<!-- MOCKUP: dropdown from catalog here -->`
- Important visual states: empty table, form with error, save confirmation

### ❌ DO NOT include
- Real routes, controllers, or backend handlers
- Database queries or ORM calls
- Real form actions pointing to functional endpoints
- Functional validations (only the visual representation of an error state)
- Auth logic or session checks
- Complex JS (only basic UI behavior: menu toggle, active tab, accordion)

### Fake data — format by stack

**HTML / EJS / HBS / Nunjucks:**
Hardcoded directly in the template. No fetch calls, no scripts.
```html
<!-- MOCKUP: simulated data -->
<!-- CLI-00123 | María González | Active | 2026-03-15 -->
```

**Blade (Laravel):**
```blade
{{-- MOCKUP: simulated data, not from DB --}}
@php
$records = [
    ['id' => 'CLI-00123', 'name' => 'María González', 'status' => 'Active'],
    ['id' => 'CLI-00124', 'name' => 'Carlos Ramírez', 'status' => 'Pending'],
];
@endphp
```

**EJS:**
```ejs
<%
const records = [
  { id: 'CLI-00123', name: 'María González', status: 'Active' },
  { id: 'CLI-00124', name: 'Carlos Ramírez', status: 'Pending' },
];
%>
```

---

## Step 3 — Folder structure

Match the project's existing view organization. General patterns:

```
Laravel:    resources/views/mockup/[module]/
EJS / HBS:  views/mockup/[module]/
Nunjucks:   templates/mockup/[module]/
Plain HTML: mockup/[module]/
```

Each module folder contains the views plus a `README.md`.

---

## Step 4 — Realistic fake data

Adapt to the module's domain:

- **Names** → "María González", "Carlos Ramírez", "Ana Herrera", "Luis Mendoza"
- **Dates** → close to today's date, readable format
- **Amounts** → $1,250.00, $34,800.00 — never bare "100"
- **Statuses** → 3-4 variants: Active, Inactive, Pending, Cancelled
- **IDs / references** → "CLI-00123", "ORD-2024-042" — never "1", "2", "3"
- **Descriptions** → short text that makes real sense in context

Coherent across views: same record in the index and in the detail. Min 3-5 rows in tables.

**Never use:** Lorem ipsum, Test, Sample, Example1, item 1.

---

## Step 5 — Mockup README.md

Generate alongside the views:

```markdown
# Mockup: [Module name]

**Status:** Under review
**Date:** [current date]
**Stack:** [detected]
**CSS:** [detected]

## Included views

| View | File | Description |
|------|------|-------------|
| List | index | Table with all records |
| Create | create | Create form |
| Edit | edit | Edit form |
| Detail | show | Read-only view |

## Project elements reused

[List layouts, partials, components from the existing project that were used]
[If none: "No existing templates found — self-contained layout used"]

## How to view locally

[Adapt to detected stack]
- HTML: open index.html directly, or: python3 -m http.server 8080
- Node: node server.js (or npm run dev) then navigate to /mockup/[module]
- Laravel: add routes to web.php, then php artisan serve

## Pending before implementation

- [ ] Confirm form fields
- [ ] Define possible statuses
- [ ] Validate navigation flow

## Design notes

[Decisions made or questions for the client]
```

---

## Iteration flow

- **Field or layout change** → edit only the affected view
- **Flow change** → update navigation links
- **New view** → generate the file, update the README
- **"It's approved, let's build it"** → now generate real routes, controllers, backend logic. The mockup is the spec.

---

## Final notes

- The mockup should look like it belongs in the app — because it uses the app's own pieces
- Prioritize iteration speed over code perfection
- If the user isn't sure what they want, ask 2 key questions before generating — no more
- Always end with: "What do we change?" to invite iteration
