# Embedded Systems with the MSP430G2553

This repository contains the source for an introductory embedded systems
textbook built around the Texas Instruments MSP430G2553 microcontroller
and the MSP-EXP430G2ET LaunchPad development board.

The book is written as a [Quarto](https://quarto.org) project and lives in
[`book/`](book/). It can be rendered to a searchable HTML site and/or a PDF.

The original Microsoft Word drafts (and their diagram source `.pptx` files)
that this Quarto book was adapted from are preserved for reference under
[`Archive/TI-MSP430G2553/`](Archive/TI-MSP430G2553/). They are not needed to
build the book and should not be edited going forward &mdash; new/updated
chapter content should be written directly in the `.qmd` files under
[`book/chapters/`](book/chapters/).

## Repository layout

```
book/                       Quarto project (the book itself)
  _quarto.yml                Book configuration (title, chapter list, output formats)
  index.qmd                  Preface / front matter
  chapters/                  One .qmd file per chapter
  images/                    Figures, organized per-chapter (images/ch01, images/ch02, ...)
scripts/
  convert_docx_to_qmd.py     Re-usable script that converts an Archive/ .docx
                              chapter draft into a Quarto .qmd chapter + images.
                              Useful when a new chapter (e.g. ADC, Serial
                              Communication) is drafted in Word and needs to
                              be brought into the Quarto book.
Archive/TI-MSP430G2553/      Original Word (.docx/.pdf) drafts and .pptx
                              diagram sources, kept for historical reference.
```

## Installing the required tools

You only need **Quarto** (which bundles Pandoc) to build the book. Two
additional tools are only needed if you want to build a PDF, or re-run the
docx&rarr;qmd conversion script for new chapters.

### 1. Quarto (required)

Quarto is the tool that renders the `.qmd` files into HTML/PDF and manages
the overall book build.

- **All platforms:** download an installer from
  <https://quarto.org/docs/get-started/> and follow the platform instructions.
- **Linux (no root/sudo needed):** download the portable tarball and put it
  on your `PATH`:

  ```bash
  curl -sL https://github.com/quarto-dev/quarto-cli/releases/download/v1.10.18/quarto-1.10.18-linux-amd64.tar.gz -o quarto.tar.gz
  tar xzf quarto.tar.gz -C ~/tools    # or any directory you like
  export PATH="$HOME/tools/quarto-1.10.18/bin:$PATH"   # add to ~/.bashrc to persist
  ```
- **macOS (Homebrew):** `brew install --cask quarto`
- **Windows (winget):** `winget install --id Posit.Quarto`

Verify the install:

```bash
quarto check
```

### 2. A PDF engine (optional, only needed for `quarto render --to pdf`)

Quarto's PDF output goes through LaTeX. The easiest way to get a working
LaTeX install is Quarto's bundled TinyTeX:

```bash
quarto install tinytex
```

### 3. Pandoc + LibreOffice (optional, only needed to re-run the docx&rarr;qmd conversion script)

These are **not** required to build the book from the already-converted
`.qmd` files &mdash; only if you're converting a *new* or *updated* chapter
draft from `Archive/TI-MSP430G2553/*.docx` using
`scripts/convert_docx_to_qmd.py`.

- **Pandoc** does the actual docx &rarr; Markdown conversion. Quarto bundles
  its own copy internally, but the conversion script calls the `pandoc`
  command directly, so it must also be on your `PATH`:
  - Debian/Ubuntu: `sudo apt install pandoc`
  - macOS: `brew install pandoc`
  - Or download a portable build from <https://github.com/jgm/pandoc/releases>
- **LibreOffice** (`soffice` on `PATH`) is used only to rasterize legacy
  vector images (`.wmf` / `.emf`) that Word documents sometimes embed, since
  browsers and PDF engines can't display those formats directly:
  - Debian/Ubuntu: `sudo apt install libreoffice`
  - macOS: `brew install --cask libreoffice`

## Building the book

From the `book/` directory:

```bash
cd book

# Render to HTML (output goes to book/_book/)
quarto render --to html

# Render to PDF (requires a LaTeX install, see above)
quarto render --to pdf

# Render everything configured in _quarto.yml (html + pdf)
quarto render
```

To preview the book locally with live-reload while editing:

```bash
cd book
quarto preview
```

This starts a local web server and opens the book in your browser,
automatically re-rendering pages as you edit `.qmd` files.

Rendered output is written to `book/_book/` and is not checked into version
control.

## Editing content

- Each chapter is a single `.qmd` file in `book/chapters/`, named
  `NN-slug.qmd` (e.g. `03-gpio.qmd`). Chapter order and grouping into parts
  is controlled by the `book.chapters` list in `book/_quarto.yml`.
- Chapters use plain Markdown, with a YAML `title:` front matter block. See
  the [Quarto Markdown Basics guide](https://quarto.org/docs/authoring/markdown-basics.html)
  for formatting help.
- Figures live under `book/images/chNN/` and are referenced with a Quarto
  figure ID for automatic numbering and cross-referencing, e.g.:

  ```markdown
  ![Caption text](images/ch03/image30.png){#fig-ch03-3-2}
  ```

  You can cross-reference a figure elsewhere in the same chapter with
  `@fig-ch03-3-2`, which Quarto renders as an auto-numbered, clickable
  reference (e.g. "Figure 3.2").
- Code listings use fenced code blocks (` ```c ... ``` `) for syntax
  highlighting.
- Chapters 6 (ADC) and 7 (Serial Communication) are currently placeholders
  (`book/chapters/06-adc.qmd`, `book/chapters/07-serial-communication.qmd`)
  since those chapters have not been drafted yet.

### Converting a new/updated chapter from Word

If a chapter is drafted or revised in Word under `Archive/TI-MSP430G2553/`,
regenerate its `.qmd` file and images with:

```bash
python3 scripts/convert_docx_to_qmd.py        # convert all configured chapters
python3 scripts/convert_docx_to_qmd.py 6      # convert only chapter 6
```

This requires `pandoc` (and `soffice`, if the chapter has `.wmf`/`.emf`
images) to be on your `PATH` &mdash; see the installation section above. The
script re-generates the target `.qmd` file and its `book/images/chNN/`
folder from scratch each time it's run, so any manual edits made directly to
a generated `.qmd` file will be lost if you re-run the conversion for that
chapter. To add a newly-drafted chapter, add its details to the `CHAPTERS`
list at the top of `scripts/convert_docx_to_qmd.py`.
