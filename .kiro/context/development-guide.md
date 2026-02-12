# Development Guide

## Prerequisites

### Required Software
- **Ruby**: 3.3+ (GitHub Pages uses Ruby 3.3.4, system has 3.3.10)
- **Bundler**: Latest version (4.0.6+)
- **Git**: For version control

### Installation

#### macOS (Current System)
```bash
# Install Ruby 3.3 via Homebrew
brew install ruby@3.3

# Add to PATH (temporary for current session)
export PATH="/opt/homebrew/opt/ruby@3.3/bin:$PATH"
export PATH="/opt/homebrew/lib/ruby/gems/3.3.0/bin:$PATH"

# Or add to ~/.zshrc for permanent setup
echo 'export PATH="/opt/homebrew/opt/ruby@3.3/bin:$PATH"' >> ~/.zshrc
echo 'export PATH="/opt/homebrew/lib/ruby/gems/3.3.0/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Verify Ruby version
ruby --version  # Should show 3.3.10 or similar

# Install bundler
gem install bundler

# Verify bundler installation
bundle --version  # Should show 4.0.6 or higher
```

#### Why Ruby 3.3?
- GitHub Pages uses Ruby 3.3.4 in production
- Ruby 2.6 (system default) is too old for latest github-pages gem (232)
- Ruby 3.3 ensures local development matches production environment

## Setup

### First Time Setup
```bash
# 1. Clone repository
git clone https://github.com/dvddpl/dvddpl.github.io.git
cd dvddpl.github.io

# 2. Ensure Ruby 3.3 is in PATH
export PATH="/opt/homebrew/opt/ruby@3.3/bin:$PATH"
export PATH="/opt/homebrew/lib/ruby/gems/3.3.0/bin:$PATH"

# 3. Install dependencies
bundle install

# 4. Run local server
bundle exec jekyll serve

# 5. Open browser
open http://localhost:4000
```

### Troubleshooting Setup

#### Ruby Version Too Old
```bash
# Error: Gem requires Ruby version >= 3.0
# Solution: Install Ruby 3.3 via Homebrew
brew install ruby@3.3
# Then add to PATH as shown above
```

#### Bundler Not Found
```bash
# Error: command not found: bundle
# Solution: Ensure Ruby 3.3 bin directory is in PATH
export PATH="/opt/homebrew/opt/ruby@3.3/bin:$PATH"
export PATH="/opt/homebrew/lib/ruby/gems/3.3.0/bin:$PATH"
gem install bundler
```

#### Permission Errors
```bash
# If you get permission errors:
gem install bundler:2.2.3 --user-install
```

#### Port Already in Use
```bash
# If port 4000 is busy:
bundle exec jekyll serve --port 4001
```

## Running Locally

### Development Server
```bash
# Standard server (with auto-rebuild)
bundle exec jekyll serve

# With drafts visible
bundle exec jekyll serve --drafts

# With future posts visible
bundle exec jekyll serve --future

# Incremental build (faster)
bundle exec jekyll serve --incremental

# Specific port
bundle exec jekyll serve --port 4001

# Verbose output for debugging
bundle exec jekyll serve --verbose
```

### Build Only (No Server)
```bash
# Build site to _site/ directory
bundle exec jekyll build

# Build with drafts
bundle exec jekyll build --drafts

# Clean build (remove _site first)
bundle exec jekyll clean
bundle exec jekyll build
```

## Writing Posts

### Creating a New Post

#### 1. Create File
```bash
# File naming convention: YYYY-MM-DD-title-with-dashes.md
touch _posts/2026-02-11-my-new-post.md
```

#### 2. Add Front Matter
```yaml
---
layout: post
title: "Your Post Title"
description: "Brief description for SEO and previews"
tags: javascript nodejs aws serverless
background: /assets/your-image.jpg
---
```

#### 3. Write Content
```markdown
Your post content here in Markdown format.

## Headings

Use ## for main sections (# is reserved for title).

### Code Examples

```javascript
const example = "Use triple backticks for code blocks";
```

### Images

![Alt text](/assets/your-image.jpg)

### Links

[Link text](https://example.com)
```

#### 4. Add Images
```bash
# Copy images to assets directory
cp ~/Downloads/my-image.jpg assets/

# Reference in post
![Description](/assets/my-image.jpg)
```

### Front Matter Reference

#### Required Fields
```yaml
layout: post          # Always "post" for blog posts
title: "Post Title"   # Post title (use quotes if contains colons)
```

#### Recommended Fields
```yaml
description: "SEO description"  # For search engines and previews
tags: tag1 tag2 tag3           # Space-separated tags (no commas)
background: /assets/bg.jpg  # Header background image
```

#### Optional Fields
```yaml
published: true       # Set to false to hide post
date: 2026-02-11     # Override date from filename
```

### Post Templates

#### Technical Tutorial Template
```markdown
---
layout: post
title: "How to [Do Something]"
description: "Learn how to [accomplish task] with [technology]"
tags: tutorial javascript nodejs
background: /assets/tutorial-bg.jpg
---

## TL;DR

Quick summary of what you'll learn and the solution.

## The Problem

Describe the problem or challenge.

## The Solution

Step-by-step solution with code examples.

```javascript
// Code example
```

## Explanation

Detailed explanation of how it works.

## Conclusion

Summary and next steps.

---
Photo by [Author](link) on [Unsplash](link)
```

#### Career/Reflection Template
```markdown
---
layout: post
title: "Lessons Learned from [Experience]"
description: "Reflections on [topic]"
tags: career leadership personalgrowth
background: /assets/reflection-bg.jpg
---

Opening hook or story.

## The Context

Set the scene.

## What I Learned

Key lessons with examples.

## Takeaways

Actionable advice for readers.

---
Photo credit if applicable
```

## Testing

### Preview Before Publishing
```bash
# 1. Run local server
bundle exec jekyll serve

# 2. Open in browser
open http://localhost:4000

# 3. Check:
# - Post appears in list
# - Title and description correct
# - Images load properly
# - Code formatting correct
# - Links work
# - Mobile responsive
```

### Common Issues

#### Post Not Showing
- Check filename format: `YYYY-MM-DD-title.md`
- Check front matter has `layout: post`
- Check date isn't in future (unless using --future flag)
- Check `published: false` isn't set

#### Images Not Loading
- Check path starts with `/assets/`
- Check file exists in correct location
- Check filename matches exactly (case-sensitive)

#### Formatting Issues
- Check YAML front matter is valid (use YAML validator)
- Check Markdown syntax
- Check code blocks use triple backticks

## Deployment

### Publishing to GitHub Pages

#### Standard Workflow
```bash
# 1. Write and test post locally
bundle exec jekyll serve

# 2. Stage changes
git add _posts/2026-02-11-my-new-post.md
git add assets/new-image.jpg

# 3. Commit with descriptive message
git commit -m "Add post: My New Post"

# 4. Push to GitHub
git push origin gh-pages

# 5. Wait 1-2 minutes for GitHub Pages to build
# 6. Visit https://dvddpl.github.io to see live post
```

#### Better Workflow (Recommended)
```bash
# 1. Create feature branch
git checkout -b post/my-new-post

# 2. Write and commit post
git add _posts/2026-02-11-my-new-post.md
git commit -m "Add post: My New Post"

# 3. Push branch
git push origin post/my-new-post

# 4. Create Pull Request on GitHub
# 5. Review changes
# 6. Merge to gh-pages
# 7. Delete feature branch
```

### Deployment Checklist
- [ ] Post tested locally
- [ ] Images optimized and uploaded
- [ ] Links verified
- [ ] Front matter complete
- [ ] Tags appropriate
- [ ] Spelling/grammar checked
- [ ] Code examples tested
- [ ] Mobile responsive checked

## Updating Dependencies

### Check for Updates
```bash
# See outdated gems
bundle outdated

# Check GitHub Pages current versions
open https://pages.github.com/versions
```

### Update Process
```bash
# Update all gems
bundle update

# Update specific gem
bundle update jekyll-feed

# Update github-pages (updates all Jekyll dependencies)
bundle update github-pages

# Test after updating
bundle exec jekyll serve
```

### Major Version Updates
```bash
# 1. Update Gemfile
# Change: gem "github-pages", "~> 209"
# To:     gem "github-pages", "~> 232"

# 2. Update dependencies
bundle update github-pages

# 3. Test thoroughly
bundle exec jekyll serve

# 4. Check for breaking changes
# Read: https://github.com/github/pages-gem/releases

# 5. Commit Gemfile.lock
git add Gemfile Gemfile.lock
git commit -m "Update github-pages to v232"
```

## Common Tasks

### Add New Page
```bash
# 1. Create markdown file
touch my-page.md

# 2. Add front matter
---
layout: page
title: My Page
permalink: /my-page/
background: /assets/background_test.png
---

# 3. Add to navigation (edit theme or _config.yml)
```

### Change Site Settings
```bash
# Edit _config.yml
vim _config.yml

# Restart server to see changes
# (Ctrl+C to stop, then restart)
bundle exec jekyll serve
```

### Add New Tag
```bash
# Just use it in a post's front matter
tags: newtag existingtag

# It will automatically appear on /posts/ page
```

## Debugging

### Enable Verbose Logging
```bash
bundle exec jekyll serve --verbose
```

### Check Build Errors
```bash
# Build without serving
bundle exec jekyll build

# Check _site/ directory
ls -la _site/
```

### Validate Configuration
```bash
# Check _config.yml syntax
bundle exec jekyll doctor
```

### Clear Cache
```bash
# Remove generated files
bundle exec jekyll clean

# Rebuild from scratch
bundle exec jekyll build
```

## Performance Tips

### Faster Builds
```bash
# Use incremental builds (only rebuild changed files)
bundle exec jekyll serve --incremental

# Limit posts during development
bundle exec jekyll serve --limit_posts 5
```

### Optimize Images
```bash
# Before adding to assets/, optimize:
# - Resize to appropriate dimensions
# - Compress (use ImageOptim, TinyPNG, etc.)
# - Consider WebP format for modern browsers
```

## Resources

### Jekyll Documentation
- Official docs: https://jekyllrb.com/docs/
- GitHub Pages docs: https://docs.github.com/en/pages
- Liquid syntax: https://shopify.github.io/liquid/

### Theme Documentation
- Clean Blog theme: https://github.com/StartBootstrap/startbootstrap-clean-blog-jekyll

### Markdown Reference
- GitHub Flavored Markdown: https://guides.github.com/features/mastering-markdown/
- Kramdown syntax: https://kramdown.gettalong.org/syntax.html

### Community
- Jekyll Talk: https://talk.jekyllrb.com/
- Stack Overflow: Tag `jekyll`
