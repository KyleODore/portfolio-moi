# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is a Jekyll-powered portfolio website for Kyle O'Dore using the Chirpy theme (v7.3). The site is deployed to GitHub Pages at `https://kyleodore.github.io/portfolio-moi` with automated CI/CD.

## Architecture

**Jekyll + Chirpy Theme Structure:**
- Uses gem-based Chirpy theme installation for maintainability
- Configuration-driven customization through `_config.yml`
- Content organized in `_posts/` (project articles) and `_tabs/` (site pages)
- Assets in `assets/img/` with portraits, project images, and favicons
- PWA enabled with offline caching capabilities

**Key Configuration:**
- Base URL: `/portfolio-moi` for GitHub Pages
- Avatar: `/assets/img/portraits/portrait.jpg`
- Personal info: GitHub username `KyleODore`, email `kyleodore@gmail.com`
- Theme features: Dark/light mode, search, responsive design

## Development Commands

**Local Development:**
```bash
# Start development server
bash tools/run.sh

# With custom host
bash tools/run.sh -H 0.0.0.0

# Production mode
bash tools/run.sh --production
```

**Build and Test:**
```bash
# Build and validate site
bash tools/test.sh

# Manual commands
bundle install                    # Install dependencies
bundle exec jekyll serve         # Development server
bundle exec jekyll build         # Production build
```

**Dependencies:**
- Ruby with Bundler
- Jekyll with Chirpy theme ~7.3
- html-proofer for link validation

## Content Management

**Adding Projects/Posts:**
- Create markdown files in `_posts/` with format: `YYYY-MM-DD-title.md`
- Include frontmatter with title, date, categories, and tags
- Current categories: `[Projects, Academic]`, `[Projects, Enterprise Software]`, `[Projects, Web Development]`

**Site Navigation:**
- Pages defined in `_tabs/` with order and icon frontmatter
- Contact info configured in `_data/contact.yml`
- Social sharing options in `_data/share.yml`

## Deployment

**Automated via GitHub Actions:**
- Triggers on push to main branch
- Builds with Jekyll production environment
- Validates links with html-proofer
- Deploys to GitHub Pages automatically
- Testing includes link validation and accessibility checks

**Manual Deployment:**
Deployment is fully automated; manual intervention should not be necessary.

## Asset Organization

**Images:**
- **Portraits:** Multiple sizes in `assets/img/portraits/` (portrait.jpg, portrait-small.jpg, etc.)
- **Projects:** Logos and screenshots in `assets/img/projects/`
- **Favicons:** Complete set in `assets/img/favicons/` including PWA icons

**Performance:**
- Sass compilation and compression enabled
- HTML minification in production builds
- PWA caching for offline functionality

## Common Tasks

**Adding New Project:**
1. Add project images to `assets/img/projects/`
2. Create post in `_posts/` with project details
3. Use existing post structure and frontmatter format

**Updating Personal Info:**
- Main config: `_config.yml` (title, tagline, social links)
- About page: `_tabs/about.md`
- Contact methods: `_data/contact.yml`

**Theme Updates:**
- Update gem version in `Gemfile`
- Run `bundle update jekyll-theme-chirpy`
- Test thoroughly as theme updates may affect customizations