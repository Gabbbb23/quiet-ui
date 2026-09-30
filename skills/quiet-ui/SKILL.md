---
name: quiet-ui
description: "The default visual language for any interface work: one flat surface instead of bordered cards, no shadows anywhere, one text size, hue-free greys, filters that act as you type, and the exact light and dark palettes. Consult before designing, building, restyling or reviewing any screen, component, layout, theme or design system — and before reaching for a card, a shadow, a large heading, or a Search button."
---

# Quiet UI

Most agent-built interfaces look the same, and they look wrong in the same way: every region is a bordered
card with a rounded corner and a drop shadow, sitting on a tinted page; headings are two or three times the
body size over an uppercase eyebrow; every table has a frame and a rule under every row; every state is a
filled pill; a bar across the top names the product to someone already signed in. It is not ugly, exactly.
It is *loud*, and none of the loudness carries information.

This skill is the alternative, and it is the default. Do not produce the pattern above and wait to be
corrected.

`no-ui-slop` covers the words on a screen. This covers everything else. Both apply.

## The question

Before drawing any mark, answer one question: **what does this tell the reader that they could not already
see?**

- A border says *these things are separate from those*.
- A shadow says *this floats above the page*.
- A larger size says *this outranks that*.
- A colour says *this is in a state*, or *this is that member of a fixed set*.

If the answer is nothing, the mark does not go in. That is the whole skill; the rest is that question
already answered for the cases that come up.

## The principles

**P1 — Chrome is a cost.** Nothing is drawn unless it carries information. A bar naming the product, a
caption restating a number, a subtitle explaining the page: each is paid for in screen and attention and
none of it is repaid.

**P2 — Containment is a claim.** A box around things claims they are one object, separate from what is
outside it. On a working screen that claim is almost always false — a queue, a table and a form on one page
are one piece of work. Separate with space, and with one hairline where two things would otherwise merge.
Reach for a real card only when the thing genuinely is a separate object.

**P3 — Depth is lightness, never a shadow.** Use no drop shadows, on anything, at rest or floating. What
sits on top says so by being a step lighter (dark theme) or by a hairline and a step of white (light
theme). Shadows smear that step, and once everything has one, none of them mean anything.

**P4 — Hierarchy is weight and space, not size.** One text size carries the whole interface. What ranks
higher is medium weight; what is context is muted; what is separate gets a gap. A heading three times the
body size tells a reader who deliberately navigated there nothing they did not know.

**P5 — Colour is spent, not scattered.** Colour does two jobs and no others. It signals state — late is
red *text*, and the accent fill belongs to the one action that moves the work forward and to the active
navigation item. And it tells apart the members of a fixed set: a person's avatar carries a hue of its own,
picked from their id so they are the same colour everywhere without anyone choosing or storing it; a fixed
status, stage or kind of history entry is drawn with its own icon in a hue from the identity palette
(`--hue-*` in `tokens.css`), so a column of them can be scanned. The hues keep one meaning each, so a reader
learns them once: gray has not started or has gone quiet, blue is moving, violet and amber wait on someone
else, orange asks for a change, green is agreed or done, red has stopped. The word always stands beside the
colour and carries the meaning. Free text, names, amounts and dates stay uncoloured, and the greys carry no
hue at all.

**P6 — A control acts; it does not wait to be applied.** Filtering happens as the reader types or chooses.
A Search button, an Apply button, a Refresh button is a control that has not been finished.

**P7 — Equal proportions.** Space that should read as the same size must be the same number. The gap above
a heading and the gap below it to its rule; the gutter on the left and the gutter on the right; the padding
at the top of a panel and at its foot; the space between four panels in a grid. An interface is mostly
rectangles of nothing, and an eye reads an unequal pair as a mistake long before it can say which side is
wrong. Set spacing from one scale, and check the pairs rather than each value on its own.

**P8 — Density is respect.** A 32px gutter inside an already-inset panel, a max-width cap that empties a
third of a wide monitor, a row padded to twice the height of its text: each costs rows the reader could
have seen without scrolling.

**P9 — The label carries the meaning.** `no-ui-slop` in full.

**P10 — A press is answered at once.** Moving somewhere is instant: the page pressed appears in the same
frame, as its skeleton, and fills in when the server has it. Saving something shows it is working: the button
that creates, changes or deletes spins until it is saved. A screen that sits unchanged after a press reads as
a press that did not land.

## The rules that follow

**Type.** Four sizes and no more: 14px for the page name, section names, row titles, body and controls;
12px for metadata, counts, timestamps and second lines; 16px for a record's own name on its record page and
for a dialog title; 14px tabular for numbers compared down a column. No 24px or 30px headings. No uppercase
tracked-out eyebrow.

**Surface.** The page is one surface. A named region is a heading line with a hairline under it and the
content below in the page's own space — no border, no background, no shadow. The page heading is one line:
the section, a separator, the page name, at body size, with a rule that reaches both edges of the panel.

**Rules and borders.** A rule goes where two things would otherwise run together, and nowhere else. A table
has no frame, no rule under the column names and no rules between rows — a hover tint locates the row,
rounded at its ends. A border that exists only to make something look like a control appears on focus and
not before. Hairlines are `border-border` at half strength or so; they separate without being looked at.

**Colour.** The greys in `tokens.css` are hue-free by design. Keep them. Replace only the accent and the
alarm with the product's own. The identity hues (`--hue-*`) come with it, each with a light and a dark value;
draw one as its text or icon colour on a wash of itself (`color-mix(in oklch, var(--hue-x) 15%, transparent)`),
never as a solid fill. Pick a person's hue from a hash of their id over the eight hues, not at random on each
render.

**Focus.** One ring, in the ring colour. Not a border *and* a glow together — that reads as a hard dark
edge inside a soft one.

**X buttons.** Every X is 32 × 32 to press, with a 16px glyph — it is the smallest mark on a screen and
the one people reach for in a hurry. Standing alone (closing a dialog or sheet, removing a row) it gets the
32px box. Drawn as a badge on something smaller (a chip, a thumbnail's corner), where a 32px box would cover
what it removes, it keeps its drawn size and gets an invisible margin: `relative after:absolute
after:-inset-N`, with `N` making the total 32px. Enlarge by moving the box, never the glyph.

**Answering a press.** Two kinds of press. Going somewhere is instant and shows no spinner: the page
pressed replaces the old one in the frame of the press as a skeleton that repeats its real measurements, so
nothing moves when it fills in, and the navigation highlight moves with it. Do not rely on the framework's
prefetch for this — draw the skeleton from the press itself, chosen from the address (a record's or a
list's), and give every route the same skeleton as its loading state so the hand-over does not show. Content
already on the page is switched to locally, not fetched again. Saving something — create, change, delete —
shows a spinner in the button that did it, which cannot be pressed twice until the server has answered.
Nothing else spins.

**Scrolling.** A box that scrolls fades the edge that has more beyond it, so a row the box cuts reads as a
list continuing rather than text sliced in half. At the end of the list, nothing to hint at, so the last
row is drawn whole. The panel that holds the page scrolls inside itself, so the scrollbar stays within its
own edges rather than running down the window beside it.

**Motion.** None that decorates. Transitions belong to things that open and close.

**Reach.** A list someone works through takes the keyboard — `j`/`k` or the arrows, Enter to open, Escape
to close, with only one list on a page bound to the keys. A row opens a side panel rather than navigating
away; a full page is for a record someone will sit in. Undo after the fact, not a confirmation dialog
before it.

## The files

- **`tokens.css`** — the light and dark palettes verbatim, plus the scrollbar treatment. Copy both blocks
  into the project's global stylesheet. Read it before choosing any colour.
- **`patterns.md`** — worked recipes: the shell, the page header, a register's toolbar (search, paging,
  filters), the table, list rows, the scrolling fade, and the theme switcher. Read it before building any
  of those; they are the parts that get rebuilt badly most often.

## What you are replacing

Recognise the old pattern so you do not reproduce it. If a screen you are changing already looks like the
left column, convert it as you pass through — do not add to it.

| Old | New |
| --- | --- |
| Every region a bordered card with a shadow | One surface; a heading on a hairline |
| `shadow-sm` at rest, `shadow-lg` on overlays | No shadow anywhere; depth is a step of lightness |
| 30px heading over an uppercase eyebrow | One line at body size: section / page name |
| Table in a frame, a rule under every row | No frame, no rules; a rounded hover tint |
| A filled pill for every state in every row | A status's own icon and hue on a wash, with its word |
| Grey initials for everyone | Each person's initials in their own hue, from their id |
| Search box plus a Search button | Results follow the typing, after a short pause |
| A row of numbered page buttons | "1–20 of 89" and a step either way |
| Six filter controls always on screen | One button with a dot when a filter is set |
| Blue-tinted greys in both themes | Hue-free greys in both themes |
| A top bar naming the product | No top bar; the sidebar carries everything |
| 32px gutter, content capped mid-screen | One even 16px gutter, full width |
| A 16px X you have to aim at | 32px to press, the glyph where it was |
| A click that leaves the page standing still | The page pressed at once, as a skeleton |

## Before you call it done

1. Is anything drawn that carries no information? Delete it.
2. Any shadow at all? Delete it, and step the surface's lightness instead.
3. Any text above 16px? Any uppercase eyebrow? Bring it back to the scale.
4. Any card that is really a heading over a list? Make it a plain section.
5. Any rule that separates nothing? Any filled pill repeated down a column? Delete it.
6. Any control that waits to be applied? Make it act.
7. Any grey with hue in it? Take the hue out.
8. Any pair of spaces that should match and does not — above a heading and below it, the left gutter and
   the right, the top of a panel and its foot? Make them the same number.
9. Read every line of text and name the fact it carries. If you cannot, delete the line.
10. Any X button smaller than 32px to press? Give it the box, or the invisible margin.
11. Press everything. Does a link show its page's skeleton at once, with no spinner? Does every button
    that saves spin until it is saved?