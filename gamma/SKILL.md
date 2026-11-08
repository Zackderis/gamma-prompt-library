---
name: gamma
description: >-
  Interactive Gamma design prompt library. Run /gamma to open a visual menu of 52 ready-to-run
  prompts across 7 design categories — Business Strategy, Sales & Client Acquisition, Content &
  Personal Brand, Productivity & Systems, Career & Growth, Personal Life, and Fun & Viral — then
  pick a prompt (or type a custom idea), fill in your details, and build the finished visual in
  Gamma. Use this WHENEVER the user types /gamma, asks to design or create a visual / deck /
  roadmap / dashboard / diagram / map / one-pager with Gamma, asks for the Gamma prompt library or
  menu, or says "design something with Gamma" / "build this in Gamma". Requires the Gamma
  connector to be enabled in the chat.
---

# Gamma Design Skill

This skill replaces the old copy-paste prompt library. Instead of opening a doc, copying a prompt,
and pasting it back, the user runs `/gamma`, taps what they want to design in a visual menu, answers
a few quick questions, and the finished visual is built in Gamma — all in one conversation.

**Files**
- `assets/gamma-menu.html` — the interactive menu widget (7 categories + Custom + a 7-tab browser of
  all 52 prompts). It is self-contained widget markup (`<div>` + `<style>` + `<script>`) and calls
  `sendPrompt()` on each tap. Render it as-is with the `show_widget` tool; do not rewrite it.
- `references/prompt-library.md` — the full text of all 52 prompts, organised by category with IDs
  like `3.4`. This is the source of truth — read the exact prompt from here after the user picks.

## Flow

### Step 1 — Open the menu
When the user runs `/gamma` (or asks to design something with Gamma), display the interactive menu
as a **widget** so that tapping a prompt sends it straight to the chat automatically:
- Render the full contents of `assets/gamma-menu.html` using the **`show_widget`** tool (the
  "visualize" / interactive-widget tool). Read the file and pass its contents as `widget_code`
  **exactly as-is** — it is already self-contained widget markup (a `<div>` + `<style>` + `<script>`
  fragment); do not add `<html>`/`<head>`/`<body>`, and do not redesign or rewrite it. Use a title
  like `gamma_menu`.
- **Do NOT use the Artifact tool for this.** Artifact pages cannot send messages back to the chat, so
  taps would do nothing. `show_widget` provides the `sendPrompt()` bridge the menu relies on, so a tap
  is sent instantly as the user's next message.
- Add one short line under it, e.g. *"Pick a category, browse all prompts, or type a custom idea 👇"*.

The widget does the rest: each prompt (and the Custom box) calls `sendPrompt(...)` on tap, which posts
the user's choice to the chat as the next message — so you'll simply receive their selection and
continue at Step 2. The user can also just tell you a category or prompt name in text — handle that the
same way.

### Step 2 — Look up the chosen prompt
When a **build request** comes back (`Category: … / Prompt: "…"`):
- Open `references/prompt-library.md` and find that exact prompt by its title under that category.
  Use its full text verbatim — do not paraphrase or shorten it.

### Step 3 — Fill in every blank (ask for each one)
The prompts contain fill-ins in square brackets, e.g. `[industry]`, `[service offering]`,
`[target audience]`. **Ask the user to provide a value for every bracket** before building — do not
guess or auto-fill them. Keep it quick: list the blanks in one message as short questions (or ask
one at a time if there are only one or two). If a bracket offers example options (e.g.
`[timeframe, e.g. 6 months, 1 year]`), show those as suggestions.

### Step 4 — Show the completed prompt and confirm
Replace every bracket with the user's answers. Show the final, filled-in prompt in a code block and
ask: **"Build this in Gamma now?"** with a nudge that they can edit any wording first. Wait for a
yes.

### Step 5 — Build it in Gamma
On confirmation, run the completed prompt through the **Gamma connector** to generate the visual
(deck, roadmap, dashboard, diagram, etc.). Keep the prompt's "clean, visual, Gamma-ready" intent
intact. If the Gamma connector isn't enabled, tell the user how to turn it on: in a chat, click the
**+** in the prompt bar → **Connectors** → toggle **Gamma** on, then try again.

### Custom requests
If the user picks **Custom** (or the panel sends a *custom request*): take their description and
write a brand-new prompt in the exact style of the library — one paragraph, framed for a clean,
visual, Gamma-ready output, telling Gamma what to include and how to structure it. Identify the
details you need from them (the equivalent of the brackets), ask for each, then confirm and build
exactly as in steps 4–5.

### After building
Offer to design another: they can run `/gamma` again to reopen the menu, or just name another prompt.

## Rules
- **Never skip the fill-in step** — every bracket gets a real value from the user before building.
- **Always confirm** the completed prompt before building in Gamma.
- Use each prompt's full library text verbatim; only the brackets change.
- Render the menu with **`show_widget`** (never the Artifact tool) and unchanged — `show_widget`
  provides the `sendPrompt()` bridge, so each tap is sent to the chat automatically. Artifact pages
  can't message the chat, which would make taps do nothing.
- Keep the outputs visual and Gamma-ready; that's the whole point of the library.
