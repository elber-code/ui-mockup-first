# ui-mockup-first

A methodology and set of Claude skills for generating visual mockups **before** writing any logic, backend, or database code.

---

## Skills

| Skill | Stack | Description |
|-------|-------|-------------|
| [ui-mockup-first](./skills/ui-mockup-first/) | Any (HTML, EJS, HBS, Laravel, …) | Base skill — detects your stack and generates mockups for any framework |
| [ui-mockup-first-laravel](./skills/ui-mockup-first-laravel/) | Laravel (Blade) | Laravel-specific: reads your layouts, components, and conventions before generating |

More framework-specific variants coming.

---

## The problem

Two situations where this saves you:

**1. Building something new from scratch**

You have an idea. You ask an AI to build it. The AI generates controllers, models, migrations, routes, and views — and when you finally see it running, you realize:

- A field is missing
- The flow isn't what you had in mind
- The layout doesn't work for that use case
- The client says "that's not it"

Now you have to redo real code. Not a sketch — actual backend logic with database calls. That's expensive in time, energy, and tokens.

**2. Adding a feature or action to something that already exists**

You want to add a new button, a new form, a new flow to an existing module. It seems small so you just ask the AI to implement it directly. But moving fast means things slip through:

- An input that should be there isn't
- A validation that seemed obvious was skipped
- The new action doesn't connect properly to the rest of the flow
- The UI breaks a pattern the rest of the app follows

You catch it after the code is written, not before. Now you're fixing real code instead of adjusting a view.

**The fix is the same in both cases: see it before you build it.**

---

## The approach

Generate **only the views** first. Static, navigable, with realistic fake data. No controllers, no database, no routes that do anything real. Just the visual interface — enough to open it in a browser, click through it, and say "yes, this is it" or "no, that input is missing" or "this flow is wrong."

Once it's approved, *then* you build. The mockup becomes the spec.

Iterating over a static view is fast and cheap. Iterating over functional code connected to a database is not.

---

## How it works with an existing project

The skills don't generate into a vacuum. Before writing anything, they scan the project:

- What stack and CSS framework is in use
- What layouts already exist
- What components are already built
- What folder and naming conventions the project follows

If your project already has a layout, a component library, a sidebar — the mockup uses those. Not new parallel files that look different from the rest of the app.

The mockup looks like your app. That's the point.

---

## Recommended flow

```
Idea (new module or new feature in an existing one)
  ↓
Skill scans the project (stack, layouts, components)
  ↓
Generates mockup views using what's already there
  ↓
Open it in the browser — click through every state
  ↓
Catch what's missing or wrong → AI adjusts (fast, cheap)
  ↓
Approved ✓
  ↓
Build the real backend — the mockup is the spec
```

---

## Installation

```bash
# Instala todos los skills (te pregunta cuál elegir)
npx skills add https://github.com/elber-code/ui-mockup-first

# Solo el skill base (cualquier stack)
npx skills add https://github.com/elber-code/ui-mockup-first --skill ui-mockup-first

# Solo el skill de Laravel
npx skills add https://github.com/elber-code/ui-mockup-first --skill ui-mockup-first-laravel
```

> 📄 [Versión en español → README_es.md](./README_es.md)
