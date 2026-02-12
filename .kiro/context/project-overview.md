# Project Overview

## Purpose
Personal technical blog by Davide de Paolis, Engineering Manager based in Hamburg, Germany. The blog covers software engineering topics including AWS, serverless architecture, Node.js, React, leadership, and personal development.

## History
- Started: December 2020
- Migrated from DEV.to for better content control
- Last updated: April 2021 (needs revival)
- 9 published posts covering technical tutorials and career advice

## Architecture

### High-Level Design
Static site generated with Jekyll, hosted on GitHub Pages. Content written in Markdown, automatically built and deployed when pushed to gh-pages branch.

```
Author writes Markdown → Git push → GitHub Pages builds → Static site served
```

### Technology Stack
- **Generator**: Jekyll 3.10.0
- **Hosting**: GitHub Pages (free, CDN-backed)
- **Theme**: StartBootstrap Clean Blog (remote theme)
- **Language**: Ruby 2.6+, Markdown
- **Build**: Bundler 2.2.3
- **Version Control**: Git (gh-pages branch only)

### Key Components

**Content Layer**:
- `_posts/`: Blog posts in Markdown with YAML front matter
- `assets/`: Post images and backgrounds
- Front matter: layout, title, description, tags, background

**Configuration Layer**:
- `_config.yml`: Site metadata, theme, plugins
- `Gemfile`: Ruby dependencies
- `.gitignore`: Build artifacts exclusion

**Presentation Layer**:
- Remote theme (no local theme files)
- Liquid templating for dynamic content
- Responsive Bootstrap-based design

**Pages**:
- `index.md`: Home page (post list)
- `about.md`: Author bio
- `posts.md`: Posts organized by tags
- `contact.md`: Contact information
- `404.html`: Error page

## Dependencies

### Critical Dependencies
- `github-pages` gem: Bundles all Jekyll dependencies
- `jekyll-feed`: RSS feed generation
- `kramdown-parser-gfm`: GitHub-flavored Markdown

### Current Status
⚠️ **OUTDATED**: Using github-pages ~> 209 (from 2020)
✅ **Latest**: github-pages 232 (as of Feb 2026)

## Deployment Architecture

### Build Process
1. Author commits to gh-pages branch
2. GitHub detects push
3. GitHub Pages runs Jekyll build
4. Static HTML generated
5. Deployed to GitHub's CDN
6. Available at https://dvddpl.github.io

### No CI/CD Pipeline
- No GitHub Actions
- No automated tests
- No preview deployments
- Direct push to production

## Content Strategy

### Topics Covered
- AWS and serverless architecture
- Node.js and JavaScript
- React and React Admin
- Database optimization (MySQL, Sequelize)
- Software engineering practices
- Leadership and career development
- Personal growth and productivity

### Post Structure
- Technical tutorials with code examples
- Problem-solving chronicles (debugging stories)
- Career advice and reflections
- Book reviews
- Personal retrospectives

### Audience
- Software engineers
- Technical leads
- Developers learning AWS/serverless
- Career-focused developers


## Future Vision
- Add search and comments
- Implement proper git workflow
- Optimize for performance and SEO
