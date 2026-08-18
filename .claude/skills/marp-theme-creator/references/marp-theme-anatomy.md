# Marp Theme CSS — What You Actually Need to Know

This is a practical reference for building Marp custom themes. It covers the gotchas that aren't obvious from the documentation.

## Theme declaration

Every Marp theme CSS file MUST begin with this comment:
```css
/* @theme my-theme-name */
```
This is how Marpit registers the theme. Without it, the theme won't load.

## Extending the default theme

```css
@import 'default';
```

This gives you sensible base styles for typography, lists, tables, etc. But it also gives you a LOT of opinionated styling that's hard to override because of how Marp compiles CSS.

### The specificity problem

Marp compiles all theme CSS and wraps selectors with a very long prefix:
```
div#\:\$p > svg > foreignObject > section
```

Both the default theme's rules AND your custom rules get this prefix. So specificity is usually equal between them. **The problem is source order**: some of the default theme's rules appear AFTER your custom rules in the compiled output, so they win by cascade.

### The solution: override CSS variables

The default theme uses CSS custom properties for almost everything. Override these in `:root` rather than fighting selector specificity:

```css
:root {
  /* Kill blockquote left border */
  --borderColor-default: transparent;

  /* Kill table row striping */
  --bgColor-muted: transparent;

  /* Kill default background colour (so yours shows through) */
  --bgColor-default: transparent;
}
```

For properties where variables aren't enough, use `!important` as a last resort:
```css
section blockquote {
  border: none !important;
}
section table tr {
  background-color: transparent !important;
}
```

### Bold text inside headings turns blue

The default theme has this rule (compiled after yours, so it wins over `section strong`):

```css
section :is(h2, h3, h4, h5, h6) strong {
  color: var(--heading-strong-color);  /* light-dark(#48c, #7bf) - blue */
  font-weight: inherit;
}
```

If your theme colours `strong`, also set the variable in `:root`:

```css
--heading-strong-color: var(--accent);
```

### CSS comments must never contain `*/` mid-text

Obvious once said, easy to hit when a comment quotes markdown: writing an example like `remarks**/**quotes` inside a comment terminates it at the `*/`, the rest of the file becomes garbage, the whole stylesheet fails to parse, and Marp silently falls back to the default theme (white background, blue headings). If a preview suddenly renders in default-theme colours, check for this before debugging anything else.

## Syntax highlighting

Marp uses highlight.js for code blocks. The syntax colours are controlled by `--color-prettylights-syntax-*` CSS variables.

**Critical**: The default theme defines these with `light-dark()`:
```css
--color-prettylights-syntax-keyword: light-dark(#cf222e, #ff7b72);
```

The `light-dark()` function picks the first value for `color-scheme: light` and the second for `color-scheme: dark`. Since the default colour scheme is `light`, you get colours designed for WHITE backgrounds — even if your code block has a dark background.

**You MUST override these variables** if your code blocks have dark backgrounds:

```css
:root {
  /* GitHub dark theme values — good baseline for dark code blocks */
  --color-prettylights-syntax-comment: #8B95A8;
  --color-prettylights-syntax-constant: #79C0FF;
  --color-prettylights-syntax-entity: #D2A8FF;
  --color-prettylights-syntax-entity-tag: #7EE787;
  --color-prettylights-syntax-keyword: #FF7B72;
  --color-prettylights-syntax-string: #A5D6FF;
  --color-prettylights-syntax-variable: #FFA657;
  --color-prettylights-syntax-brackethighlighter-angle: #9198A1;
  --color-prettylights-syntax-brackethighlighter-unmatched: #F85149;
  --color-prettylights-syntax-carriage-return-bg: #B62324;
  --color-prettylights-syntax-carriage-return-text: #F0F6FC;
  --color-prettylights-syntax-markup-heading: #1F6FEB;
  --color-prettylights-syntax-markup-bold: #F0F6FC;
  --color-prettylights-syntax-markup-italic: #F0F6FC;
  --color-prettylights-syntax-markup-list: #F2CC60;
  --color-prettylights-syntax-storage-modifier-import: #D0D8E8;
  --color-prettylights-syntax-string-regexp: #7EE787;
}
```

Adjust these to match your theme's palette for a cohesive feel.

## Core HTML structure

Each slide is a `<section>` element. All selectors work from `section`:

| Selector | Purpose |
|----------|---------|
| `section` | Base slide (background, font, padding, dimensions) |
| `section.lead` | Title/lead slides — apply with `<!-- _class: lead -->` |
| `section.invert` | Inverted colour scheme — `<!-- _class: invert -->` |
| `section.divider` | Section break (custom class, not built-in) |
| `section h1` – `section h6` | Headings |
| `section p` | Paragraphs |
| `section a` | Links |
| `section ul`, `section ol` | Lists |
| `section li` | List items |
| `section blockquote` | Block quotes |
| `section pre` | Code block containers |
| `section pre code` | Code block text |
| `section code` | Inline code |
| `section table` | Tables |
| `section table th` | Table headers |
| `section table td` | Table cells |
| `header` | Slide header (from `header:` frontmatter) |
| `footer` | Slide footer (from `footer:` frontmatter) |
| `section::after` | Pagination (from `paginate: true`) |

## Slide dimensions

Set on `section` using **absolute units only** (px):
```css
section {
  width: 1280px;   /* 16:9 */
  height: 720px;
}
```

## Custom slide classes

You can define any class name and apply it with `<!-- _class: yourclass -->`:
```css
section.fancy {
  background: navy;
  color: white;
}
```

The `lead` and `invert` classes are conventions from the built-in themes, but you can add as many custom classes as you like.

## Split backgrounds clip your content at the split line

With `![bg right](img.jpg)`, Marpit renders the slide as multiple layers: the image goes in a full-width background layer, but the CONTENT goes inside `<foreignObject width="50%">`. That is a hard clip: no negative margin, transform, or `overflow: visible` lets content paint past the halfway line, because the clip is on the SVG element that contains it.

Fine for ordinary split slides. It matters when a design element must cross from the text side onto the image (a banner, a rotated ribbon, an overlapping card). For those, skip the bg syntax and let a slide class position a NORMAL in-flow image as the panel instead, so image and content share one layer:

```css
section.title {
  position: relative;
  padding-right: 42%;         /* keep normal flow off the image */
}
section.title img {
  position: absolute;
  top: 0; right: 0;
  width: 45%; height: 720px;
  object-fit: cover;
}
section.title p:has(img) { margin: 0; }  /* neutralise the img's <p> wrapper */
section.title h2 {                        /* the element that crosses over */
  position: relative;
  z-index: 1;                 /* above the positioned img */
  background: var(--bg);
  margin-right: -110%;        /* extend the box across the panel */
  transform: rotate(-3deg);
  transform-origin: left center;
}
```

The author writes `![](img.jpg)` on that slide, not `![bg right](img.jpg)`. Content that overflows the slide edge is clipped at the slide boundary by the outer foreignObject, which is usually what you want for full-bleed effects.

## Markdown authoring limits worth knowing

- Emphasis cannot open mid-word against punctuation: `remarks**/**quotes` renders literally (the opening `**` sits between a letter and punctuation, so CommonMark won't open it). Spaced, `remarks **/** quotes`, it works.
- `console` fenced code blocks give shell prompts a highlight class (`hljs-meta`), so a theme can colour the `$` differently from the command.

## Header, footer, and pagination

Headers and footers are positioned elements. They have NO default styling from the base — you must position them:
```css
header, footer {
  position: absolute;
  left: 70px;
  right: 70px;
  font-size: 14px;
}
header { top: 22px; }
footer { bottom: 22px; }
```

Pagination renders in `section::after`:
```css
section::after {
  font-size: 13px;
  bottom: 22px;
  right: 70px;
}
```

## Google Fonts

Import fonts with `@import url(...)` AFTER the theme import:
```css
/* @theme my-theme */
@import 'default';
@import url('https://fonts.googleapis.com/css2?family=...');
```

## Building with the theme

The markdown file must come BEFORE `--theme-set`. It is a greedy array option: with the file after it, marp-cli (v4.x) swallows the path into the array, prints its usage text, and exits 0 without building anything. An "it printed the help and made no file" result is this, not a syntax error.

```bash
# Single file
npx @marp-team/marp-cli slides.md --theme-set ./themes/

# Live preview
npx @marp-team/marp-cli -p slides.md --theme-set ./themes/

# PDF export (needs --allow-local-files if the deck references local images)
npx @marp-team/marp-cli --pdf slides.md --theme-set ./themes/ --allow-local-files

# Render every slide to PNG for visual verification
npx @marp-team/marp-cli slides.md --theme-set ./themes/ --images png --allow-local-files -o out/slides.png
```

The PNG form is the useful one for checking your own work: render, then look at every image before showing anyone. HTML output never needs `--allow-local-files`; PDF and image output do whenever local files are referenced.

## Common mistakes

1. **Setting `color` on `pre code` expecting it to change syntax highlighting** — it only sets the fallback colour. The hljs spans override it. Use the `--color-prettylights-syntax-*` variables.
2. **Using `border: none` on blockquotes without `!important`** — the default theme's border-left rule has equal specificity and may appear later in the cascade.
3. **Forgetting to set `--bgColor-muted: transparent`** — this is what the default theme uses for table row striping (`tr:nth-child(2n)`).
4. **Using relative units for slide dimensions** — Marp requires px for width/height.
5. **Not testing both HTML and PDF output** — rendering can differ.
6. **Putting the markdown file after `--theme-set`** - greedy array option; builds nothing, exits 0.
7. **A `*/` inside a CSS comment** - kills the whole stylesheet; Marp silently falls back to the default theme.
8. **Expecting content to escape a `![bg left/right]` split** - the content layer is clipped at the split line; use an in-flow image and a slide class instead.
9. **Colouring `strong` without setting `--heading-strong-color`** - bold inside h2-h6 comes out blue.
