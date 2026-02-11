---
inclusion: always
---

# Blog Standards

When working on this Jekyll blog, always follow these standards:

## Front Matter Standards

Every blog post MUST use this exact front matter structure:

```yaml
---
layout: post
title: "Post Title Here"
description: "SEO description 150-160 characters"
tags: tag1 tag2 tag3
background: /assets/images/image-name.jpg
---
```

### Rules:
- `layout` is always `post` for blog posts
- `title` must use quotes if it contains colons
- `description` should be 150-160 characters for optimal SEO
- `tags` are space-separated, lowercase, no commas
- `background` must use absolute path starting with `/assets/images/`
- Do NOT use `cover_image` field (inconsistent with theme)
- Do NOT use `published` field unless intentionally hiding post

## File Naming Convention

Blog posts MUST follow Jekyll's naming convention:

```
_posts/YYYY-MM-DD-title-with-dashes.md
```

Examples:
- ✅ `_posts/2026-02-11-my-new-post.md`
- ❌ `_posts/my-new-post.md` (missing date)
- ❌ `_posts/2026-2-11-my-new-post.md` (month/day not zero-padded)
- ❌ `_posts/2026-02-11-my_new_post.md` (underscores instead of dashes)

## Image Standards

### Image Paths
- Always use absolute paths: `/assets/images/filename.jpg`
- Never use relative paths: `../assets/images/filename.jpg`
- Never use external URLs for local images

### Image Files
- Store all images in `assets/images/` directory
- Use descriptive filenames: `react-admin-error.png` not `screenshot1.png`
- Use lowercase with hyphens: `my-image.jpg` not `My_Image.jpg`
- Optimize images before adding (compress, resize appropriately)
- Background images should be ~1900x1000px

### Image Credits
Always credit image sources at the end of posts:

```markdown
---
Photo by [Author Name](author-url) on [Unsplash](photo-url)
```

## Code Standards

### Code Blocks
Always specify language for syntax highlighting:

```javascript
// Good
const example = "code here";
```

Not:
```
// Bad - no language specified
const example = "code here";
```

### Code Comments
- Add comments to explain non-obvious code
- Keep examples focused and minimal
- Use realistic variable names
- Test code before publishing

## Content Structure

### Required Sections
1. Opening hook (first 100 words)
2. Main content with clear headings
3. Conclusion with takeaways
4. Image credits (if applicable)

### Heading Hierarchy
- Use `##` for main sections (not `#` - that's reserved for title)
- Use `###` for subsections
- Don't skip levels (don't go from `##` to `####`)

## Tag Standards

### Existing Tags (Use These)
**Technical**: javascript, nodejs, react, aws, serverless, mysql, debugging, performance, tutorial, fullstack

**Career**: career, leadership, managingpeople, motivation, personalgrowth, personaldevelopment, beginners, advice, softwareengineering

### Tag Rules
- Use 2-5 tags per post
- Use lowercase only
- Use existing tags when possible
- Create new tags only when necessary
- Tags are space-separated, no commas

## Development Workflow

### Local Testing Required
Before pushing any post:

```bash
# 1. Run local server
bundle exec jekyll serve

# 2. Check:
# - Post appears in list
# - Title and description correct
# - Images load properly
# - Code formatting correct
# - Links work
# - Mobile responsive
```

### Git Workflow
Recommended workflow for new posts:

```bash
# 1. Create feature branch
git checkout -b post/post-title

# 2. Write and test post
# 3. Commit changes
git add _posts/2026-02-11-post-title.md
git commit -m "Add post: Post Title"

# 4. Push and create PR
git push origin post/post-title

# 5. Review and merge to gh-pages
```

## Dependency Management

### Updating Dependencies
When updating gems:

```bash
# Check current versions
bundle outdated

# Compare with GitHub Pages versions
# Visit: https://pages.github.com/versions

# Update github-pages gem (updates all Jekyll dependencies)
bundle update github-pages

# Test thoroughly after updating
bundle exec jekyll serve
```

### Version Pinning
- Keep `github-pages` gem version in sync with GitHub Pages
- Pin remote theme to specific version/commit
- Document any custom gem versions in README

## Configuration Standards

### _config.yml
- Keep site metadata up to date
- Don't commit sensitive information
- Restart server after config changes
- Document any custom variables

### Gemfile
- Use `github-pages` gem for dependency management
- Specify version: `gem "github-pages", "~> 232", group: :jekyll_plugins`
- Keep Gemfile.lock committed
- Document any additional gems

## Quality Checklist

Before publishing any post, verify:

- [ ] Front matter follows standard format
- [ ] Filename follows YYYY-MM-DD-title.md convention
- [ ] Images use absolute paths starting with /assets/images/
- [ ] Code blocks specify language
- [ ] All links work
- [ ] Images load correctly
- [ ] Post tested locally
- [ ] Mobile responsive
- [ ] Spelling and grammar checked
- [ ] Image credits included

## Security Standards

### Never Commit
- API keys or secrets
- Personal email addresses (use obfuscation)
- Private information
- Credentials of any kind

### Email Obfuscation
Use HTML entities or contact forms instead of plain text:

```html
<!-- Good -->
<a href="mailto:&#100;&#100;&#112;&#64;&#103;&#109;&#97;&#105;&#108;&#46;&#99;&#111;&#109;">Email</a>

<!-- Bad -->
Email: user@example.com
```

## Performance Standards

### Image Optimization
- Compress images before adding
- Use appropriate dimensions (don't upload 4K images)
- Consider WebP format for modern browsers
- Use lazy loading for images below fold

### Build Performance
- Use incremental builds during development
- Clean build artifacts periodically
- Limit posts during development testing

## Documentation Standards

### README.md
Keep README up to date with:
- Setup instructions
- How to write posts
- How to deploy
- Troubleshooting common issues

### Code Comments
- Explain why, not what
- Document any workarounds
- Link to relevant issues or docs

## Error Handling

### Common Issues

**Post not showing:**
- Check filename format
- Check front matter has `layout: post`
- Check date isn't in future
- Check `published: false` isn't set

**Images not loading:**
- Check path starts with `/assets/images/`
- Check file exists
- Check filename matches exactly (case-sensitive)

**Build errors:**
- Check YAML front matter is valid
- Check Markdown syntax
- Run `bundle exec jekyll build` to see errors

## Maintenance Standards

### Regular Tasks
- **Monthly**: Update dependencies, check for broken links
- **Quarterly**: Review and update documentation
- **Yearly**: Audit security practices, review theme updates

### Backup Strategy
- All content in Git (primary backup)
- Consider periodic exports
- Keep local copy of images

## When to Break These Rules

These standards can be broken when:
- Experimenting with new features (use feature branch)
- Theme requires different format
- GitHub Pages updates change requirements

Always document exceptions and reasoning.
