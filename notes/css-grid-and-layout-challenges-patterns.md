# CSS Grid & Layout — patterns

Deliberate practice log: one entry per challenge. Each pattern has a cue (when to reach for it), the mistake I actually made, the correct mental model, and a drill to repeat it.

---

## C1 — User profile

**Gap labels during the challenge:** detail → detail (columns right from the start; alignment and spacing needed fixing).

### 1. `align-items` goes on the parent of the things you want aligned

- **Cue:** "center X next to Y vertically."
- **My mistake:** I put `align-items: center` on `.profile__info`. Its children (`h1`, `p`) got centered, but each one inside its own row, so they drifted apart instead of sitting together in the middle.
- **Mental model:** `align-items` aligns a container's **children inside their own cells**. The avatar and `.profile__info` are siblings, so the alignment goes on their shared parent (`.profile__header`). By default a grid item stretches to fill its cell, and the rows of a stretched grid stretch too (`align-content: normal`).
- **Drill:** before writing `align-*`, say out loud: "who are the children, and who is their parent?" Then confirm with the DevTools overlay that the highlighted item is smaller than its cell.

### 2. Space comes from `gap`, never from leftover margins

- **Cue:** any space between grid items.
- **My mistake:** the space between the header and the stats came from the browser's default `ul` margin, not from `gap`. Default margins on `h1` and `p` also threw off the vertical centering.
- **Mental model:** if a gap should exist, the parent should be a grid with `gap`. Reset user-agent `margin` on `h1`, `p`, `ul`, and also `padding` and `list-style` on `ul`, so only `gap` controls spacing.
- **Drill:** after writing a layout, set `gap: 0` on every grid. If space is still there, something other than `gap` is creating it.

### 3. Nested grids, one job each

- **Cue:** a component with several distinct arrangements, like side-by-side on top and in a row below.
- **What worked:** `.profile` stacks the sections, `.profile__header` places the avatar next to the text, `.profile__info` stacks the name over the bio, and `.profile__stats` makes 3 columns.
- **Mental model:** a grid only controls its **direct** children. Each level of the layout needs its own grid. If an element does several layout jobs at once, split it.

### 4. `auto 1fr` for fixed media next to fluid text

- **Cue:** an image, icon or avatar beside text that should take up the remaining space.
- **Mental model:** `auto` sizes the column to its content (the avatar), and `1fr` takes what's left.
- **Edge case:** at 320px a 160px avatar plus a 2rem gap leaves about 130px for the bio. Fixed media eats narrow screens, so revisit this in C4 (media queries).

### 5. `1fr` is not "equal" — it's `minmax(auto, 1fr)`

- **Cue:** "N equal columns."
- **Mental model:** the `auto` minimum means a column never shrinks below its longest word. On narrow screens "Followers" can widen its column. Use `repeat(3, minmax(0, 1fr))` when columns must stay equal.
- **Status:** not applied in C1 yet. Test it at 320px.

### Self-check before the next challenge

- [ ] Without looking: which property centers items vertically in their cells, and on which element does it go?
- [ ] Which user-agent styles do I reset before a grid layout?
- [ ] What does `1fr` expand to, and when does that matter?

---

## C2 — Bento grid

**Gap labels during the challenge:** detail → detail → missing tool (row height) → detail → missing composition (auto-placement) → needed a direct walkthrough.

### 1. Row height: `minmax(min, auto)`, not a fixed container height

- **Cue:** "tiles need a sensible height even when they're almost empty," and they still have to grow with content.
- **My mistake:** first `grid-template-rows: 1fr 1fr 1fr` with no container height. `1fr` had no free space to divide, so the rows collapsed to their content. Then I patched it with `height: 300px` on `.bento`, which overflows as soon as Hero gets more text.
- **Mental model:** `fr` divides **free** space. With no height on the container there is no free space, so `1fr` acts like `auto`. A row that needs a floor and should grow with its content is `minmax(100px, auto)`: `min` is the guarantee, `auto` means "as tall as the content." Let the container size itself from its rows, not the other way around.
- **Drill:** add 3–4 paragraphs to the biggest tile. Its rows should grow, and the others should stay at their minimum.

### 2. Auto-placement: fully placed items go first, and the cursor never goes back

- **Cue:** "which items actually need a position?"
- **My mistake:** in v2 I only shortened the `:nth-child` rules (`3/4` → `3`) instead of deleting them. When I deleted everything, the layout fell apart: with only `grid-row: span 2`, Tall landed in column 3, not 4, and every tile after it moved.
- **Mental model:** with the default `grid-auto-flow: row`:
  1. Items with an explicit **row and column** are placed first.
  2. Everything else follows in HTML order. A cursor moves right and down and **never goes back** to holes it left behind.

  So only the item that auto-placement would put in the wrong spot gets a full position. In C2 that is Tall (`grid-column: 4; grid-row: 2 / span 2`). Hero and Wide need only `span`, and the 1×1 tiles need nothing.
- **Drill:** draw the target map, then walk the cursor through it in HTML order on paper. The first tile that lands in the wrong place is the one that needs explicit placement. Confirm by deleting rules one at a time with the overlay on.

### 3. Explicit first, then refactor down

- **Cue:** a new layout I'm not sure about.
- **What worked:** v1 placed every tile with `start / end` line numbers. Once that matched the map, v2 showed what could go: repeated tracks became `repeat()`, and `start / end` became `span`.
- **Mental model:**
  - `grid-column: 4` sets only the **start line**; the end defaults to `auto`, which is one track. `span 2` sets only the **size**, and auto-placement picks the start. `3 / span 2` sets both.
  - When Prettier breaks a line of identical tracks, use `repeat()`.

### 4. Things that couple CSS to markup or viewport

- **`:nth-child` placement:** tied to HTML order. Reorder the HTML and positions jump to other elements. Prefer classes on the items that need a position.
- **Auto-placement also depends on HTML order:** Wide lands in row 1 only because it comes right after Hero. Open question: what happens when Tall moves to the end of the HTML? Answer from rule 2, then check.
- **`width: 600px`:** horizontal scroll below 600px. Use a max width instead.
- **`repeat(4, 1fr)`:** same `minmax(auto, 1fr)` trap as C1 #5. One long word in a 1×1 tile widens its column.

### Self-check before the next challenge

- [ ] Why does `1fr` in `grid-template-rows` do nothing when the container has no height?
- [ ] In what order does the grid place items, and why does an item with only `span` sometimes leave a hole?
- [ ] What's the difference between `grid-column: 4`, `grid-column: span 2` and `grid-column: 3 / span 2`?

---

## C3 — Page layout

**Gap labels during the challenge:** missing tool (sticky footer) + detail (column widths) → detail (rows, height) → missing tool (`minmax()` outside track sizing).

### 1. Areas describe placement, columns describe size

- **Cue:** "main should be wider than the sidebars."
- **My mistake:** I made main wider by repeating its name: `"sidebar main main aside"`, with no `grid-template-columns`. The implicit columns are `auto`, so their widths came from content. Main was wide only because it had the most text. A long link in the sidebar would have changed the proportions.
- **Mental model:** the areas string is a **map** (what goes where). Track sizes are a **separate decision** in `grid-template-columns` / `grid-template-rows`. One column per real column of the layout (`1fr 2fr 1fr`), not one per "unit of width."
- **Payoff:** swapping the sidebar to the right is a one-word change in the string (`"aside main sidebar"`). Proportions stay because the sizes live on the tracks, not in the map.
- **Drill:** write any areas layout, then delete `grid-template-columns` and watch the widths follow the content. Put a long word into the narrowest area.

### 2. Sticky footer: `min-height` on the container + `auto 1fr auto` rows

- **Cue:** "footer at the bottom of the screen, even with little content."
- **My mistakes:**
  1. No container height, so there was no free space for `1fr` (same as C2 #1).
  2. `grid-template-rows: 1fr 1fr auto`: the header got as much free space as the content row, which is about half the screen when the window is tall.
  3. `height: 100vh`: long content overflows the container, and the footer stays in the middle of the text.
- **Mental model:**
  - `min-height: 100vh` means "at least the screen, more if the content needs it." It creates free space without capping the height.
  - Only **one** row gets `1fr` (the one that should absorb the free space). Rows that should be as tall as their content get `auto`.
  - Reset `body { margin: 0 }`, or `100vh` + 16px of default margin gives a scrollbar on an empty page.
- **Drill:** test with 1 paragraph (footer at the bottom, no scrollbar) and with 20 paragraphs (footer after the content, page scrolls).

### 3. `minmax()` only works in track sizing

- **Cue:** I want "at least X, but grow."
- **My mistake:** `height: minmax(100vh, auto)`. That value is invalid, so the browser dropped the whole declaration. The bug was silent: DevTools showed it struck through with a ⚠.
- **Mental model:** `minmax()` is valid only in `grid-template-rows/columns` and `grid-auto-rows/columns`. On a regular box, a floor is `min-height` / `min-width`, and a ceiling is `max-*`.
- **Drill:** when a property "doesn't work," look in the Styles panel first. A struck-through line with a warning icon means invalid syntax, not a cascade problem.

### 4. Edge cases to remember

- **Long URL in a `1fr` column:** same `minmax(auto, 1fr)` trap as C1 #5. Use `minmax(0, 1fr)` or `overflow-wrap: anywhere`.
- **Narrow screen:** three columns at 400px make the sidebar and aside unreadable. Fixed in C4 with a media query and a different areas string.
- **`100vh` on mobile:** the address bar changes the viewport height. Use `100dvh`.

### Self-check before the next challenge

- [ ] Why shouldn't width proportions come from repeating a name in the areas string?
- [ ] Which three things make a sticky footer, and why `min-height` instead of `height`?
- [ ] Where is `minmax()` valid, and how do I spot an invalid declaration in DevTools?
