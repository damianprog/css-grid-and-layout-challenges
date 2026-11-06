# CSS Grid — challenge plan

Nov 2, 2026 · @Damian

## About the course and rules

10 challenges for the course [Ultimate CSS Grid & Layout Techniques, v3](https://master.dev/courses/css-grid/) (Jen Kramer, \~6h20m). Each one comes after a block of lessons and is deliberately different from the course exercises.

- Plain CSS, no Tailwind and no shared theme — each challenge is a separate CodePen or file.
- After finishing a block, you say in the project which lesson you finished; you get the challenge laid out (with starter HTML if needed).
- You solve it yourself, in puzzle mode: questions and hints instead of ready-made CSS.

## Challenges

| #   | After lessons                                           | Challenge                              | Requirements and constraints                                                                                                                                                                       | Status      |
| --- | ------------------------------------------------------- | -------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ----------- |
| C1  | Grid Overview → Basic Grid Exercise                     | User profile                           | Avatar on the left, name and bio on the right, vertically centered; below, 3 stats in equal columns. Only grid and gap, no flexbox and no margins for positioning. Check it with the DevTools grid overlay. | Done        |
| C2  | Grid Layout Exercise → Grid Span & Repeat Exercise      | Bento grid                             | 4×3 grid, 6 tiles: one 2×2, two 2×1, the rest 1×1. First with line numbers, then refactor to span and repeat().                                                                                   | Done        |
| C3  | Grid Template Area (+ exercise)                         | Page layout                            | Header, sidebar, main, aside, footer using only grid-template-areas. Move the sidebar to the right by changing only the areas string.                                                     | Done        |
| C4  | The whole Responsive Design & Media Queries section     | Responsive dashboard, mobile-first     | 1 column → from 600px 2 columns with a featured full-width tile → from 1000px 4 columns and a different area order. HTML order unchanged.                                                        | Done        |
| C5  | Overlapping Rows & Columns → CSS Animated Grid Exercise | Hero banner                            | Background, gradient and text layered with grid, no position: absolute. A card with a description slides over the image on :hover.                                                       | Done        |
| C6  | Grid Margin → minmax, auto-fit & auto-fill              | Gallery without media queries          | Tiles min. 180px, the number of columns adjusts by itself. auto-fit and auto-fill versions, test with 2 and 10 items; explain the difference in one sentence.                                     | Not started |
| C7  | Subgrid → Subgrid in Action                             | Pricing table with 3 plans             | Different content lengths; headings, prices, lists and buttons aligned in one line. First without subgrid, then with subgrid.                                                                    | Not started |
| C8  | Moons of Saturn (whole section)                         | Article list page — the grid exam      | 3 breakpoints, featured post, subgrid in cards, overlap in the hero. You plan the breakpoints yourself, no step-by-step instructions.                                                            | Not started |
| C9  | Flexbox + Responsive Images sections                    | Navbar + product section               | Navbar with flex: logo in the middle on desktop (order), wrapping on mobile. Image via picture (different crop on mobile) plus srcset/sizes. Justify: where flex, where grid.                     | Not started |
| C10 | Container Queries section (final)                       | Card in 3 contexts                     | The same card in a sidebar, a 2-column grid and full width; layout via @container, without changing classes. Theme switcher via a style query.                                            | Not started |
