# Mind Map · Markdown 思维导图

> Paste a Markdown outline and get an interactive mind map: click a node to fold it, zoom and pan, export PNG or SVG. Parsing happens locally.

**English** | [简体中文](README.zh-CN.md)

<a href="https://bushimashen.github.io/mind-map/"><b>▶ Live demo</b></a>

## Features

- **Markdown in, mind map out** — `#`–`######` headings become levels, `-` / `*` / `+` / `1.` list items hang under the nearest heading, nesting follows indentation
- **Left-to-right tidy tree** — leaves stack on fixed rows, parents sit at the vertical centre of their children, so nothing overlaps
- **Fold / unfold** — click any node to collapse it; a `+N` badge shows how many descendants are hidden
- **Colour by depth** — root solid blue, then purple / cyan / green / orange / pink / slate, so depth is readable at a glance
- **View** — wheel zoom anchored at the cursor, drag to pan, fit-to-view, expand-all / collapse-all
- **Export** — 2× PNG, or SVG with concrete colours baked in (no CSS variables, so it opens correctly anywhere)
- **Load a file** — drop in a `.md` / `.txt` file instead of pasting

## Implementation notes

**The layout is a single pass, not a constraint solver.** A depth-first walk assigns each leaf the next free
row and then moves each parent to `(firstChild.y + lastChild.y) / 2`. It's the classic tidy-tree shortcut:
correct and O(n) as long as every node has one fixed row height — which is true here since all labels are
single-line.

**Node width is estimated, not measured.** `measureWidth()` sums ~14 px per CJK character and ~7.6 px per ASCII
character, capped at 300 px. That works before the SVG exists (no layout thrash, no hidden measuring container),
at the cost of being approximate for unusual fonts — the box is padded 6 px each side to absorb it.

**Folding re-runs the whole layout.** Rather than hiding DOM nodes in place, the collapsed set feeds back into
`layoutTree()`, so the visible tree is re-packed with no gaps. That's why folding a big branch visibly shrinks
the canvas instead of leaving a hole.

**Export builds a fresh SVG string** with literal colours and no `data-id` attributes, instead of cloning the live
DOM. Cloning would carry `var(--x)` references and interaction hooks that other tools can't resolve.

## Markdown support (and what's ignored)

| Supported | Ignored |
|---|---|
| ATX headings `#` … `######` | Paragraph text, blockquotes |
| `-` `*` `+` lists, `1.` `1)` ordered lists | Code fences, tables, images, links (rendered as plain text) |
| Indent-based nesting (tabs count as 2 spaces) | Inline emphasis — `**bold**` stays literal |

That's deliberate: a mind map is a skeleton, and trying to render full Markdown inside a node makes the node unreadable.

## Usage

Download `index.html` and double-click it. No install, no build, no network.

## Limits

- Single-line labels only; very long labels are truncated at 300 px with the text still centred.
- No editing on the canvas — edit the Markdown and regenerate.
- No free positioning or manual branch reordering.

## License

MIT — see [LICENSE](LICENSE).
