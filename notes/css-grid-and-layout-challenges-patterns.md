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
