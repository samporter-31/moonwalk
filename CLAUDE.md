# CLAUDE.md - Moonwalk Jekyll Theme

This file provides guidance for AI assistants working in this repository.

## Project Overview

Moonwalk is a fast, minimalist Jekyll blog theme with clean dark/light mode support. It is distributed as both a Ruby gem and a GitHub remote theme. The codebase is a standard Jekyll site with no JavaScript build tooling or test framework.

- **Live demo**: https://abhinavs.github.io/moonwalk/
- **Version**: 0.1.1 (see `moonwalk.gemspec`)
- **License**: MIT

## Repository Structure

```
moonwalk/
├── _config.yml          # Main Jekyll + theme configuration
├── _data/
│   └── home.yml         # Navbar, footer, portfolio, and list data
├── _includes/           # Reusable Liquid template partials
│   ├── card_list.html
│   ├── custom_head.html
│   ├── date_and_social_share.html
│   ├── head.html
│   ├── horizontal_list.html
│   ├── post_list.html
│   ├── toggle_theme_button.html
│   ├── toggle_theme_js.html
│   └── vertical_list.html
├── _layouts/            # Page layout templates
│   ├── blog.html        # Blog listing page
│   ├── default.html     # Base layout (theme toggle, footer)
│   ├── home.html        # Landing page (navbar, portfolio, blog)
│   └── post.html        # Individual post/article
├── _posts/              # Markdown blog posts (sample content)
├── _sass/               # SCSS stylesheets
│   ├── moonwalk.scss    # Main styles, CSS variables, dark mode
│   ├── list.scss        # Card, horizontal, vertical list styles
│   └── syntax.scss      # Rouge syntax highlighting styles
├── assets/css/
│   └── main.scss        # SCSS entry point (imports _sass files)
├── bin/
│   ├── bootstrap        # Install Ruby dependencies
│   └── start            # Start development server
├── moonwalk.gemspec     # Ruby gem specification + runtime deps
├── Gemfile              # Points to gemspec
├── _config.yml          # Site configuration
├── index.md             # Home page (uses `home` or `blog` layout)
├── about.md             # About page (uses `post` layout)
├── blog.html            # Blog index page (uses `blog` layout)
└── 404.html             # Error page
```

## Development Setup

### Prerequisites

- Ruby (3.0.x recommended; avoid 3.1.x on Windows - see `moonwalk_on_windows.md`)
- Bundler gem

### Install and run

```bash
bin/bootstrap       # Runs: bundle install
bin/start           # Runs: bundle exec jekyll serve
# Site available at http://127.0.0.1:4000
```

### Manual commands

```bash
bundle exec jekyll serve    # Development server with live reload
bundle exec jekyll build    # Build static site to _site/
```

There is no test suite, linter, or CI pipeline. Verification is done by running the dev server and visually inspecting the output in the browser.

## Key Dependencies

Defined in `moonwalk.gemspec`:

| Gem | Version | Purpose |
|-----|---------|---------|
| `jekyll` | ~> 4.2.0 | Static site generator |
| `jekyll-feed` | ~> 0.15.0 | RSS feed at `/feed.xml` |
| `jekyll-soopr-seo-tag` | ~> 2.7.3 | SEO meta tags (drop-in replacement for `jekyll-seo-tag`) |
| `rouge` | ~> 3.23.0 | Code syntax highlighting |
| `webrick` | ~> 1.7 | Dev server (required for Ruby 3+) |

No JavaScript package manager (npm/yarn) is used. There are no frontend build tools. The only JS is inline Liquid-rendered vanilla JS for the theme toggle (`_includes/toggle_theme_js.html`).

## Layouts

Three main layouts, all extending `default.html`:

- **`home`** - Full landing page. Shows navbar, project cards, blog post list, footer. Controlled by `_config.yml` `theme_config` flags.
- **`blog`** - Blog listing page. Shows all posts in reverse chronological order.
- **`post`** - Individual post/page. Adds a "back home" link and date/social share bar.

`index.md` can use either `home` or `blog` layout depending on the desired entry point.

## Configuration

### `_config.yml` — Primary site configuration

```yaml
theme_config:
  appearance: "dark"          # "light", "dark", or "auto"
  appearance_toggle: true     # show/hide the light/dark toggle button
  back_home_text: "home.."   # text for the back link in post layout
  date_format: "%Y-%m-%d"    # strftime format for post dates
  show_description: true
  show_navbar: true
  show_footer: true
  show_copyright: true
  show_projects: true         # card list from project_entries in home.yml
  show_blog: true             # post list from _posts/
  show_old_projects: true     # card list from old_project_entries in home.yml
  show_misc_list: false       # vertical list from misc_entries in home.yml
  home:
    title_projects: Portfolio
    title_misc_list: Details
    title_blog: Blog
    title_old_projects: Old Projects
```

### `_data/home.yml` — Navigation and content data

Controls all navigation and home page content sections. Supports four list types:

**`navbar_entries` / `footer_entries`** — Horizontal lists (navbar, footer):
```yaml
navbar_entries:
  - title: about
    url: about           # relative or absolute URL
```

**`project_entries` / `old_project_entries`** — Card lists (portfolio):
```yaml
project_entries:
  - title: My Project
    url: https://example.com
    desc: Short description shown in card body
    highlight: NEW       # optional badge text (keep short)
```

**`misc_entries`** — Vertical list (generic, supports nesting and post list):
```yaml
misc_entries:
  - title: Blog posts
    post_list: true      # expands to all posts as sub-entries
    url: false
  - title: Nested section
    url: false
    entries:             # recursive nesting supported
      - title: Sub-item
        url: https://example.com
```

## Styling and Theming

### CSS architecture

- `_sass/moonwalk.scss` — Core styles: fonts, layout, typography, dark/light variables
- `_sass/list.scss` — List component styles (cards, horizontal nav, vertical)
- `_sass/syntax.scss` — Rouge syntax highlighting
- `assets/css/main.scss` — Entry point that imports all partials

### Dark/light mode

Theme uses CSS custom properties (`--bg`, `--text`, `--links`, etc.) with a `@mixin dark-appearance`. To customize colors, override these variables in `_sass/moonwalk.scss`:

```css
/* Light mode (html root) */
html {
  --bg: #fff;
  --bg-secondary: #f8f9fa;
  --headings: #000;
  --text: #333;
  --links: blue;
  --highlight: #ffecb2;
}

/* Dark mode (via mixin) */
@mixin dark-appearance {
  html, body {
    --bg: #1f242A;
    --bg-secondary: #323945;
    --headings: #3D9970;
    --text: #adb5bd;
    --links: #91a7ff;
    --highlight: #ffd43b;
  }
}
```

User preference is persisted via `localStorage` and applied before first paint to avoid flash. The toggle button is rendered by `_includes/toggle_theme_button.html` (SVG icons) and driven by inline JS in `_includes/toggle_theme_js.html`.

### Fonts

- Body text: **Inter** (400, 700) — loaded from Google Fonts
- Code/monospace: **Anonymous Pro** — loaded from Google Fonts
- Max content width: 720px (`.w` wrapper class)
- Mobile breakpoint: 600px

## Blog Posts

Posts live in `_posts/` and follow Jekyll's standard filename convention: `YYYY-MM-DD-slug.md`.

Each post front matter should include:
```yaml
---
layout: post
title: "Post Title"
---
```

Optional front matter fields recognized by the theme:
- `description` — Used in SEO meta tags
- `image` — Used for Twitter card and social share image

Permalink format is set to `/:slug` in `_config.yml`, meaning only the slug (filename without date and extension) is used in the URL.

## Gem vs Remote Theme Modes

The theme can be used in two ways:

**As a Ruby gem** (local development):
```yaml
# _config.yml
theme: moonwalk
```

**As a GitHub remote theme** (GitHub Pages compatible):
```yaml
# _config.yml
remote_theme: abhinavs/moonwalk
```

For GitHub Pages, also replace `jekyll-soopr-seo-tag` with `jekyll-seo-tag` in the plugins list (see `github_pages.md`).

## Deployment

- **Netlify / Vercel**: Push to any branch; Jekyll is auto-detected.
- **GitHub Pages**: Use `remote_theme` mode (see `github_pages.md`).
- **Self-hosted**: Run `bundle exec jekyll build` and serve the `_site/` directory.

The `robots.txt` file at root is served as-is by Jekyll.

## Gem Packaging

When releasing a new gem version, only files tracked by git in these directories are bundled (as defined in `moonwalk.gemspec`):
- `_layouts/`
- `_includes/`
- `_sass/`
- `assets/`
- `LICENSE.txt`
- `README.md`
- `_config.yml`

To release: bump the version in `moonwalk.gemspec`, then run `gem build moonwalk.gemspec` and publish via `gem push`.

## Key Conventions

- **No JavaScript framework** — all interactivity is plain vanilla JS embedded in Liquid includes.
- **No build pipeline** — SCSS is compiled directly by Jekyll (via `sass-embedded` or `sassc`). Do not introduce webpack, Vite, or similar.
- **Liquid templating** — layouts and includes use Jekyll's Liquid syntax. Keep logic minimal in templates; prefer data-driven approaches via `_data/`.
- **Minimal CSS** — the theme's selling point is its small footprint. Avoid adding heavy dependencies.
- **Data-driven navigation** — do not hardcode nav/footer links in HTML; use `_data/home.yml`.
- **Theme toggle must work without JS** — the default appearance from `_config.yml` must render correctly even if JavaScript is disabled. JS only enhances with toggle capability.
- **Permalink slug format** — the `/:slug` permalink format strips dates from URLs. Filenames in `_posts/` should use descriptive slugs.
- **CSS variables for theming** — all theme colors must use the CSS custom property variables; do not use hardcoded hex values in component styles.

## Optional Integrations

### Soopr (social share + like buttons)

Sign up at https://www.soopr.co, get a `publish_token`, and add it to `_config.yml`:
```yaml
soopr:
  publish_token: "your-token-here"
```

This enables share buttons, like buttons, and auto-generated social share images on posts.

Without a token, the `date_and_social_share.html` include renders only the post date and author.
