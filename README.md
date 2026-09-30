# quiet-ui

An [Agent Skill](https://agentskills.io) that sets the visual language for any interface an AI assistant builds or restyles. Left alone, most assistants draw every region as a bordered card with a rounded corner and a drop shadow, put a 30px heading over an uppercase eyebrow, frame every table and fill a pill for every status. This skill replaces that with one flat surface, no shadows, one text size, hue-free greys, and colour kept for state.

Every rule follows from one question the assistant asks before drawing anything: what does this mark tell the reader that they could not already see? A border claims two things are separate, a shadow claims something floats, a bigger size claims rank, a colour claims a state. If the claim is false, the mark does not go in.

## Install

```bash
npx skills add Gabbbb23/quiet-ui --skill quiet-ui
```

Or copy `skills/quiet-ui/` into the folder your agent loads skills from (`.claude/skills/`, `~/.claude/skills/`, or your tool's equivalent).

## What changes

| Usually drawn | With quiet-ui |
| --- | --- |
| Every region a bordered card with a shadow | One surface; a heading line on a hairline |
| `shadow-sm` at rest, `shadow-lg` on overlays | No shadows; a popover is one step lighter than the page |
| 30px heading over an uppercase eyebrow | One line at body size: section / page name |
| Table in a frame, a rule under every row | No frame, no rules; a rounded hover tint on the row |
| A filled pill for every status | The status's own icon and hue on a faint wash, beside its word |
| Search box plus a Search button | Results follow the typing after about 300ms |
| Numbered page buttons | "1–20 of 89" and a step either way |
| Blue-tinted greys | Greys with no hue in either theme |

## Files

```
skills/
└── quiet-ui/
    ├── SKILL.md       # the principles, the rules, and a checklist to run before calling a screen done
    ├── patterns.md    # worked React + Tailwind v4 + shadcn/ui recipes: the shell, page header, register toolbar,
    │                  # table, list row, scrolling fade, hover-only scrollbars, theme menu
    └── tokens.css     # the light and dark palettes and the identity hues, as shadcn/ui variables
```

`tokens.css` uses shadcn/ui's variable names, so a shadcn project can paste both blocks into its global stylesheet. Swap `--primary` and `--destructive` for your product's accent and alarm colours and leave the greys as they are. In another stack, keep the reasoning in `patterns.md` and translate the class names.

## Works alongside

`SKILL.md` refers to `no-ui-slop`, a separate skill for the words on a screen (headings, labels, empty states). It is not part of this repo. quiet-ui works without it; its principle P9 simply has nothing to point at.

## License

MIT
