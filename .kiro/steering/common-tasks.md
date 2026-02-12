---
inclusion: always
---

# Common Tasks

Quick reference for frequent operations on this Jekyll blog.

## Writing a New Post

```bash
# 1. Create post file with correct naming
touch _posts/2026-02-11-my-post-title.md

# 2. Add front matter (copy from template below)
# 3. Write content in Markdown
# 4. Add images to assets/
# 5. Test locally
bundle exec jekyll serve

# 6. Commit and push
git add _posts/2026-02-11-my-post-title.md assets/
git commit -m "Add post: My Post Title"
git push origin gh-pages
```

### Post Template
```yaml
---
layout: post
title: "Your Post Title"
description: "Brief description for SEO (150-160 chars)"
tags: javascript nodejs aws
background: /assets/your-image.jpg
---

Opening paragraph that hooks the reader...

## Main Section

Content here...

## Conclusion

Wrap up with key takeaways...

---
Photo by [Author](link) on [Unsplash](link)
```

## Updating Dependencies

```bash
# Check what's outdated
bundle outdated

# Check GitHub Pages current versions
open https://pages.github.com/versions

# Update github-pages gem (recommended)
# First, edit Gemfile to update version number
# Then:
bundle update github-pages

# Test after updating
bundle exec jekyll serve
```

## Fixing Bundler Issues

```bash
# If bundler version mismatch:
gem install bundler:2.2.3

# If permission errors:
gem install bundler:2.2.3 --user-install

# If gems won't install:
bundle install --path vendor/bundle
```

## Testing Locally

```bash
# Standard server
bundle exec jekyll serve

# With drafts visible
bundle exec jekyll serve --drafts

# With future posts
bundle exec jekyll serve --future

# Different port
bundle exec jekyll serve --port 4001

# Incremental build (faster)
bundle exec jekyll serve --incremental

# Verbose output for debugging
bundle exec jekyll serve --verbose
```

## Troubleshooting Posts

### Post Not Showing
```bash
# Check filename format
ls -la _posts/

# Should be: YYYY-MM-DD-title.md
# Check front matter has layout: post
# Check date isn't in future
# Check published: false isn't set
```

### Images Not Loading
```bash
# Verify image exists
ls -la assets/your-image.jpg

# Check path in post starts with /assets/
# Check filename matches exactly (case-sensitive)
```

### Build Errors
```bash
# Build without serving to see errors
bundle exec jekyll build

# Check for YAML syntax errors in front matter
# Check for Markdown syntax issues
# Run doctor command
bundle exec jekyll doctor
```

## Adding Images

```bash
# 1. Optimize image first (compress, resize)
# 2. Copy to assets directory
cp ~/Downloads/my-image.jpg assets/

# 3. Use descriptive filename
mv assets/my-image.jpg assets/react-admin-filter-error.jpg

# 4. Reference in post with absolute path
# ![Description](/assets/react-admin-filter-error.jpg)
```

## Standardizing Front Matter

If you need to fix inconsistent front matter across posts:

```bash
# Find posts using cover_image instead of background
grep -r "cover_image:" _posts/

# Find posts with published field
grep -r "published:" _posts/

# Manually edit each file to use standard format
```

## Cleaning Build Artifacts

```bash
# Remove generated files
bundle exec jekyll clean

# Rebuild from scratch
bundle exec jekyll build

# Check what was generated
ls -la _site/
```

## Checking for Broken Links

```bash
# Build site
bundle exec jekyll build

# Use link checker (if installed)
# brew install linkchecker
linkchecker _site/index.html

# Or manually check important pages
```

## Git Workflow for Posts

### Simple Workflow (Direct to gh-pages)
```bash
git add _posts/2026-02-11-new-post.md
git commit -m "Add post: New Post Title"
git push origin gh-pages
```

### Better Workflow (Feature Branch)
```bash
# Create branch
git checkout -b post/new-post-title

# Write and commit
git add _posts/2026-02-11-new-post.md
git commit -m "Add post: New Post Title"

# Push branch
git push origin post/new-post-title

# Create PR on GitHub, review, then merge
# After merge, delete branch
git checkout gh-pages
git pull
git branch -d post/new-post-title
```

## Updating Site Configuration

```bash
# Edit config
vim _config.yml

# IMPORTANT: Must restart server to see changes
# Ctrl+C to stop server, then:
bundle exec jekyll serve
```

## Creating New Pages

```bash
# Create page file
touch my-new-page.md

# Add front matter
cat > my-new-page.md << 'EOF'
---
layout: page
title: My New Page
permalink: /my-new-page/
background: /assets/background_test.png
---

Page content here...
EOF

# Test locally
bundle exec jekyll serve
```

## Checking Site Performance

```bash
# Build site
bundle exec jekyll build

# Check build time
time bundle exec jekyll build

# Check site size
du -sh _site/

# Check image sizes
du -sh assets/*
```

## Backing Up Content

```bash
# All content is in Git, but for extra safety:

# Export posts
tar -czf posts-backup-$(date +%Y%m%d).tar.gz _posts/

# Export images
tar -czf images-backup-$(date +%Y%m%d).tar.gz assets/

# Or just ensure Git is up to date
git push origin gh-pages
```

## Viewing Site Locally

```bash
# Start server
bundle exec jekyll serve

# Open in browser
open http://localhost:4000

# Or specific page
open http://localhost:4000/2026/02/11/post-title/
```

## Checking Deployed Site

```bash
# View live site
open https://dvddpl.github.io

# Check specific post
open https://dvddpl.github.io/2026/02/11/post-title/

# Check GitHub Pages status
open https://github.com/dvddpl/dvddpl.github.io/settings/pages
```

## Quick Fixes

### Fix Bundler Version
```bash
gem install bundler:2.2.3
bundle install
```

### Update All Dependencies
```bash
bundle update
bundle exec jekyll serve
```

### Reset Local Environment
```bash
bundle exec jekyll clean
rm -rf _site .jekyll-cache
bundle install
bundle exec jekyll serve
```

### Fix Git Issues
```bash
# If behind remote
git pull origin gh-pages

# If conflicts
git status
# Resolve conflicts, then:
git add .
git commit -m "Resolve conflicts"
git push origin gh-pages
```

## Emergency Rollback

If a deploy breaks the site:

```bash
# Find last working commit
git log --oneline

# Revert to that commit
git revert <commit-hash>
git push origin gh-pages

# Or hard reset (use with caution)
git reset --hard <commit-hash>
git push origin gh-pages --force
```

## Monthly Maintenance

```bash
# 1. Update dependencies
bundle update github-pages

# 2. Test locally
bundle exec jekyll serve

# 3. Check for broken links
# (manually or with tool)

# 4. Review analytics
# (if configured)

# 5. Commit updates
git add Gemfile.lock
git commit -m "Update dependencies"
git push origin gh-pages
```

## Getting Help

### Check Logs
```bash
# Verbose build output
bundle exec jekyll build --verbose

# Check Jekyll version
bundle exec jekyll --version

# Check gem versions
bundle list
```

### Common Resources
- Jekyll docs: https://jekyllrb.com/docs/
- GitHub Pages docs: https://docs.github.com/en/pages
- Theme repo: https://github.com/StartBootstrap/startbootstrap-clean-blog-jekyll
- Jekyll Talk forum: https://talk.jekyllrb.com/

### Debug Mode
```bash
# Run with debug output
JEKYLL_ENV=development bundle exec jekyll serve --verbose --trace
```
