# Marp Presentation Project

Markdown-based slide decks using [Marp](https://marp.app/). Everything stays in plain text and is version-controllable. Decks go in `presentations/`, demos in `examples/`, themes in `themes/`.

Two project skills (in `.claude/skills/`) do the heavy lifting:

- **`marp-presentation`** builds slide decks: asks about audience and source material, picks the theme, writes and compiles the slides.
- **`marp-theme-creator`** designs new theme CSS. Its `references/marp-theme-anatomy.md` holds the hard-won Marp CSS gotchas: read it before touching any theme.

## Themes

- **`uofg`** - internal University of Glasgow (Noto Sans, university blues)
- **`uofg-creative`** - external or mixed audience with a UofG connection (Literata serif headings, warmer palette)
- **`personal`** - personal or non-institutional (dark warm palette, Bricolage Grotesque, terracotta accent)
- **`unrendered`** - techie talks with attitude; slides look like raw markdown source (black, heavy mono, one hard red). Authoring notes are in the comment at the top of the CSS

## Building

The markdown file must come BEFORE `--theme-set`: it is a greedy array option, and with the file after it marp-cli builds nothing and exits 0.

```bash
# HTML
npx @marp-team/marp-cli presentations/your-file.md --theme-set ./themes/

# Live preview
npx @marp-team/marp-cli -p presentations/your-file.md --theme-set ./themes/

# PDF (--allow-local-files is needed when the deck references local images)
npx @marp-team/marp-cli --pdf presentations/your-file.md --theme-set ./themes/ --allow-local-files
```
