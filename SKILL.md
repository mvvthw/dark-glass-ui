---
name: dark-glass-ui
description: The user's personal UI design language — dark, monotone, Apple-inspired, with liquid-glass surfaces, beveled white primary buttons, and one consistent hover/press system. Use when the user asks to build or restyle a web app, site, dashboard or component "in my style", "dark glass", "liquid glass", "my UI style / design language", or references this skill by name. Includes a drop-in base stylesheet (glass-ui.css).
---

# Dark Glass UI

A universal design language for web apps: **dark, monotone, quiet, tactile.** Think Apple's dark-mode system apps (Freeform, Photos, visionOS chrome) — content first, chrome floats above it as frosted glass, and every interaction feels the same.

Start every project by copying [glass-ui.css](glass-ui.css) into it and building on its tokens and classes. Don't re-derive values; extend the file.

## Principles

1. **Monotone.** Black, grays and white only. The one exception is `--danger` (#ff5a52) for destructive actions. No brand accent colors, no blue selection — selection and focus are white.
2. **Content is the color.** Images, media and user content supply all the visual interest. UI chrome stays neutral and recedes.
3. **Chrome floats.** Toolbars, top bars, context menus and popovers are glass pills/panels floating over content with generous margins (16px from edges), never full-bleed bars.
4. **One interaction language.** Every control hovers, presses and transitions identically (see below). No one-off hover effects.
5. **Restraint.** Subtle is the default: hovers brighten slightly, nothing lifts, bounces or glows. Springy motion is reserved for things *appearing* (popovers, dialogs).
6. **Rounded and soft.** Pills for controls, 22–28px radii for panels/cards, ~14px for media. Nothing should look boxy.

## Tokens (from glass-ui.css)

| Role | Value |
|---|---|
| Page bg / canvas / surfaces | `#000` / `#0b0b0c` / `#121214`, `#1a1a1d` |
| Text 1 / 2 / 3 | `#f5f5f7` / 62% / 32% alpha of `rgb(235 235 245)` |
| Separators | `rgba(255,255,255,.08)` |
| Fills | white at 6% / 10% / 16% |
| Radii | 10 · 16 · 22 · 28 · pill (999) |
| Font | `-apple-system, SF Pro, Inter, system-ui` — Inter loaded from Google Fonts as non-Apple fallback |
| Motion | `--dur` 260ms, `--dur-press` 140ms, `--ease-ui` cubic-bezier(.25,.1,.25,1) |

Typography: large titles 34–46px / 700 / −0.035em tracking; headlines 16px / 600 / −0.02em; body 14px / −0.01em; captions 12px uppercase / 600 / +0.06em. Tight negative tracking on anything ≥15px.

## Liquid glass

`.glass` = translucent white gradient + `backdrop-filter: blur(30px) saturate(170%)` + inner top highlight + deep soft drop shadow, plus two pseudo-elements:
- `::before` — 1px **rim light** via masked gradient border, brightest top-left and bottom-right.
- `::after` — faint radial **specular sheen** from the top-left.

Rules:
- Glass needs something behind it. On empty pages add the `.ambient` backdrop (three slowly drifting blurred gray blobs).
- Direct children need `position: relative; z-index: 1` (already applied via `.glass > *`).
- Over busy content (menus, popovers, dialogs, tag pickers) add `.glass-thick` so text stays legible.
- Use glass for floating chrome only — not for every card. Cards are flat `rgba(255,255,255,.035)` surfaces with a 1px inner ring.

## Interaction system (the important part)

Every interactive element reads one registered custom property, **`--hl`** (hover level): `0` rest → `1` hover → `1.6` pressed/active/editing. All tints are computed from it with `calc()`, and `--hl` itself transitions, so every control animates identically.

- **Hover:** `--hl: 1`. Surface/fill brightens slightly. Only inside `@media (hover: hover)`.
- **Press:** `--hl: 1.6` + `transform: scale(0.97)` (large cards: `0.992`). Press is fast (140ms), release eases back.
- **No lift, no translateY, no glow, no spring on hover.** Cards brighten their ring + surface + shadow together.
- **Hover-revealed actions** (e.g. a card's ••• button) use `.reveal`: fade in only, never scale. Always visible on touch devices.
- **Focus:** 2px white 55% outline, 2px offset, keyboard only (`:focus-visible`).
- When adding a new interactive class, **add it to the preset `:is()` lists** in glass-ui.css instead of writing a bespoke `:hover`.

### Button tiers

| Class | Look |
|---|---|
| `.btn-primary`, `.chip.on` | **Solid white with a clean 3D bevel**: light vertical gradient, bright 1px inner top edge, darker 1px inner bottom edge, hairline inner ring, tight 1px contact shadow + small soft shadow. On press the bevel inverts (inner top shadow). **Never a white glow/halo.** One primary per view. |
| `.btn` | Filled secondary — white at 10% → 16% on hover. |
| `.btn-ghost`, `.icon-btn`, `.toolbar-btn`, `.menu-item`, segmented items | Transparent → white 8.5% fill on hover. |
| `.pill.glass`, `.icon-btn.glass` | Glass that tints itself from `--hl`. |
| `.btn-danger` / `.danger` | Red text on 16% red fill; ghost variants turn red only on hover. |

## Patterns

- **Floating top bar:** left = back pill (`‹ Section`) + title pill; right = status pill + icon buttons. All glass, 40px tall, 16px from edges, no background bar.
- **Bottom dock / toolbar:** centered glass pill (56px) of ghost buttons with icon + label, separated into groups by 1px separators (e.g. create actions | zoom controls).
- **Context bar:** glass pill that appears above (or below, near the top edge) the selected object, with icon buttons; destructive action last.
- **Large-title header:** big title + secondary count line, search pill and one primary button on the right; header sticky with a blurred gradient fade.
- **Cards grid:** `repeat(auto-fill, minmax(260px, 1fr))`, 22px gap, card = media preview inset 8px with its own radius + footer (headline name, secondary meta "3 images · 2h ago").
- **Empty states:** centered glass card with a small layered illustration or icon tile, title, one sentence, primary + secondary button.
- **Dialogs:** thick glass sheet, 26px radius, title + one-line explanation + right-aligned actions (Cancel ghost/secondary, confirm primary or danger). Destructive confirmations name the object and say it can't be undone.
- **Menus:** thick glass, 16px radius, 36px rows with leading icon, danger item last.
- **Chips / tags:** pill chips; selected = beveled white. Tags over images = small dark-glass pills with a hairline white ring.
- **Segmented controls:** dark recessed track, selected segment = translucent white with top highlight.
- **Toasts:** glass pill, top-center, auto-dismiss ~3s.
- **Text fields:** every text input in the app — search, dialog fields, inline forms — is the **same component** (`.field`, wrapped in one `TextField` component in React/etc.): a recessed pill (subtle white fill, inner top shadow, one 1px hairline ring) with an optional leading icon. Hover and focus only brighten that single ring via `--hl`. Never stack a border + focus ring + outline. `.field-sm` for compact contexts (popovers).

### Renaming / in-place editing

Never swap text for a boxed input inside an existing pill or card. Instead:
- The text itself becomes editable: wrap the input in `.inline-edit` with `data-value` mirroring the value so it auto-sizes, inheriting the exact typography. Select all on enter; Enter/blur saves, Esc cancels.
- Make editability discoverable with `.edit-affordance`: a pencil icon that **slides out** of the container on hover, and turns into a **white ✓** while editing (add `.is-editing` to the container, which also raises its `--hl`). Prevent the ✓'s pointerdown from blurring the input first, then blur on click to save.

## Motion

- Hovers/presses: `--ease-ui`, 260ms / 140ms.
- Entrances: `rise` (fade + 14px up + 0.98 scale) with `--ease`, staggered if listing; use `animation-fill-mode: backwards` so it doesn't lock `transform`.
- Popovers, menus, dialogs, toasts: `pop` with `--spring` — the only place overshoot is allowed.
- Always honor `prefers-reduced-motion`.

## Don'ts

- No colored accents, gradients with hue, or blue focus/selection.
- No hover lift/translate, scale-up, glow, or bouncy hover transitions.
- No outer glow on white buttons — bevel only.
- No boxed inputs nested inside pills/cards for inline edits.
- No double/triple focus rings on inputs, and no one-off input styles — one field component everywhere.
- No sharp or 4px corners on media or panels.
- No full-width opaque nav bars; chrome floats.
- No bespoke per-component hover rules outside the preset system.

## Layout & responsiveness

- 16px minimum gutter on phones, `clamp(16px, 5vw, 64px)` page padding on desktop.
- Hide toolbar labels and non-essential status pills under 640px; keep icons.
- Touch targets ≥ 36px; hover-only reveals become always-visible on `(hover: none)`.
