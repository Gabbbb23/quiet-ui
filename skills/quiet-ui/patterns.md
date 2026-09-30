# Worked patterns

Recipes for the parts that get rebuilt badly most often. The code is React with Tailwind v4 and shadcn/ui,
because that is what it was built in; in another stack keep the behaviour and the reasoning and translate
the classes. Every "why" line is the part that matters — the class names are incidental.

---

## 1. The shell

No top bar. A bar that holds a sidebar toggle and the product's name is a whole row of every screen spent
on nothing: the toggle belongs on the thing it toggles, and the reader knows which product they signed
into.

The sidebar holds the brand, the search, the navigation, and a footer with the person, the theme and the
sign-out. It collapses to an **icon rail**, not off screen, so the menu stays usable when it is narrowed;
every item needs a tooltip for that state.

The content panel is the window's height and **scrolls inside itself**, so its scrollbar stays within its
own edges instead of running down the window beside it. One even 16px gutter, no width cap, a hairline and
a small corner radius.

```tsx
<SidebarProvider>
  <AppSidebar />
  <SidebarInset className="border-border/50 h-svh overflow-hidden rounded-[10px]! md:h-[calc(100svh-1rem)] md:border">
    {/* The sidebar is a sheet on a phone, so the toggle keeps a strip of its own there — and only there. */}
    <div className="bg-background sticky top-0 z-20 flex h-12 shrink-0 items-center border-b px-3 md:hidden">
      <SidebarTrigger />
    </div>
    <div className="flex min-h-0 flex-1 flex-col overflow-y-auto">
      <div className="w-full p-4 has-[[data-bleed]]:p-0 has-[[data-fill]]:h-full has-[[data-fill]]:min-h-0">
        {children}
      </div>
    </div>
  </SidebarInset>
</SidebarProvider>
```

Two markers a page can carry on its root:

- `data-bleed` — take the panel whole and draw your own edges (a calendar, a mail client). Such a page
  sizes itself to `calc(100svh - <the panel's own margin>)`, not to a header that no longer exists.
- `data-fill` — take the panel's height, so the page can hold its heading still and scroll only the list
  under it.

---

## 2. The page header

One line, at body size, with a rule that reaches both edges of the panel — it pulls back through the
content gutter (`-mx-4 px-4`) so it meets them instead of stopping short.

```tsx
<div className="border-border/40 -mx-4 mb-4 flex shrink-0 flex-col justify-between gap-3 border-b px-4 pb-3 sm:flex-row sm:items-center">
  <div className="min-w-0">
    <div className="flex min-w-0 items-center gap-2 text-sm">
      <span className="text-muted-foreground shrink-0">{section}</span>
      <span className="text-muted-foreground/40" aria-hidden="true">/</span>
      <h1 className="truncate font-medium">{title}</h1>
      {status ? <Badge variant="secondary">{status}</Badge> : null}
    </div>
    {meta ? <p className="text-muted-foreground mt-1 truncate text-xs">{meta}</p> : null}
  </div>
  {children /* the one primary action, if there is one */}
</div>
```

The helper takes **no description parameter**. Give it one and whoever calls it next will pass a sentence.

---

## 3. A named region

Not a card. A heading line, a hairline, and the content in the page's own space. `count` beside the title
is the name being precise, not a metric. Leave the title out when the page heading already names the
region — two "Clients" on one screen is one too many.

```tsx
<section className={cn("flex min-w-0 flex-col", fill && "min-h-0")}>
  {title === undefined && !action ? null : (
    <div className="border-border/70 flex h-9 shrink-0 items-center justify-between gap-4 border-b">
      <h2 className="flex items-baseline gap-2 truncate text-sm font-medium">
        {title}
        {count === undefined ? null : <span className="text-muted-foreground tabular-nums">{count}</span>}
      </h2>
      {action}
    </div>
  )}
  {fill ? <ScrollFade className="min-h-0 flex-1 space-y-4 pt-2">{children}</ScrollFade>
        : <div className="space-y-4 pt-2">{children}</div>}
</section>
```

---

## 4. A register's toolbar

**One row**: the search on the left; the place in the register and one filter button on the right. This is
the pattern that replaces a search box with a Search button, a row of always-visible filter chips, and a
run of numbered page buttons underneath the table.

```tsx
<div className="flex flex-wrap items-center gap-2">
  <RegisterSearch className="w-full sm:w-72" label="Search clients" placeholder="Name, client ID, phone, email" />
  <div className="ml-auto flex items-center gap-1">
    <RegisterPager from={place.from} to={place.to} total={place.total} previousHref={…} nextHref={…} />
    <RegisterFilters active={activeCount} resets={["tag", "source"]}>
      <RegisterFilterGroup label="Tag">{chips}</RegisterFilterGroup>
      <RegisterFilterGroup label="Source">{select}</RegisterFilterGroup>
    </RegisterFilters>
  </div>
</div>
```

### The search

A magnifier and the words, with **no box until the reader is in it**. Results follow the typing after a
short pause. The search goes in the address, so the page is shareable and Back works.

```tsx
<div className="focus-within:ring-ring/70 flex h-9 items-center gap-2 rounded-md px-2 outline-none transition-[box-shadow] focus-within:ring-2">
  <Search aria-hidden="true" className="text-muted-foreground size-4 shrink-0" />
  <input aria-label={label} className="placeholder:text-muted-foreground w-full min-w-0 bg-transparent text-sm outline-none"
         onChange={(e) => setValue(e.target.value)} placeholder={placeholder} type="search" value={value} />
</div>
```

Three things to get right:

- **Debounce ~300ms**, then write the query into the URL with a *replace*, not a push, so Back leaves the
  page instead of walking every keystroke.
- **Hold what the address already carried** in a ref and compare before navigating, or mounting navigates
  to the search it just arrived on.
- **Drop the page number** whenever a filter changes. A search that changes what matched must not land the
  reader on page 7 of a 2-page result.

One ring on focus, no border — a 1px border plus a 3px ring reads as a hard dark edge inside a soft one.

### The place in the register

"1–20 of 89" and a step either way, on the search's own line. No run of numbered buttons: nobody aims at
page 47, they search for the record. Count in one fixed locale so the server and the browser print the same
string.

If the pager is a client component and the screen is a server one, pass `previousHref` / `nextHref`
**already built** — a server component cannot hand a function across the boundary.

### The filters

All of them behind one button, with a **dot when any is set**. Put the dot outside the icon's corner
(`-top-0.5 -right-0.5`) with a ring in the background colour, or it sits on top of the glyph.

```tsx
<Button aria-label="Filters" className="relative size-7" size="icon" variant="ghost">
  <SlidersHorizontal className="size-4" />
  {active > 0 ? <span aria-hidden className="bg-primary ring-background absolute -top-0.5 -right-0.5 size-1.5 rounded-full ring-2" /> : null}
</Button>
```

Inside: a label over each filter's controls, a short fixed list as chips, a longer one as a select, a
divider, and Reset — which clears only the parameters it is given, so a search that is typed survives it,
and is disabled while nothing is set. Choosing applies at once; there is no Apply.

---

## 5. The table

No frame, no rule under the column names, no rules between rows. Column names are muted and normal weight.
The hover tint locates the row and rounds at its ends.

The tint must sit on the **cells**, not the row: a `<tr>` in a `border-collapse: collapse` table cannot
round its own background, so a radius on the row is silently ignored. Scope it to the body — a header row
that ends in a spacer cell will otherwise light up under the pointer.

```tsx
<tbody className="[&_tr:hover>td]:bg-muted/50 [&_tr>td:first-child]:rounded-l-md [&_tr>td:last-child]:rounded-r-md">
```

Resizable columns: every handle the same thinness and the same height, visible on header hover, and
changing colour for nothing — not on hover, not while dragging. The cursor is the feedback. Keep a
`focus-visible` colour for keyboard resizing, which a pointer drag does not trigger.

---

## 6. A list row

One line: an optional state icon, the record's name in medium weight, what is happening to it muted and
beside it, the timing right-aligned in 12px. Truncate; do not wrap. Rows divide with a faint rule, tint on
hover, and carry no border of their own.

```tsx
<div className="hover:bg-muted/60 -mx-2 flex items-center justify-between gap-3 rounded-md px-2 py-2 text-sm">
  <div className="flex min-w-0 flex-1 items-center gap-2">
    {urgent ? <Flame aria-label="Urgent" className="text-destructive size-3.5 shrink-0" /> : null}
    <span className="shrink-0 truncate font-medium">{name}</span>
    <span className="text-muted-foreground truncate">{what}</span>
  </div>
  <div className="text-muted-foreground flex shrink-0 items-center gap-3 text-xs">
    {waitingOn ? <span className="truncate">With {waitingOn}</span> : null}
    {due ? <span className={cn("tabular-nums", overdue && "text-destructive")}>{due}</span> : null}
  </div>
</div>
```

A date on an overdue row says *how* late ("6 days late"), not when it was due: a column of overdue dates
makes the reader subtract before they can tell a day from a fortnight.

---

## 7. The scrolling fade

A row sliced in half by the bottom of a scroll box reads as broken text. Fade the edge that has something
past it, and only that edge — at the end of the list there is nothing to hint at, so the last row is drawn
whole.

**Use an overlay, not `mask-image` on the scrolling element.** A mask covers that element's own scrollbar
and fades its arrows away along with the rows. Position two gradients over the box instead, each stopping
short of the scrollbar by the room it takes:

```tsx
<div className={cn("relative", className)}>
  <div ref={box} className={cn("h-full overflow-y-auto", contentClassName)}>{children}</div>
  {before ? <div aria-hidden className="from-background pointer-events-none absolute top-0 left-0 bg-gradient-to-b to-transparent"
                 style={{ height: "1.5rem", right: gutter }} /> : null}
  {after ? <div aria-hidden className="from-background pointer-events-none absolute bottom-0 left-0 bg-gradient-to-t to-transparent"
                style={{ height: "1.5rem", right: gutter }} /> : null}
</div>
```

`gutter` is `offsetWidth - clientWidth` — zero where the browser overlays its scrollbars rather than
reserving space. Sideways it is `offsetHeight - clientHeight` and the overlays inset from the bottom.

Recompute on `scroll` and from a `ResizeObserver` on the box **and on its children** — a filtered list
changes the rows' height, not the box's.

Splitting the box in two splits its classes too: one prop for how the box sits in its parent's layout,
another for the padding and spacing around what scrolls inside it.

## 8. Scrollbars that appear on hover

A bar standing in every list at rest is one more mark carrying no information. Hide the thumb and the
arrows, keep the bar's width so nothing reflows, and draw them only while the pointer is over the thing
that scrolls.

Two Chromium facts decide this, and both cost time to discover:

1. **`:hover` does not work here.** Chromium resolves the `::-webkit-scrollbar-*` pseudo-elements once and
   does not resolve them again when the scroller's hover state changes, so
   `div:hover::-webkit-scrollbar-thumb` is silently never applied — the element reports `:hover` and the
   thumb stays as it was. Setting an **attribute** forces the style recalculation that does pick them up.
2. **A styled scrollbar cannot fade.** Chromium runs no transition on a scrollbar pseudo-element; the
   colour jumps. Only the standard `scrollbar-color` animates, and setting it hands the whole bar back to
   the browser — a wide native one with its own arrows. That is a real trade, not an oversight: pick the
   design you drew and accept the instant appearance, or pick the fade and accept the platform's bar.
   Going back and forth wastes an afternoon.

```css
::-webkit-scrollbar { width: 8px; height: 8px; }
::-webkit-scrollbar-track, ::-webkit-scrollbar-corner { background: transparent; }
::-webkit-scrollbar-thumb { border: 2px solid transparent; border-radius: 9999px; background: transparent padding-box; }
[data-scrollbar-hover]::-webkit-scrollbar-thumb { background: <muted 35%> padding-box; }
::-webkit-scrollbar-button { display: block; width: 8px; height: 8px; opacity: 0;
  background: center / 7px 7px no-repeat; }
[data-scrollbar-hover]::-webkit-scrollbar-button { opacity: 1; }
/* One arrow at each end: Chrome offers a second at each, and those stay hidden. */
::-webkit-scrollbar-button:start:increment, ::-webkit-scrollbar-button:end:decrement { display: none; }
::-webkit-scrollbar-button:vertical:decrement { background-image: var(--scroll-arrow-up); }
```

The arrows are background images because Chrome's scrollbar parts ignore `mask-*`, which means each one
carries its own colour and needs a set per theme.

Firefox has no `::-webkit-` scrollbars and takes the standard properties instead, so it gets the same
hide-until-hover and a real fade with it:

```css
@supports not selector(::-webkit-scrollbar) {
  * { scrollbar-width: thin; scrollbar-color: transparent transparent; transition: scrollbar-color 150ms ease; }
  *[data-scrollbar-hover] { scrollbar-color: <muted 45%> transparent; }
}
```

The attribute comes from one listener on the document, since pointer events bubble: find the nearest
ancestor of the target whose computed `overflow` scrolls and which actually overflows, mark it, unmark the
last one. Clear it on `pointerleave` of the document and on window `blur`, or the pointer leaving the
window leaves a bar behind.

## 9. The theme

Three answers — Light, Dark, System — so it is a **menu, not a switch**: following the system is not the
halfway point between the other two, and a switch leaves no way back to it.

The trigger draws the theme **actually on screen** (`resolvedTheme`), not the answer that is set. A monitor
icon tells the reader nothing about what they are looking at; a sun or a moon always does. Which of the
three is set belongs in the menu and in the button's label. It lives in the sidebar's footer beside the
person and the sign-out, because it is a setting about the reader, not a place to go.

Four things that go wrong:

1. **The blocking script must be rendered by a server component**, into `<head>`. React does not execute a
   `<script>` it creates in the browser and logs an error when it meets one — which is what makes
   `next-themes` noisy under React 19. Owning this is about seventy lines; it is not worth a dependency.

   ```js
   (function(){try{var s=localStorage.getItem("theme");
   var d=s==="dark"||(s!=="light"&&window.matchMedia("(prefers-color-scheme: dark)").matches);
   var c=document.documentElement.classList;c.toggle("dark",d);c.toggle("light",!d);
   document.documentElement.style.colorScheme=d?"dark":"light";}catch(e){}})()
   ```

2. **Read the stored choice and the system setting with `useSyncExternalStore`**, not state written from an
   effect. Both return `undefined` from `getServerSnapshot`, which is what makes the next point possible.

3. **Nothing theme-dependent may reach the server render.** A label that names the current theme hydrates
   against a different word. Render no icon and a general label until the store has answered, and keep the
   button's size either way so nothing jumps.

4. **A tab's own write raises no `storage` event.** Notify local watchers by hand; another tab's change
   still arrives through `storage`. Store the system choice as *nothing*, so a browser that has never been
   told follows the system too.
