# Marp themes

Four custom themes for [Marp](https://marp.app/) presentations, plus demo decks and two Claude Code skills, one for building decks and one for designing new themes.

## The themes

| Theme | When to use it | Look |
|-------|----------------|------|
| `uofg` | Internal University of Glasgow presentations | Clean brand: Noto Sans, university blues |
| `uofg-creative` | External or mixed audiences with a UofG connection | UofG palette with Literata serif headings and warmer surfaces |
| `personal` | Personal or non-institutional talks | Dark and warm: Bricolage Grotesque, terracotta accent |
| `unrendered` | Informal technical talks | Slides that look like raw markdown source: black, heavy mono, single red accent |

The `unrendered` theme has a few authoring quirks, documented in the comment at the top of `themes/unrendered.css`.

Rendered demos of each theme are in `examples/`.

## Getting started

Requires Node 18 or newer.

```bash
git clone https://github.com/UoGSoE/marp-themes.git
cd marp-themes
npm install
```

Build a demo deck to HTML:

```bash
npx @marp-team/marp-cli examples/demo.md --theme-set ./themes/
```

Note the argument order: the markdown file must come before `--theme-set`. It is a greedy option, and with the file after it marp-cli prints its help text and builds nothing.

Other useful invocations:

```bash
# Live preview in the browser while editing
npx @marp-team/marp-cli -p examples/demo.md --theme-set ./themes/

# Export to PDF (--allow-local-files is needed when the deck references local images)
npx @marp-team/marp-cli --pdf examples/demo.md --theme-set ./themes/ --allow-local-files
```

## Writing a deck

Create a markdown file in `presentations/` with Marp frontmatter naming one of the themes:

```markdown
---
marp: true
theme: uofg
paginate: true
---

# My first slide
```

Slides are separated with `---`. See `examples/demo.md` for each theme's slide classes (lead slides, dividers, two-column layouts and so on), and `examples/demo-unrendered.md` for the unrendered theme's own classes.

## Claude Code skills

The repo ships two project skills in `.claude/skills/`:

- `marp-presentation` walks through building a deck: audience, source material, theme choice, then writes and compiles the slides.
- `marp-theme-creator` designs new theme CSS. Its `references/marp-theme-anatomy.md` collects the non-obvious parts of Marp theming (specificity traps, syntax-highlight variables, split-background clipping) and is worth reading even if you write the CSS by hand.

If you open this repo with Claude Code, the skills are picked up automatically.

## Contributing

Clone or fork the repo, `npm install`, and check your changes by rebuilding the demo decks against the themes. Pull requests are welcome.

## Licence

MIT. See [LICENSE](LICENSE).
