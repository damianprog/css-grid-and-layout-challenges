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

---

## C4 — Responsive dashboard

**Gap labels during the challenge:** none. It met the requirements on the first attempt, with no hints. The notes below come from the code review, which was based on the code only, without running it in a browser.

### 1. Mobile-first: base styles are the narrow layout

- **Cue:** "1 column → 2 columns → 4 columns."
- **What worked:** the base rule is only `display: grid` and `gap`, which already gives 1 column. Each wider view is added with `min-width`, and the HTML order doesn't change.
- **Mental model:** a media query adds only what differs from the narrower view, and `min-width` rules build on each other. That's why the 1000px query must also reset what the 600px one set for `.tile-featured`.

### 2. A row-locked item lets auto-placement find its column

- **Cue:** "this tile must span these rows, but its column follows from the others."
- **What worked:** from 1000px `.tile-activity` has only `grid-row: 1/3` and still lands in column 4, and the remaining tiles fill row 1 by themselves.
- **Mental model:** this extends C2 #2. Fully placed items go first (`.tile-featured` takes columns 1–3 of row 2), then items locked to a row, then everything else. Activity needs both rows free, so column 4 is the first one that fits.
- **Drill:** remove `grid-row` from `.tile-featured` and predict where each tile lands before looking.

### 3. Don't write what the grid already does

- **`grid-template-rows: repeat(2, auto)`:** can be removed. Implicit rows are already `auto`, and `grid-row` creates them.
- **`grid-column: 1/3`:** tied to the number of columns. `1 / -1` always means the full width of the explicit grid.
- **Status:** not applied yet.

### 4. Edge cases to remember

- **Focus order:** from 1000px, Tab goes to "Full report" in the bottom row first, then up to the three "Details" links, and "All activity" last. Focus and screen readers follow the HTML, not the visual order. It's minor here, but a real accessibility problem with bigger rearrangements.
- **Position-dependent layout:** it works only with exactly 5 tiles. A sixth one without a position lands in the next row and takes one column.

### 5. Open exercise: `grid-template-areas`

The challenge plan mentions "area order," and I used line numbers, which also meets the requirements. Rewrite it with areas: can most of the changes between breakpoints move into a single rule?

### Self-check before the next challenge

- [ ] Why does mobile-first need no rule for the 1-column layout?
- [ ] In what order does the grid place fully placed, row-locked and remaining items?
- [ ] Why can the visual order and the Tab order differ, and what does that cost?

---

## C5 — Hero banner

**Gap labels during the challenge:** missing tool (gradient) → missing tool (image fill) → missing composition (card track, needed a step-by-step walkthrough) → detail (padding past `overflow: hidden`, `transition`) → missing composition (text reflowing during the animation) → detail (`align-self`) → missing tool (image inflating the hero).

### 1. Layers are items in the same grid area

- **Cue:** "text over an image," and the usual reflex is `position: absolute`.
- **What worked:** image, gradient, text and card all sit on the same grid lines. Later HTML paints on top, so the order of the layers comes from the markup.
- **Mental model:** grid items are allowed to share cells. Unlike absolute positioning, every layer stays in the layout, so the content can still give the container its height.
- **Gradient:** it's a generated image, so it goes in `background-image: linear-gradient(direction, start, end)`. Use `rgb(0 0 0 / 0.7)` and `transparent` so the photo shows through, with the dark end under the text.

### 2. An image doesn't stretch to its grid area

- **Cue:** a gray strip under the image, while the gradient `div` fills the whole area.
- **My mistake:** I expected the image to fill the area like the `div` does. It kept its 16:9 height, so it was shorter than `70vh`.
- **Mental model:** `stretch` is the default for a `div`, but an element with its own aspect ratio keeps its size. It needs an explicit `width` and `height`, and `object-fit: cover` crops the excess instead of distorting the image.
- **Drill:** in DevTools, switch `object-fit` between `fill`, `contain` and `cover`, and describe what each one does.

### 3. Image inside `min-height`: `height: 0; min-height: 100%`

- **Cue:** the hero is the right height in a normal window but much taller than `70vh` in a wide one.
- **My mistake:** after changing `height` to `min-height` on the hero, `height: 100%` on the image stopped limiting it. The rows grew to the image's proportional height (about 1080px at 1920px wide).
- **Mental model:** with `min-height` the rows are sized from content, and a percentage height has nothing to resolve against yet, so the image contributes its natural height. With `height: 0; min-height: 100%` it contributes nothing to the rows, then fills the area once the area has a size.
- **Trade-off:** `height` caps the hero and clips content in a short window. `min-height` lets it grow, so the image must not be the thing that grows it.
- **Drill:** test at a very wide window (hero is exactly `70vh`) and at about 250px tall (hero grows with the text and nothing is clipped).

### 4. Reveal by animating a track, not by toggling the element

- **Cue:** "a panel slides in from the edge," without `transform` or `position`.
- **My mistakes:**
  1. `display: none` on the card. It removes the element from the layout, so there is nothing to animate.
  2. `grid-column: 3/-1` with 2 columns. Line `-1` was also line 3, so the browser dropped the end line and created an implicit third column outside the image.
  3. I pictured the hero moving aside to uncover the card, because I thought of the hero's size rather than how its tracks divide it.
- **Mental model:** the container stays the same size and only the division changes: `1fr 0` → `1fr 20rem`. Layers spanning all tracks don't move, and only the line between the tracks does. `transition` goes on the base rule, not on `:hover`, so it also animates on the way out. Both states need the same number of tracks and compatible units: `0` → `20rem` or `0fr` → `1fr`, but not `0` → `1fr`.
- **Drill:** build it in stages: static card in its track, then hidden, then a jump on hover, and only then `transition`.

### 5. Whatever depends on a changing track reflows with it

- **Cue:** text squeezing and rewrapping during an animation.
- **My mistakes:**
  1. `.hero-text` sat in column 1, which narrows when the card comes in.
  2. The card took its width from its track, so its text rewrapped at every frame.
  3. `overflow: hidden` on the card still left its padding visible at track width 0.
- **Mental model:**
  - Anything that should stay still spans **all** tracks, so its area never changes. That's why the text uses `1/3`, like the image.
  - A sliding element has a fixed width equal to the open track (`width: 20rem`). It overflows the empty track, and `overflow: hidden` on the **container** clips it, so it emerges from behind the edge instead of stretching.
  - With `border-box` an element can't get narrower than its padding, and `overflow: hidden` clips at the padding box. Clip on the parent when the padding must go too.

### 6. `align-self` needs room to move

- **Cue:** "I added `align-self: end` and nothing changed."
- **My mistake:** `.hero-text` also had `height: 100%`, so the box already filled its area and its bottom edge was already at the bottom.
- **Mental model:** alignment moves the **item's box** inside its area, not the content inside the box. `justify-self` is horizontal and `align-self` is vertical. If the DevTools highlight fills the whole area, alignment can't do anything.

### 7. Small defaults worth remembering

- **`width: 100vw`:** not needed on a block-level grid container, which already fills its parent. `100vw` includes the scrollbar, so it causes horizontal scroll once the page is taller than the window.
- **`a { color: inherit; }`:** links don't inherit color by default because the browser styles them. One rule makes every link follow its context.

### 8. Edge cases to remember

- **Rows:** `repeat(2, 1fr)` isn't needed. One row plus `align-self: end` gives the same result, and taller text no longer enlarges an unused row above it. Not applied yet.
- **Below 320px:** the `20rem` card gets clipped. At 360px it fits but covers most of the hero, which I accepted because it goes away on mouse-out.
- **Touch and keyboard:** there's no real hover on touch, and the link in the hidden card can still receive keyboard focus while invisible. `:focus-within` is the lead to follow.

### Self-check before the next challenge

- [ ] Why does a `div` fill its grid area while an `img` doesn't, and what do I add to the `img`?
- [ ] Why does `display: none` rule out a slide, and what should change instead?
- [ ] What should I check when a layer moves or rewraps during a track animation?
- [ ] Why does `align-self` do nothing on an item with `height: 100%`?

---

## C6 — Gallery without media queries

**Gap labels during the challenge:** detail (measurements right, no "why") → missing tool (reading collapsed tracks in DevTools) → missing composition (why the variants stop differing, needed a fill-in-the-blank) → detail (what counts as an empty column) → missing composition (the "columns can grow" condition, needed the answer).

### 1. `auto-fit` and `auto-fill` create the same tracks

- **Cue:** "the number of columns adjusts by itself," using `repeat(auto-fit | auto-fill, minmax(180px, 1fr))`.
- **My mistake:** in `fit, 2 items` at 1200px I counted 2 columns, because that's what the page showed. I also had cause and effect reversed: I thought the empty columns got 0px because the tiles took the whole width.
- **Mental model:** both keywords create as many tracks as fit at the minimum size (1200 / 180 → 6). The only difference is what happens to **empty** tracks. `auto-fill` keeps them, and `auto-fit` collapses them to 0. Only after that is `1fr` resolved: in `fit` the 2 remaining tracks share 1200px (600px each), and in `fill` all 6 share it (200px each).
- **One sentence:** `auto-fit` collapses empty columns and `auto-fill` keeps them, which shows only when there are empty columns and the columns can grow (e.g. `1fr`).

### 2. The difference needs empty tracks AND a flexible max

- **Cue:** "auto-fit and auto-fill look the same, so is there a difference at all?"
- **Mental model:** if either condition is missing, the two variants look identical.
  - **No empty tracks:** `fit` has nothing to collapse. With 2 items that happens below `3 × 180 = 540px`, and with 10 items at 1200px there are 6 tracks and all of them are used.
  - **Fixed max** (`minmax(180px, 180px)`): `fit` still collapses the empty tracks (DevTools shows it), but no tile can grow into the freed space, so both pages have the same empty strip on the right.
- **Thresholds:** the variants differ once `fill` fits more tracks than there are items: N items differ from `(N + 1) × min` up. For 10 items that's 1980px without `gap`. With `gap` add `N × gap`.

### 3. An empty column is empty in every row

- **Cue:** 10 tiles in 6 columns leave 2 empty cells in the last row, so why doesn't `fit` collapse them?
- **Mental model:** collapsing works on whole tracks. A column is empty only when **no row** has an item in it. Gaps at the end of the last row don't count.

### 4. Reading the real tracks in DevTools

- **Cue:** the overlay shows fewer columns than I expect, or line numbers piled on one edge.
- **Mental model:** collapsed tracks have 0 width, so their lines sit in the same place and the numbers overlap (at the right edge I saw `-1`, `7`, `-1`). The last line number tells you the real number of tracks: line 7 means 6 columns.
- **Drill:** select the grid, open **Computed** → `grid-template-columns`. `600px 600px 0px 0px 0px 0px` vs `200px × 6` settles any doubt.

### 5. Choosing between them

- **`auto-fit`:** a gallery with 2 or 20 photos, where a few items should stretch across the whole row.
- **`auto-fill`:** tiles should keep a consistent size whatever the count, e.g. a product grid where one product shouldn't become 1200px wide, or a list that grows dynamically without resizing existing items.

### 6. Edge cases to remember

- **No `gap`:** tiles of the same color merge into one strip, so tile boundaries aren't visible. Adding `gap` shifts every threshold (3 columns need `3 × 180 + 2 × gap`). Not applied yet.
- **Container narrower than the minimum:** below 180px the tile can't shrink and the page scrolls horizontally. `minmax(min(180px, 100%), 1fr)` fixes it. Not tested yet: compare both at 160px.

### Self-check before the next challenge

- [ ] How many tracks do `auto-fit` and `auto-fill` create at 1200px with `minmax(180px, 1fr)`, and what differs between them?
- [ ] Which two conditions must hold for the difference to be visible?
- [ ] From what width do 10 items in `minmax(180px, 1fr)` differ between the variants, without and with `gap: 16px`?
- [ ] Where in DevTools do I check whether tracks are collapsed?

---

## C7 — Pricing table

**Gap labels during the challenge:** missing tool (where to start, needed steps) → detail (implicit rows stretching) → detail (`grid-row` vs `grid-template-rows`) → missing tool (`lh`) → subgrid on the first attempt from three guiding questions.

### 1. Implicit rows share the free space equally

- **Cue:** "buttons at the bottom of every card," with the card as a grid and no `grid-template-rows`.
- **My mistake:** `align-self: end` on the button. In the shorter card the free space was spread over all 4 implicit rows, so the button sat at the bottom of its own stretched row, not at the bottom of the card.
- **Mental model:** with `align-content: normal` the implicit `auto` rows of a stretched grid all grow. Decide which **one** row absorbs the free space: `auto auto 1fr auto`. Then `align-self` isn't needed.
- **Drill:** turn on the overlay on the shortest card and check that only the list row is taller than its content.

### 2. Placement on the item, sizing on the container

- **Cue:** "this row should take the rest."
- **My mistake:** I reached for `grid-row` on `.plan-features`.
- **Mental model:** `grid-row` / `grid-column` on an item say **where** it goes. Track **sizes** are decided only by the container: `grid-template-rows` / `grid-template-columns`.

### 3. Without subgrid each card guesses: `min-height: 2lh`

- **Cue:** headings of different lengths push the prices out of line across cards.
- **Mental model:** every card is a separate grid, so its rows don't know about the other cards. The best you can do is reserve the worst case. `lh` = one line of the element's own text (`font-size × line-height`), so it goes on `.plan-name`, not on `.plan`, where it would use the smaller card font.
- **Limit:** once the window narrows and the Pro name wraps to 3 lines, the prices drift apart again. Any fixed value is a guess.

### 4. Subgrid: the parent owns the rows, the cards borrow them

- **Cue:** "the same parts must line up across sibling components," whatever the content length.
- **What worked:**
  1. The parent `.pricing` defines the rows: `grid-template-rows: auto auto 1fr auto`.
  2. Each card spans them all: `grid-row: span 4`.
  3. The card stays a grid but uses the parent's tracks: `grid-template-rows: subgrid`.
- **Mental model:** a shared row is as tall as the tallest piece in it across **all** cards. That's why one long name moves every price down together, and why `min-height: 2lh` became unnecessary (removed).
- **Gap:** the subgrid inherits the parent's `gap` on the shared axis. That's where the space inside the card came from. Override it with `row-gap` on the card.

### 5. Highlighting one card without breaking the rows

- **Safe:** `background`, `color`, `box-shadow`, `outline`, `scale` / `transform`. They don't change the box size the grid sees. Applied: `background` + `scale: 1.05`.
- **Affects everyone:** `padding`, `border`, `font-size` on the featured card make the shared rows taller, so all cards grow with it. It stays aligned, but it's not "just Pro."

### 6. Edge cases to remember

- **Narrow screen:** 3 columns stay at 320px. With a 1-column breakpoint only the first card uses the explicit rows. The next ones land in implicit `auto` rows, so their list row is no longer `1fr`. That's fine when the cards are stacked. Not applied yet.
- **`1fr` with no container height:** the list row equals the longest list (C2 #1). It only matters if `.pricing` gets a fixed height.
- **`repeat(3, 1fr)`:** same `minmax(auto, 1fr)` trap as C1 #5 if a long word lands in a plan name.

### Self-check before the next challenge

- [ ] Why does `align-self: end` alone not put the button at the bottom of a grid card?
- [ ] Which three declarations make subgrid rows, and on which elements do they go?
- [ ] Why does `2lh` go on the heading and not on the card?
- [ ] Which ways of highlighting a card keep the shared rows unchanged?
