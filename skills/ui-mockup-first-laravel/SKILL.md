---
name: ui-mockup-first-laravel
description: Generates Laravel Blade mockups BEFORE writing any controllers, models, or database code. Scans the existing project first — detects layouts, Blade components, CSS framework, and conventions — then builds the mockup on top of what's already there. Use when the user wants to see a screen or flow before building it in Laravel, or before adding a feature to an existing module. Triggers: "show me how it would look", "mockup of", "design the view for", "before coding I want to see", "screen for [module]", "view for [module]".
---

# UI Mockup First — Laravel

## Philosophy

**See it first, then build it.**

The classic problem: the dev codes controllers, models, routes, and Blade views — and the client says "a field is missing" or "that's not the flow I wanted". Work and tokens wasted.

The solution: generate **only the Blade views** first — navigable, with realistic fake data. No controllers, no DB, no real routes. Once the client or team approves, *then* you build.

This skill is Laravel-native: Blade syntax, proper folder conventions, and — critically — it reads the project before generating anything so the mockup looks like the actual app, not a disconnected prototype.

---

## Step 0 — Read the project before generating anything

Before writing a single line, scan the project. This is what separates a useful mockup from one that looks nothing like the real app.

### What to check

**CSS / visual framework:**
```
package.json          → tailwindcss, bootstrap, etc.
tailwind.config.js    → custom colors, fonts, breakpoints
vite.config.js        → asset pipeline clues
resources/css/app.css → custom utility classes
```

**Existing layouts:**
```
resources/views/layouts/         → app.blade.php, guest.blade.php, admin.blade.php
resources/views/components/layouts/
```
If a layout exists → the mockup views will extend it with `@extends('layouts.app')` (or whichever applies).
If no layout exists → create a simple self-contained `x-mockup-layout` component.

**Existing Blade components:**
```
resources/views/components/      → x-* anonymous components
app/View/Components/             → class-based components
```
List what's available: buttons, cards, modals, badges, inputs, alerts, tables, pagination, etc.
**If a component exists that fits → use it directly. Don't recreate it.**

**Starter kit or UI package:**
```
composer.json  → look for: laravel/jetstream, laravel/breeze, filament/filament,
                            spatie/*, livewire/livewire, wire:*, etc.
```
- Jetstream / Breeze → layouts and components already exist, use them
- Filament → check if mockup should be inside the admin panel or separate
- Livewire → basic `wire:` attributes are OK if the project uses it, but keep logic minimal

**Route and naming conventions:**
```
routes/web.php   → existing route prefixes, naming patterns (admin., app., etc.)
```
Match the existing conventions for the mockup route prefix.

**View folder structure:**
```
resources/views/   → how are views organized? modules? features? flat?
```
Mirror that structure under `mockup/`.

### After scanning, report briefly before generating:
```
Layout found: resources/views/layouts/app.blade.php — will extend this
Components found: x-button, x-card, x-badge, x-input, x-modal — will reuse where applicable
CSS: Tailwind (tailwind.config.js) — custom color "brand-primary" detected
Starter kit: Laravel Breeze
Route prefix in use: none — will use "mockup" prefix
View structure: flat under resources/views/ — will create resources/views/mockup/[module]/
Generating...
```

If the project is brand new (empty), state that and proceed with sensible defaults.

---

## Step 1 — Identify the needed views

For a typical module the base views are:

- `index` → list or table of the resource
- `create` → create form
- `edit` → edit form (can share structure with create)
- `show` → read-only view of a record
- `[special-name]` → any non-CRUD view the user describes

If the user describes a complete flow, map the views yourself. If ambiguous, ask at most 2 things before generating.

---

## Step 2 — Generation rules

### ✅ DO include
- Blade syntax: `@extends`, `@section`, `@foreach`, `@if`, `@php` for fake data
- **Existing layout** found in Step 0 — extend it, don't create a parallel one
- **Existing components** found in Step 0 — use `<x-button>`, `<x-card>`, etc. wherever they fit
- Only create new `x-mockup-*` components for things that don't exist yet
- The project's CSS framework and custom classes from Step 0
- **Realistic** fake data in `@php` blocks at the top of each view
- Navigation between mockup views via mockup routes
- Intent comments: `{{-- MOCKUP: catalog dropdown goes here --}}`
- Important visual states: empty table, form with error, save confirmation

### ❌ DO NOT include
- Real controllers or models
- Eloquent queries or DB calls
- Real form `action=` pointing to real endpoints
- Functional validations (only visual error state)
- `@auth`, `@can`, auth middleware
- Functional `@csrf` in forms — add `{{-- MOCKUP: @csrf would go here --}}` instead
- Complex Livewire or Alpine.js logic (only basic UI: menu toggle, active tab)

### Fake data pattern
```blade
{{-- MOCKUP: simulated data, not from DB --}}
@php
$records = [
    ['id' => 'CLI-00123', 'name' => 'María González', 'email' => 'maria@empresa.com', 'status' => 'Active',   'created_at' => '2026-03-15'],
    ['id' => 'CLI-00124', 'name' => 'Carlos Ramírez', 'email' => 'carlos@empresa.com', 'status' => 'Pending',  'created_at' => '2026-03-18'],
    ['id' => 'CLI-00125', 'name' => 'Ana Herrera',    'email' => 'ana@empresa.com',    'status' => 'Active',   'created_at' => '2026-03-20'],
    ['id' => 'CLI-00126', 'name' => 'Luis Mendoza',   'email' => 'luis@empresa.com',   'status' => 'Inactive', 'created_at' => '2026-03-22'],
];
@endphp
```

---

## Step 3 — Folder structure

```
resources/views/
└── mockup/
    ├── components/          ← only components that DON'T already exist in the project
    │   └── [only what's needed]
    └── [module]/
        ├── index.blade.php
        ├── create.blade.php
        ├── edit.blade.php
        ├── show.blade.php
        └── README.md
```

**The `mockup/components/` folder only gets created if the project lacks the components needed.** If the project already has `x-card`, `x-badge`, `x-button` — don't duplicate them.

Routes in `routes/web.php`:
```php
// MOCKUP ROUTES — remove before production
Route::prefix('mockup')->name('mockup.')->group(function () {
    Route::view('/[module]',        'mockup.[module].index')->name('[module].index');
    Route::view('/[module]/create', 'mockup.[module].create')->name('[module].create');
    Route::view('/[module]/edit',   'mockup.[module].edit')->name('[module].edit');
    Route::view('/[module]/show',   'mockup.[module].show')->name('[module].show');
});
```

Adjust the prefix if the project uses a different convention (e.g., `admin.mockup.`).

---

## Step 4 — When to create new mockup components

Only create `x-mockup-*` components when the project doesn't already have something equivalent.

| Need | Check first | Create only if missing |
|------|-------------|------------------------|
| Page title + action button | `x-page-header`, `x-header` | `x-mockup-page-header` |
| Colored status pill | `x-badge`, `x-status` | `x-mockup-status-badge` |
| KPI / summary card | `x-card`, `x-stat-card` | `x-mockup-stat-card` |
| Base layout | `layouts/app.blade.php` | `x-mockup-layout` |
| Sidebar | `x-sidebar`, `partials/sidebar` | `x-mockup-sidebar` |

When creating new components, use props that mirror what a real production component would receive. The `mockup-` prefix keeps them isolated until they're promoted to real components.

### Example: status-badge (create only if no badge component exists)
```blade
{{-- resources/views/mockup/components/status-badge.blade.php --}}
@props(['status'])
@php
$colors = [
    'Active'    => 'bg-green-100 text-green-800',
    'Inactive'  => 'bg-gray-100 text-gray-600',
    'Pending'   => 'bg-yellow-100 text-yellow-800',
    'Cancelled' => 'bg-red-100 text-red-800',
];
$class = $colors[$status] ?? 'bg-gray-100 text-gray-600';
@endphp
<span class="inline-flex items-center px-2.5 py-0.5 rounded-full text-xs font-medium {{ $class }}">
    {{ $status }}
</span>
```

---

## Step 5 — Realistic fake data

Adapt data to the module's domain:

- **Names** → "María González", "Carlos Ramírez", "Ana Herrera", "Luis Mendoza"
- **Dates** → close to today's date, in a readable format
- **Amounts** → formatted: $1,250.00, $34,800.00 — never bare "100"
- **Statuses** → 3-4 variants coherent with the module: Active, Inactive, Pending, Cancelled
- **IDs / reference numbers** → realistic format: "CLI-00123", "ORD-2024-042" — never "1", "2", "3"
- **Descriptions** → short text that makes real sense

Data must be coherent across views: the record in the index is the same one in the show view. Minimum 3-5 records in tables.

**Never use:** Lorem ipsum, Test, Sample, Example1, item 1.

---

## Step 6 — Mockup README.md

Generate this alongside the views:

```markdown
# Mockup: [Module name]

**Status:** Under review
**Date:** [current date]
**Stack:** Laravel Blade + [CSS framework detected]

## Included views

| View | File | Route |
|------|------|-------|
| List | index.blade.php | /mockup/[module] |
| Create | create.blade.php | /mockup/[module]/create |
| Edit | edit.blade.php | /mockup/[module]/edit |
| Detail | show.blade.php | /mockup/[module]/show |

## Project elements reused

| Element | Type | Source |
|---------|------|--------|
| [layout name] | Layout | resources/views/layouts/[file] |
| x-[component] | Component | resources/views/components/[file] |

## New mockup components created

| Component | File | Promote to real component? |
|-----------|------|---------------------------|
| x-mockup-[name] | mockup/components/[file] | [ ] |

[If none: "No new components created — existing project components used throughout"]

## How to view locally

1. Add mockup routes to routes/web.php
2. php artisan serve
3. http://localhost:8000/mockup/[module]

## Pending before implementation

- [ ] Confirm form fields
- [ ] Define possible statuses
- [ ] Validate navigation flow
- [ ] Decide which mockup components get promoted to real components

## Design notes

[Decisions made or questions for the client]
```

---

## Iteration flow

- **Field or layout change** → edit only the affected view
- **Flow change** → update navigation links and mockup routes
- **New view** → generate the file, add the route, update the README
- **"It's approved, let's build it"** → generate controllers, models, migrations. Mockup components that were created can be promoted to real components — rename, remove the `mockup-` prefix, wire to real data.

---

## Final notes

- The mockup should look like it belongs in the app — because it's built with the app's own pieces
- Reusing existing components means the approved mockup is already 80% of the real UI
- If the user isn't sure what they want, ask 2 key questions before generating — no more
- Always end with: "What do we change?" to invite iteration
