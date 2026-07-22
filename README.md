# Joseph Lansley — personal academic site

A single-page Hugo site in the spirit of the Matteo Courthoud guide, built
from scratch (no external theme dependency, so nothing to break when
upstream themes update). Sections: About, Research (with a Job Market
Paper spotlight), CV, Teaching, Contact.

## 1. Put it on GitHub Pages

1. Create a GitHub repo named exactly `josephlansley.github.io`
   (this exact name is what makes GitHub auto-publish it — if your
   GitHub username differs, rename the repo to `<your-username>.github.io`
   and update `baseURL` in `hugo.toml` to match).
2. Push this whole folder to that repo.
3. In the repo, go to **Settings → Pages**, and either:
   - use a GitHub Action to build with Hugo on every push (recommended,
     see below), or
   - build locally with `hugo` and push the generated `public/` folder's
     contents to the repo instead of the source files.

**Recommended: GitHub Action (auto-builds on every push)**
Create `.github/workflows/hugo.yml` in the repo with:

```yaml
name: Deploy Hugo site
on:
  push:
    branches: ["main"]
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: "0.134.3"
          extended: true
      - run: hugo --minify
      - uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

Then in **Settings → Pages**, set Source to "GitHub Actions". Push, wait
a minute, and the site is live at `https://josephlansley.github.io/`.

## 2. Edit content (no code required)

Everything you'll update regularly lives in plain YAML/TOML files:

- **`hugo.toml`** — your name, tagline, email, links to Scholar/GitHub/
  LinkedIn, and whether the job-market-paper banner shows (`onjobmarket`).
- **`data/papers.yaml`** — your job market paper, working papers, and
  publications. Add a new working paper by copying one list entry and
  editing the fields; order = display order.
- **`data/education.yaml`** — degrees, in display order.
- **`data/teaching.yaml`** — courses taught.
- **`layouts/partials/section-about.html`** — the one paragraph of prose
  bio; edit the sentence directly (it's plain HTML with your name/school
  already filled in from config).

## 3. Add your real files

- Headshot: drop a photo into `static/img/`, then in
  `layouts/partials/section-about.html` replace the placeholder `<span>`
  with `<img src="/img/your-photo.jpg" alt="Joseph Lansley">`.
- CV and papers: put PDFs in `static/files/` and `static/files/papers/`,
  matching the paths already referenced in `hugo.toml` and
  `data/papers.yaml` (or change the paths to match your filenames).

## 4. Preview locally before pushing

Install Hugo (extended version), then from this folder run:

```
hugo server
```

and open the printed `localhost` URL. Changes to content/data files
reload automatically.

## Design notes

Palette is a warm paper background with charcoal text, a deep burgundy
accent for the job-market-paper and primary links, and a muted gold for
eyebrow labels — a working-paper-cover-page feel rather than a generic
SaaS-landing-page look. Headings are set in Source Serif 4, body in IBM
Plex Sans, and metadata (dates, JEL codes, tags) in IBM Plex Mono to
visually separate "paper" content from prose, the way an actual working
paper does.
