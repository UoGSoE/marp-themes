---
name: marp-presentation
description: "Create Marp markdown presentations through a guided workflow. Asks about audience and source material, picks the right theme, and builds the slides. Lives in the marp repo beside its four themes in ./themes/. Triggers on: presentation, slideshow, slides, Marp, deck, talk, or any request to turn notes/data into a presentation."
user-invocable: true
argument-hint: "[topic or path to source material]"
---

# Marp Presentation Skill

Create polished markdown-based slide decks using [Marp](https://marp.app/). This skill lives in the marp repo, and the four themes live beside it in `./themes/` at the repo root. Everything stays in plain text and is version-controllable.

## When to activate

- User asks to make a presentation, slideshow, slide deck, or talk
- User wants to turn notes, data, or ideas into slides
- User mentions Marp
- User says something like "let's make a presentation about X"

---

## Step 0: Project setup

Before doing anything else, silently ensure the working directory is ready to build Marp presentations.

### Install marp-cli if needed

Check if `@marp-team/marp-cli` is available:

```bash
npx @marp-team/marp-cli --version 2>/dev/null
```

If not available, install it:

```bash
npm init -y 2>/dev/null; npm install @marp-team/marp-cli
```

### Themes

The four themes are in `./themes/` at the repo root. They are the canonical copies: do not copy, regenerate, or modify them, and build with `--theme-set ./themes/`.

### Create presentations directory

```bash
mkdir -p presentations
```

---

## Step 1: Understand the presentation

If the user hasn't already explained what they need, ask these questions. Skip any that are already obvious from context. Ask them all at once — don't drip-feed.

### What to ask

**Topic & purpose:**
> What's this presentation about, and what's the goal? (inform, persuade, teach, update, etc.)

**Audience:**
> Who will be watching? This determines which theme we use:
> - **Internal UofG / institutional** → `uofg` theme (clean, brand-compliant, Noto Sans, university blues)
> - **Mixed audience or external with UofG connection** → `uofg-creative` theme (UofG palette but with Literata serif headings, warmer surfaces, more visual personality)
> - **Personal, casual, or non-UofG** → `personal` theme (dark warm palette, Bricolage Grotesque headings, terracotta accents)
> - **Techie talk that wants attitude** → `unrendered` theme (black slides, heavy mono, one hard red; slides look like raw markdown source: headings keep their #, bullets their *, links their [brackets](hrefs))

**Source material:**
> Do you have existing material to work from? For example:
> - Obsidian/markdown notes (give me the file paths or vault location)
> - A CSV or spreadsheet (I'll turn the data into tables and charts)
> - A document, PDF, or web page
> - Bullet points or an outline
> - Just a topic — I'll help structure it from scratch
>
> You can point me at multiple sources. I'll read everything and synthesise.

**Length & format:**
> Roughly how many slides? A 10-minute talk is typically 10–15 slides. A lightning talk is 5–8. A workshop deck might be 30+. If you're unsure, I'll aim for 12–15.

### What NOT to ask

Don't ask about fonts, colours, CSS, or technical Marp details. The themes handle all of that. The user just needs to think about content and audience.

---

## Step 2: Gather source material

Based on what the user said, read and process their source material.

### Obsidian / Markdown notes
- Read the files the user points to
- Look for structure: headings, bullet points, links between notes
- Identify the narrative arc — what order should things appear in?
- Pull out key quotes, data points, and examples

### CSV / Spreadsheet data
- Read the file
- Identify which columns and rows are most relevant
- Decide what belongs in tables vs what should be summarised as bullet points or key figures
- Keep tables concise — slides aren't spreadsheets. Max 5–6 rows per table, 4 columns. If there's more data, pick the most impactful subset and note what was left out

### Documents / PDFs
- Read and extract the key points
- Identify the structure and main arguments
- Pull out quotable lines for blockquote slides

### URLs / web pages
- Fetch and read the content
- Extract relevant information
- Credit the source in the slide footer or notes

### No source material
- Work with the user to build an outline
- Ask what 3–5 key points they want the audience to take away
- Suggest a structure (problem → solution → evidence → next steps, or similar)

---

## Step 3: Build the presentation

### File setup

Create the markdown file at an appropriate location. If the user doesn't specify, use `presentations/YYYY-MM-DD-topic-slug.md`.

### Frontmatter

```yaml
---
marp: true
theme: <chosen-theme>
paginate: true
header: '<contextual header>'
footer: '<contextual footer>'
---
```

Choose appropriate header/footer text. For UofG themes, the header might be the department or project name. For personal, it might be the presenter's name or nothing at all.

### Slide structure principles

**Open strong.** The title slide should make the audience curious, not just state the topic. A question, a surprising number, or a bold claim works better than "Quarterly Update Q3".

**One idea per slide.** If you're tempted to put two things on a slide, make two slides. Slides are free.

**Vary the rhythm.** Don't use the same layout for every slide. Mix:
- Text slides (bullet points, short paragraphs)
- Data slides (tables, key figures)
- Quote slides (blockquotes — good for breaking up dense sections)
- Divider slides (`<!-- _class: divider -->` — for section breaks)
- Lead slides (`<!-- _class: lead -->` — for title and closing)
- Image slides (background images with `![bg](path)`)
- Two-column slides (`<!-- _class: cols -->`)

**Keep text short.** Slides support the speaker, they don't replace the speaker. If a slide has more than ~40 words of body text, it's too much. Move detail to speaker notes (using HTML comments below the slide content).

**End with a clear call to action or takeaway.** Not just "Thanks / Questions?" but what should the audience do or remember?

### Slide classes reference

These CSS classes are available in all three themes:

| Directive | Effect |
|-----------|--------|
| `<!-- _class: lead -->` | Title/closing slides — large heading, themed background |
| `<!-- _class: invert -->` | Inverted colour scheme (UofG themes only) |
| `<!-- _class: divider -->` | Section divider — heading only, subtle background |
| `<!-- _class: cols -->` | Two-column layout (content splits at the heading) |
| `<!-- _class: light -->` | Light background variant (personal theme only) |
| `<!-- _class: accent -->` | Terracotta background for impact (personal theme only) |
| `<!-- _class: highlight -->` | Gold heading on navy for key takeaways (uofg-creative only) |
| `<!-- _class: title -->` | Title slide: tilted red banner crossing a right-hand image (unrendered only) |
| `<!-- _class: quotes -->` | Pull-quotes staggered left/right down the slide (unrendered only) |

### Unrendered theme authoring notes

The `unrendered` theme has quirks the others don't:

- **Title slides use a normal image, NOT `bg right`.** Marp clips split-background content at the halfway line, so the tilted banner could never cross the photo. Write `![](image.jpg)` on the `title` slide; the theme pins it as the right panel. All other slides use `![bg right](image.jpg)` as usual.
- **Bold goes red** (and slightly larger); `_em_` goes heavy white. Use bold as the highlighter pen.
- **Link hrefs are shown literally** on the slide, so write them to be read, not clicked.
- **Use `console` fenced code blocks** for shell examples to get the red `$` prompt.
- A bolded mid-word slash (`remarks**/**quotes`) is not valid CommonMark and renders literally. Put spaces round it: `remarks **/** quotes`.
- To show a literal HTML entity as part of the joke (like the `&copy;` on the original deck), write `&amp;copy;`.

### Code blocks

For technical presentations, use fenced code blocks with language identifiers. The themes have custom syntax highlighting tuned for readability on their dark code block backgrounds.

### Tables

Keep tables simple. The themes style them with clean headers and no row striping. Don't cram too much data in — summarise instead.

### Speaker notes

Add speaker notes for anything the presenter needs to say but shouldn't be on the slide:
```markdown
<!--
This is where you'd explain the methodology in more detail.
The chart shows a 40% improvement but the sample size caveat is important.
-->
```

---

## Step 4: Build and preview

After creating the markdown file, compile it. The markdown file must come BEFORE `--theme-set`: it is a greedy array option, and with the file after it marp-cli (v4.x) swallows the path into the array, prints its usage text, and exits 0 without building anything.

```bash
npx @marp-team/marp-cli presentations/your-file.md --theme-set ./themes/
```

This produces an HTML file in the same directory. Open it for the user to preview:

```bash
open presentations/your-file.html   # macOS
xdg-open presentations/your-file.html  # Linux
```

If the user wants PDF output (add `--allow-local-files` whenever the deck references local images):
```bash
npx @marp-team/marp-cli --pdf presentations/your-file.md --theme-set ./themes/ --allow-local-files
```

If the user wants to iterate with live preview:
```bash
npx @marp-team/marp-cli -p presentations/your-file.md --theme-set ./themes/
```

### Verify by looking

Before showing the user, render the deck to PNGs in a temp directory and actually look at each slide:

```bash
npx @marp-team/marp-cli presentations/your-file.md --theme-set ./themes/ --images png --allow-local-files -o <tmpdir>/slides.png
```

Read the images and check: nothing clipped or overflowing, colours as intended, code blocks readable, no default-theme leakage (blue headings or white background mean the theme failed to load, likely a CSS parse error). Fix problems before the user sees them.

---

## Step 5: Iterate

After showing the first build, ask:

> How does that look? Anything you'd like to change — order, emphasis, more/fewer slides, different tone?

Be ready to:
- Reorder slides
- Add or remove content
- Adjust tone (more formal, more casual, more technical)
- Add speaker notes
- Switch themes if the audience has changed
- Add data slides from additional source material

---

## Important notes

- **Don't over-explain Marp to the user.** They don't need to know about CSS themes or markdown directives. Just make the presentation.
- **Don't ask too many questions.** Get the essentials in Step 1, then produce a first draft. Iteration is cheaper than interrogation.
- **Don't modify the theme CSS.** Use the three themes as they are.
- **Always compile and open the result** so the user can see what they're getting.
- **This skill lives in the marp repo.** The themes are the canonical copies in `./themes/` at the repo root.
