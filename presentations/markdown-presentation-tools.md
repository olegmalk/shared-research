# Markdown/CSS Presentation Tools — Deep Research Report

Documented: 2026-02-17

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Marp Deep Dive](#marp-deep-dive)
3. [Competitor Analysis](#competitor-analysis)
4. [Comparison Matrix](#comparison-matrix)
5. [Claude Code Integration Analysis](#claude-code-integration-analysis)
6. [Skill Design Recommendations](#skill-design-recommendations)
7. [Sources](#sources)

---

## Executive Summary

**Recommendation: Marp is the optimal choice for Claude Code integration.**

Marp wins on every criterion that matters for LLM-driven presentation generation:
- Pure Markdown input (no JSX, no Vue, no HTML required)
- Headless CLI export to PDF/PPTX/HTML/PNG without GUI
- Simple theming via CSS files
- npm installable, zero config needed for basic use
- Docker image for CI/CD
- Active maintenance (v4.2.3, updated Jan 2026)

Slidev is the strongest alternative if interactive/developer-facing presentations are needed, but its Vue dependency and complexity make it less suitable for non-technical users.

---

## Marp Deep Dive

### Ecosystem Components

| Component | Package | Purpose |
|-----------|---------|---------|
| Marp CLI | `@marp-team/marp-cli` v4.2.3 | Conversion engine (CLI) |
| Marp Core | `@marp-team/marp-core` v4.x | Rendering engine |
| Marpit | `@marp-team/marpit` | Base framework |
| Marp for VS Code | `marp-team.marp-vscode` | Editor extension |

### Installation

```bash
# One-shot (no install)
npx @marp-team/marp-cli@latest slides.md --pdf

# Global
npm install -g @marp-team/marp-cli

# Local project
npm install --save-dev @marp-team/marp-cli

# Homebrew
brew install marp-cli

# Docker
docker pull marpteam/marp-cli
```

### CLI Commands Reference

```bash
# Basic conversions
marp slides.md                          # → HTML (default)
marp slides.md --pdf                    # → PDF
marp slides.md --pptx                   # → PowerPoint
marp slides.md --images png             # → PNG per slide
marp slides.md --images jpeg            # → JPEG per slide
marp slides.md --image png              # → Single title image
marp slides.md --notes                  # → Speaker notes as TXT

# Output control
marp slides.md -o output.pdf            # Explicit output path
marp slides.md --pdf -o deck.pdf        # Named PDF

# PDF options
marp slides.md --pdf --pdf-notes        # Include presenter notes
marp slides.md --pdf --pdf-outlines     # Add bookmarks

# PPTX options
marp slides.md --pptx                   # Standard (slides as images)
marp slides.md --pptx --pptx-editable  # Editable text (experimental, needs LibreOffice)

# Image options
marp slides.md --images png --image-scale 2  # 2x resolution

# Development
marp slides.md -w                       # Watch mode (auto-rebuild)
marp slides.md -s                       # Server mode (HTTP)
marp slides.md -p                       # Open preview window

# Batch conversion
marp -I ./slides/                       # Convert directory
marp -I ./slides/ --pdf                 # All to PDF
marp *.md --pdf                         # Glob pattern
marp slides.md -P 10                    # 10 parallel workers

# Browser options
marp slides.md --pdf --browser chrome   # Specify browser
marp slides.md --pdf --browser firefox  # Firefox support (new in v4)
marp slides.md --pdf --browser-path /usr/bin/chromium
marp slides.md --pdf --browser-timeout 60000  # 60s timeout

# Advanced
marp slides.md --allow-local-files      # Allow local file:// resources
marp slides.md -c marp.config.mjs       # Custom config file
marp slides.md --theme custom-theme.css # Use custom theme
marp slides.md --html                   # Enable HTML in Markdown
```

### Markdown Syntax

```markdown
---
marp: true
theme: default
paginate: true
header: "Company Name"
footer: "Confidential"
title: "Q4 Results"
author: "Oleg Malkov"
size: 16:9
math: katex
style: |
  section {
    font-family: 'Inter', sans-serif;
  }
  h1 {
    color: #2563eb;
  }
---

# Slide 1 — Title

This is the first slide.

---

# Slide 2 — With Speaker Notes

Content here.

<!-- This is a speaker note. Exported with --notes or --pdf-notes -->

---

<!-- _class: lead -->
<!-- _backgroundColor: #1e293b -->
<!-- _color: white -->

# Dark Slide

Scoped directives (underscore prefix) apply only to this slide.

---

<!-- _paginate: false -->

# No Page Number Here

Background image:
<!-- _backgroundImage: url('https://example.com/bg.jpg') -->
<!-- _backgroundSize: cover -->

---

# Code Block

```javascript
const greeting = "Hello, World!";
console.log(greeting);
```

---

# Math

Inline: $E = mc^2$

Block:
$$
\int_0^\infty e^{-x^2} dx = \frac{\sqrt{\pi}}{2}
$$

---

# Auto-Fitting Text

<!-- fit --> automatically scales heading text to fit the slide width.

# <!-- fit --> This heading will auto-scale

---

# Images

![w:300](image.png)          <!-- Width 300px -->
![h:200](image.png)          <!-- Height 200px -->
![bg](background.jpg)        <!-- Background image -->
![bg left:40%](sidebar.jpg)  <!-- Split layout: image left 40% -->
![bg right](sidebar.jpg)     <!-- Split layout: image right -->
![bg blur:5px](bg.jpg)       <!-- Blurred background -->
![bg brightness:.5](bg.jpg)  <!-- Darkened background -->

---

# Fragmented List (Progressive Reveal)

* First point
* Second point
* Third point
```

### Directives Reference

**Global Directives** (affect entire deck, set in front matter or HTML comments):

| Directive | Purpose | Example |
|-----------|---------|---------|
| `marp` | Enable Marp rendering | `marp: true` |
| `theme` | Set theme | `theme: gaia` |
| `style` | Custom CSS | `style: \| section { ... }` |
| `headingDivider` | Auto-split on headings | `headingDivider: 2` |
| `size` | Slide dimensions | `size: 16:9` or `size: 4:3` |
| `math` | Math renderer | `math: katex` or `math: mathjax` |
| `title` | Metadata title | `title: My Deck` |
| `author` | Metadata author | `author: Name` |
| `description` | Metadata description | `description: About...` |
| `keywords` | Metadata keywords | `keywords: a, b, c` |
| `url` | Canonical URL | `url: https://...` |
| `image` | OG image | `image: https://...` |

**Local Directives** (per-slide, inherited by subsequent slides):

| Directive | Purpose |
|-----------|---------|
| `paginate` | Show page numbers (`true`/`false`/`skip`) |
| `header` | Slide header content (supports Markdown) |
| `footer` | Slide footer content (supports Markdown) |
| `class` | CSS class for slide `<section>` |
| `backgroundColor` | Background color |
| `backgroundImage` | Background image URL |
| `backgroundPosition` | CSS background-position |
| `backgroundRepeat` | CSS background-repeat |
| `backgroundSize` | CSS background-size |
| `color` | Text color |

**Scoped Directives**: Prefix with `_` to apply only to current slide (no inheritance):
- `_class`, `_backgroundColor`, `_color`, `_paginate`, etc.

### Built-in Themes

| Theme | Style |
|-------|-------|
| `default` | Clean, minimal, light background (GitHub-like) |
| `gaia` | Modern with enhanced visuals, supports `lead` and `invert` classes |
| `uncover` | Sophisticated, unique transitions |

### Custom Theme CSS Structure

```css
/* @theme my-corporate-theme */

@import 'default';

:root {
  --color-background: #ffffff;
  --color-foreground: #1a1a1a;
  --color-highlight: #2563eb;
  --color-dimmed: #6b7280;
}

section {
  font-family: 'Inter', 'Helvetica Neue', sans-serif;
  font-size: 28px;
  padding: 40px;
  background-image: url('logo.svg');
  background-repeat: no-repeat;
  background-position: bottom 20px right 20px;
  background-size: 80px;
}

h1 {
  color: var(--color-highlight);
  border-bottom: 3px solid var(--color-highlight);
  padding-bottom: 10px;
}

h2 {
  color: var(--color-foreground);
}

/* Lead slide class */
section.lead {
  text-align: center;
  justify-content: center;
}

section.lead h1 {
  font-size: 60px;
}

/* Invert class */
section.invert {
  background-color: #1e293b;
  color: white;
}

/* Code blocks */
pre {
  background: #f8fafc;
  border-radius: 8px;
  padding: 16px;
}

/* Tables */
table {
  border-collapse: collapse;
  width: 100%;
}

th {
  background-color: var(--color-highlight);
  color: white;
}
```

Register in CLI: `marp slides.md --theme my-corporate-theme.css --pdf`

### Configuration File

Supported formats: `marp.config.js`, `marp.config.mjs`, `marp.config.cjs`, `.marprc` (JSON/YAML), `.marprc.json`, `.marprc.yaml`, `package.json` (`marp` section).

```javascript
// marp.config.mjs
export default {
  allowLocalFiles: true,
  html: true,
  options: {
    markdown: {
      breaks: false,
      typographer: true,
    },
  },
  themeSet: './themes/',
  output: './output/',
  inputDir: './slides/',
  pdf: true,
};
```

### VS Code Extension Features

- Live preview with active slide highlighting
- IntelliSense for directives (autocomplete, hover help, diagnostics)
- Syntax highlighting for Marp-specific syntax
- Export to PDF/PPTX/HTML/PNG via command palette
- Custom theme CSS hot-reload
- Foldable slide outline
- Limitation: Export requires desktop VS Code (not vscode.dev)

### Marp CLI v4 Breaking Changes (Current Version)

- Node.js >= 18 required
- Chrome new headless mode by default
- Firefox browser support added
- Docker base image changed from Alpine to Debian
- `--browser-protocol` option (CDP or WebDriver BiDi)
- Debug logging via `--debug`
- GitHub Container Registry: `ghcr.io/marp-team/marp-cli`

---

## Competitor Analysis

### 1. Slidev

**Package**: `@slidev/cli` v52.12.0 | **Stars**: ~34k | **Maintenance**: Active (2026)

**What it is**: Vue 3-powered presentation framework for developers. Rich features, steep learning curve.

**Installation**:
```bash
npm init slidev@latest
# or
pnpm create slidev
```

**CLI Commands**:
```bash
slidev                     # Dev server
slidev export              # Export (PDF default)
slidev export --format pptx
slidev export --format png
slidev build               # Static SPA
slidev format              # Format markdown
```

**Input format**: Extended Markdown with Vue components, UnoCSS classes, YAML front matter per slide.

**Key features**:
- Shiki syntax highlighting (best-in-class)
- Monaco Editor for live code editing in slides
- Mermaid/PlantUML diagrams
- KaTeX math
- Drawing/annotation tools (Drauu)
- Built-in recording (RecordRTC)
- Slide transitions/animations
- Vue component embedding
- Theme ecosystem (npm-based)
- HMR (instant preview updates)

**Export**: PDF, PPTX (slides as images), PNG, SPA (static HTML)

**Limitations**:
- Requires Vue/Vite knowledge for customization
- PPTX export = image-based (not editable text)
- Heavy dependency tree
- Not ideal for non-developers

---

### 2. reveal.js + reveal-md

**Package**: `reveal.js` v5.2.1, `reveal-md` v6.1.4 | **Stars**: ~68k (reveal.js) | **Maintenance**: Active

**What it is**: The most mature HTML presentation framework. `reveal-md` wraps it for Markdown-first workflow.

**Installation**:
```bash
npm install -g reveal-md
# or use reveal.js directly
npm install reveal.js
```

**CLI (reveal-md)**:
```bash
reveal-md slides.md                     # Dev server
reveal-md slides.md --static _static    # Export to static HTML
reveal-md slides.md --print slides.pdf  # Export to PDF (Puppeteer)
reveal-md slides.md -w                  # Watch mode
```

**Input format**: Markdown (via reveal-md) or HTML. Slides separated by `---` (horizontal) and `----` (vertical/nested).

**Key features**:
- Nested/vertical slides
- Auto-Animate (element transitions between slides)
- Speaker notes (press `S`)
- LaTeX math
- Syntax highlighting (highlight.js)
- Rich plugin ecosystem
- PDF export (via print stylesheet or Puppeteer)
- Multiplexing (audience follows presenter)
- Fragments (incremental reveals)

**Limitations**:
- HTML-centric (Markdown is a plugin, not native)
- PDF export requires manual print-to-PDF or DeckTape
- No native PPTX export
- More complex setup than Marp
- reveal-md last published 1+ year ago

---

### 3. Remark.js

**Package**: Browser-based (no npm CLI) | **Stars**: ~12.5k | **Maintenance**: Low

**What it is**: Simple, in-browser Markdown slideshow. No build step. Load via CDN.

**Usage**: Include JS in HTML file, write Markdown in `<textarea>`.

**Key features**:
- Pure Markdown input
- Presenter mode (press `P`)
- Speaker notes
- Syntax highlighting (highlight.js)
- CSS theming
- Cloned view (presenter + audience)

**Export**: No native export. Use Chrome Print-to-PDF or DeckTape.

**Limitations**:
- No CLI tool
- No native PDF/PPTX export
- No npm binary
- Minimal maintenance
- No headless mode

---

### 4. Spectacle

**Package**: `spectacle` v10.2.3, `spectacle-cli` | **Maintenance**: Active (NearForm)

**What it is**: React-based presentation library using JSX/MDX.

**Installation**:
```bash
npm install spectacle
npm install -g spectacle-cli
```

**Key features**:
- JSX or MDX input
- Live code demos in slides
- Transitions/animations
- Theme system
- Presenter mode

**Export**: PDF via `spectacle-renderer` (headless Chrome)

**Limitations**:
- Requires React knowledge
- JSX-heavy (not pure Markdown)
- Separate tool for PDF export
- No PPTX export
- Complex for non-developers

---

### 5. MDX Deck

**Package**: `mdx-deck` | **Stars**: ~11k | **Maintenance**: Unmaintained

**What it is**: MDX-powered slide decks. Write slides in MDX (Markdown + JSX).

**Status**: **Not recommended.** Last meaningful update years ago. Missing PDF export. Broken dependencies reported.

---

### 6. Pandoc + Beamer

**Package**: `pandoc` (system binary) | **Maintenance**: Active

**What it is**: Universal document converter. Can produce Beamer (LaTeX) PDF slides from Markdown.

**CLI**:
```bash
pandoc -t beamer slides.md -o slides.pdf
pandoc -t beamer slides.md -V theme:Warsaw -o slides.pdf
pandoc -t beamer slides.md --pdf-engine xelatex -o slides.pdf
pandoc slides.md -t revealjs -s -o slides.html   # reveal.js HTML output
```

**Key features**:
- Pure Markdown input
- Beamer themes (Warsaw, Berlin, Madrid, etc.)
- LaTeX math (native)
- PDF output (via LaTeX)
- Can also output reveal.js HTML
- Academic-quality typography

**Limitations**:
- Requires LaTeX installation (heavy, ~2-5GB)
- No PPTX export for Beamer (but Pandoc can do Markdown→PPTX directly)
- Beamer aesthetics are "academic" — not modern/corporate
- No watch mode or dev server
- Steep learning curve for LaTeX customization

---

### 7. Deck.js

**Status**: **Dead.** Last updated 9+ years ago. Not recommended.

---

### 8. MkSlides

**Package**: `mkslides` (Python/pip) | **Maintenance**: Active (2025)

**What it is**: MkDocs-inspired Markdown→slides tool, using reveal.js under the hood.

```bash
pip install mkslides
mkslides build              # Build static HTML
mkslides serve              # Live preview
```

**Features**: reveal.js themes, highlight.js, YAML config, live reload. Has MCP server integration.

**Limitations**: Python-based (not Node.js), limited export options, small community.

---

### 9. Deckset (macOS only)

**Status**: Commercial macOS app. Not CLI-compatible. Not relevant for automation.

---

## Comparison Matrix

| Feature | Marp | Slidev | reveal.js + reveal-md | Remark.js | Spectacle | Pandoc+Beamer |
|---------|------|--------|----------------------|-----------|-----------|---------------|
| **Input** | Pure Markdown | Extended MD + Vue | MD or HTML | Pure MD | JSX/MDX | Pure MD |
| **PDF Export** | CLI (`--pdf`) | CLI (`export`) | Puppeteer/DeckTape | Chrome Print | spectacle-renderer | Native (LaTeX) |
| **PPTX Export** | CLI (`--pptx`) | CLI (images) | No | No | No | Pandoc (separate) |
| **HTML Export** | Default output | `build` (SPA) | Default output | In-browser | `build` | Pandoc flag |
| **Image Export** | PNG/JPEG per slide | PNG per slide | No | No | No | No |
| **Theming** | CSS files | npm themes + UnoCSS | CSS/SCSS + plugins | CSS | React themes | LaTeX themes |
| **Code Highlight** | Prism.js | Shiki (best) | highlight.js | highlight.js | Prism.js | Pandoc |
| **Speaker Notes** | Yes (HTML comment) | Yes | Yes | Yes | Yes | Yes |
| **Animations** | CSS transitions | Vue transitions | Auto-Animate | None | React Spring | None |
| **Math** | KaTeX/MathJax | KaTeX | MathJax plugin | Plugin | Plugin | LaTeX native |
| **Diagrams** | No (manual images) | Mermaid/PlantUML | Plugin | No | No | No |
| **CLI** | Full (`marp`) | Full (`slidev`) | `reveal-md` | None | `spectacle-cli` | `pandoc` |
| **Headless Export** | Yes (Chrome/FF) | Yes (Playwright) | Yes (Puppeteer) | No | Yes (Chrome) | Yes (LaTeX) |
| **npm Package** | `@marp-team/marp-cli` | `@slidev/cli` | `reveal-md` | N/A | `spectacle-cli` | N/A (system) |
| **Docker** | Official image | Community | Community | No | No | Official |
| **Node.js** | >= 18 | >= 18 | >= 14 | N/A | >= 16 | N/A |
| **GitHub Stars** | ~3.1k (CLI), ~1.9k (VS Code) | ~34k | ~68k (reveal.js) | ~12.5k | ~9.7k | ~35k |
| **Last Update** | Jan 2026 | Active 2026 | 2024-2025 | Low activity | 2025 | Active |
| **Learning Curve** | Low | Medium-High | Medium | Low | High | High |
| **Non-dev Friendly** | Yes | No | Somewhat | Yes | No | No |

---

## Claude Code Integration Analysis

### Criteria for LLM-Driven Presentation Generation

1. **Markdown-native** — LLMs generate Markdown fluently
2. **CLI-first** — callable from Bash, no GUI needed
3. **Headless export** — PDF/PPTX without display server
4. **Simple syntax** — fewer special constructs = fewer LLM errors
5. **Template/theme system** — reusable corporate identity
6. **Fast execution** — quick feedback loop
7. **Minimal dependencies** — easy to install/maintain

### Tool Ranking for Claude Code

| Rank | Tool | Score | Rationale |
|------|------|-------|-----------|
| 1 | **Marp** | 9.5/10 | Pure Markdown, full CLI, all export formats, simple CSS themes, headless, Docker |
| 2 | **Slidev** | 7/10 | Powerful but Vue dependency, complex syntax, heavy setup |
| 3 | **Pandoc+Beamer** | 6.5/10 | Pure Markdown, academic output, LaTeX install too heavy |
| 4 | **reveal-md** | 6/10 | Good CLI but no PPTX, PDF needs Puppeteer, aging |
| 5 | **Spectacle** | 4/10 | JSX required, React overhead, separate PDF tool |
| 6 | **Remark.js** | 3/10 | No CLI, no export, browser-only |

### Why Marp Wins for Claude Code

1. **Pure Markdown** — Claude generates standard Markdown. No Vue components, no JSX, no LaTeX. The `---` slide separator is trivial.

2. **One command, any format**:
   ```bash
   marp slides.md --pdf          # Done
   marp slides.md --pptx         # Done
   marp slides.md --images png   # Done
   ```

3. **Directives are just YAML/comments** — Claude handles YAML front matter natively. HTML comments for per-slide directives are standard.

4. **Theme = single CSS file** — Claude can generate/modify CSS easily. No build step for themes.

5. **Headless by default** — Chrome/Firefox headless mode. Docker available for CI/CD. No X11 or display server needed.

6. **Fast** — Single file in, single file out. Parallel batch conversion. No dev server startup required for export.

7. **Small dependency footprint** — Just the npm package + a browser. No Vue, no React, no LaTeX.

### Existing Claude Code Integrations

- **claude-office-skills** (GitHub: tfriedel/claude-office-skills) — PPTX creation via HTML→PPTX conversion and template manipulation. Good for template-based corporate decks but more complex than Marp.
- **Slidev Claude Code Skill** — exists on MCP market, generates Slidev presentations. More developer-focused.
- **Native Claude PPTX skill** — Claude.ai and API have built-in PowerPoint creation as of Feb 2026. Works with templates but requires the Claude platform, not standalone CLI.

**Marp has no existing Claude Code skill** — opportunity to create a best-in-class one.

---

## Skill Design Recommendations

### Proposed Slash Commands

#### `/presentation create <topic> [--slides N] [--style corporate|technical|minimal]`

Generates a complete Marp Markdown file.

**Flow**:
1. Claude generates `slides.md` with front matter, themed slides, speaker notes
2. Auto-exports to PDF for preview
3. Returns file path + opens preview if available

**Example**:
```
/presentation create "Q4 2025 Engineering Review" --slides 12 --style corporate
```

**What Claude generates**:
```markdown
---
marp: true
theme: pf-corporate
paginate: true
header: "PropertyFinder Engineering"
footer: "Q4 2025 Review — Confidential"
---

<!-- _class: lead -->

# Q4 2025 Engineering Review

**PropertyFinder Engineering**
February 2026

---

# Agenda

1. Key Metrics
2. Team Achievements
3. Technical Debt Progress
4. Q1 2026 Priorities

---
...
```

#### `/presentation theme <name|create> [--base default|gaia|uncover]`

Apply or create a theme.

**Flow**:
1. `apply`: Switch theme directive in existing slides
2. `create`: Generate a new CSS theme file, register in config
3. `list`: Show available themes

**Example**:
```
/presentation theme create --base gaia
→ Claude creates themes/pf-corporate.css with PF brand colors
→ Updates marp.config.mjs themeSet
```

#### `/presentation export <format> [--file slides.md]`

Export to any format.

**Flow**:
```bash
marp slides.md --pdf -o output/deck.pdf
marp slides.md --pptx -o output/deck.pptx
marp slides.md --images png -o output/
marp slides.md -o output/deck.html
```

**Formats**: `pdf`, `pptx`, `html`, `png`, `jpeg`, `notes`

#### `/presentation edit <instruction>`

Modify existing slides based on natural language.

**Examples**:
```
/presentation edit "Add a slide about database migration after slide 3"
/presentation edit "Make the title slide dark with white text"
/presentation edit "Add speaker notes to all slides"
/presentation edit "Replace the agenda with: Intro, Demo, Q&A"
```

**Flow**: Claude reads the current `slides.md`, applies changes, re-exports preview.

#### `/presentation preview`

Quick preview cycle.

**Flow**:
1. Export to HTML
2. If VS Code available: open in Marp preview
3. If headless: export PNG of first slide as thumbnail
4. Show slide count + any rendering warnings

#### `/presentation import <source>`

Convert content into a presentation.

**Sources**:
- Text/notes file → structured slides
- URL → fetch content, summarize into slides
- Outline/bullet points → expanded slides with speaker notes
- Existing PPTX → extract text, recreate in Marp

### Project Structure for Presentations

```
presentations/
├── marp.config.mjs           # Shared config
├── themes/
│   ├── pf-corporate.css      # Corporate theme
│   ├── pf-technical.css      # Technical/dark theme
│   └── pf-minimal.css        # Clean minimal theme
├── assets/
│   ├── logo.svg              # Company logo
│   └── bg-pattern.png        # Background pattern
├── templates/
│   ├── quarterly-review.md   # Template: quarterly review
│   ├── technical-proposal.md # Template: tech proposal
│   ├── team-update.md        # Template: team update
│   └── demo-deck.md          # Template: product demo
└── output/                   # Generated files (gitignored)
    ├── *.pdf
    ├── *.pptx
    └── *.html
```

### Non-Technical User Experience

For users who don't know Markdown or CSS:

1. **Natural language all the way** — user says "make me a presentation about X", Claude handles all Markdown generation
2. **Template-first** — pre-built templates for common scenarios (review, proposal, update)
3. **Style presets** — `corporate`, `technical`, `minimal`, `dark` — mapped to theme CSS files
4. **Edit by description** — "make slide 3 darker", "add my name to the footer", "bigger font on the title"
5. **Preview thumbnails** — export PNG of each slide, display as grid
6. **One-command delivery** — `/presentation export pdf` → file ready to share

### Implementation Requirements

```bash
# One-time setup
npm install -g @marp-team/marp-cli
mkdir -p presentations/{themes,assets,templates,output}

# Verify
marp --version  # Should show 4.x
```

**Browser requirement**: Chrome, Edge, or Firefox must be installed for PDF/PPTX/image export. On headless servers, use the Docker image:

```bash
docker run --rm -v $(pwd):/home/marp/app marpteam/marp-cli slides.md --pdf
```

---

## Sources

### Marp
- [Marp Official Site](https://marp.app/)
- [Marp CLI GitHub](https://github.com/marp-team/marp-cli) — v4.2.3
- [Marp CLI npm](https://www.npmjs.com/package/@marp-team/marp-cli)
- [Marpit Directives](https://marpit.marp.app/directives)
- [Marpit Theme CSS](https://marpit.marp.app/theme-css)
- [Marp VS Code Extension](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode)
- [Marp CLI v4 Discussion](https://github.com/orgs/marp-team/discussions/542)
- [Marp Core Themes](https://github.com/marp-team/marp-core/blob/main/themes/README.md)
- [Marp Directives Guide](https://github.com/marp-team/marp/blob/main/website/docs/guide/directives.md)
- [Marp Community Themes](https://rnd195.github.io/marp-community-themes/)
- [Marp Template Library](https://yoanbernabeu.github.io/MARP-Template-Library/docs/intro/)
- [Marp DeepWiki — Theming](https://deepwiki.com/marp-team/marp/3.4-theming-and-styling)

### Slidev
- [Slidev Official Site](https://sli.dev/)
- [Slidev GitHub](https://github.com/slidevjs/slidev) — v52.12.0, ~34k stars
- [Slidev CLI npm](https://www.npmjs.com/package/@slidev/cli)
- [Slidev Exporting Guide](https://sli.dev/guide/exporting.html)
- [Slidev Why](https://sli.dev/guide/why)

### reveal.js
- [reveal.js Official Site](https://revealjs.com/)
- [reveal.js GitHub](https://github.com/hakimel/reveal.js/) — v5.2.1, ~68k stars
- [reveal-md npm](https://www.npmjs.com/package/reveal-md) — v6.1.4
- [reveal.js PDF Export](https://revealjs.com/pdf-export/)

### Other Tools
- [Remark.js](https://remarkjs.com/) — [GitHub](https://github.com/gnab/remark)
- [Spectacle](https://nearform.com/open-source/spectacle/) — [npm](https://www.npmjs.com/package/spectacle) v10.2.3
- [MDX Deck GitHub](https://github.com/jxnblk/mdx-deck) — unmaintained
- [Pandoc Demos](https://pandoc.org/demos.html)
- [MkSlides GitHub](https://github.com/MartenBE/mkslides)
- [Deckset](https://www.deckset.com/) — macOS only

### Claude Code Integration
- [Claude Office Skills](https://github.com/tfriedel/claude-office-skills)
- [Slide Libraries Comparison](https://tonai.github.io/blog/posts/slide-libraries/)
- [Markdown Presentation Tools List](https://gist.github.com/johnloy/27dd124ad40e210e91c70dd1c24ac8c8)
