# Davide de Paolis - Technical Blog

Personal technical blog covering software engineering, AWS, serverless architecture, Node.js, React, leadership, and career development.

🌐 **Live Site**: [https://dvddpl.github.io](https://dvddpl.github.io)

## About

This blog is built with Jekyll and hosted on GitHub Pages. It features technical tutorials, debugging chronicles, career reflections, and personal development insights from a Technical Lead's perspective.

## Quick Start

### Prerequisites

- Ruby 2.6.10 or higher
- Bundler 2.2.3
- Git

### Setup

```bash
# Clone repository
git clone https://github.com/dvddpl/dvddpl.github.io.git
cd dvddpl.github.io

# Install correct bundler version
gem install bundler:2.2.3

# Install dependencies
bundle install

# Run local server
bundle exec jekyll serve

# Visit http://localhost:4000
```

## Writing Posts

### Create New Post

```bash
# 1. Create file with correct naming: YYYY-MM-DD-title.md
touch _posts/2026-02-11-my-new-post.md

# 2. Add front matter
cat > _posts/2026-02-11-my-new-post.md << 'EOF'
---
layout: post
title: "Your Post Title"
description: "Brief description for SEO (150-160 characters)"
tags: javascript nodejs aws
background: /assets/images/your-image.jpg
---

Your content here...
EOF

# 3. Add images to assets/images/

# 4. Test locally
bundle exec jekyll serve

# 5. Commit and push
git add _posts/2026-02-11-my-new-post.md
git commit -m "Add post: My New Post"
git push origin gh-pages
```

### Post Template

```yaml
---
layout: post
title: "Post Title Here"
description: "SEO description 150-160 characters"
tags: tag1 tag2 tag3
background: /assets/images/image-name.jpg
---

Opening paragraph that hooks the reader...

## Main Section

Content with code examples:

```javascript
const example = "code here";
```

## Conclusion

Key takeaways...

---
Photo by [Author](link) on [Unsplash](link)
```

## Project Structure

```
dvddpl.github.io/
├── _posts/              # Blog posts (YYYY-MM-DD-title.md)
├── assets/
│   └── images/          # Post images and backgrounds
├── .kiro/               # Agent context and steering files
│   ├── context/         # Project documentation for AI
│   └── steering/        # Development standards
├── _config.yml          # Jekyll configuration
├── Gemfile              # Ruby dependencies
├── index.md             # Home page
├── about.md             # About page
├── posts.md             # Posts by tags
├── contact.md           # Contact page
└── 404.html             # Error page
```

## Technology Stack

- **Generator**: Jekyll 3.10.0
- **Hosting**: GitHub Pages
- **Theme**: [StartBootstrap Clean Blog](https://github.com/StartBootstrap/startbootstrap-clean-blog-jekyll)
- **Language**: Ruby, Markdown
- **Deployment**: Automatic via GitHub Pages (gh-pages branch)

## Development

### Local Development

```bash
# Standard server
bundle exec jekyll serve

# With drafts
bundle exec jekyll serve --drafts

# With future posts
bundle exec jekyll serve --future

# Different port
bundle exec jekyll serve --port 4001

# Incremental build (faster)
bundle exec jekyll serve --incremental
```

### Update Dependencies

```bash
# Check outdated gems
bundle outdated

# Check GitHub Pages versions
open https://pages.github.com/versions

# Update github-pages gem
bundle update github-pages

# Test after updating
bundle exec jekyll serve
```

## Standards

### Front Matter

Every post must use this structure:

```yaml
---
layout: post
title: "Post Title"
description: "SEO description"
tags: tag1 tag2 tag3
background: /assets/images/image.jpg
---
```

### File Naming

Posts must follow: `_posts/YYYY-MM-DD-title-with-dashes.md`

### Images

- Store in `assets/images/`
- Use absolute paths: `/assets/images/filename.jpg`
- Optimize before adding
- Use descriptive filenames

### Tags

Use existing tags when possible:
- **Technical**: javascript, nodejs, react, aws, serverless, mysql, debugging, performance, tutorial
- **Career**: career, leadership, personalgrowth, advice, softwareengineering

## Deployment

### Automatic Deployment

Pushing to `gh-pages` branch automatically triggers GitHub Pages build:

```bash
git push origin gh-pages
# Wait 1-2 minutes
# Visit https://dvddpl.github.io
```

### Recommended Workflow

```bash
# Create feature branch
git checkout -b post/post-title

# Write and test post
bundle exec jekyll serve

# Commit changes
git add _posts/2026-02-11-post-title.md
git commit -m "Add post: Post Title"

# Push and create PR
git push origin post/post-title

# Review and merge to gh-pages
```

## Troubleshooting

### Bundler Version Mismatch

```bash
# Error: Could not find 'bundler' (2.2.3)
gem install bundler:2.2.3
bundle install
```

### Post Not Showing

- Check filename format: `YYYY-MM-DD-title.md`
- Check front matter has `layout: post`
- Check date isn't in future
- Check `published: false` isn't set

### Images Not Loading

- Check path starts with `/assets/images/`
- Check file exists in correct location
- Check filename matches exactly (case-sensitive)

### Build Errors

```bash
# See detailed errors
bundle exec jekyll build --verbose

# Check configuration
bundle exec jekyll doctor

# Clean and rebuild
bundle exec jekyll clean
bundle exec jekyll build
```

## Maintenance

### Monthly Tasks

- Update dependencies: `bundle update github-pages`
- Check for broken links
- Review analytics (if configured)

### Quarterly Tasks

- Review and update documentation
- Audit security practices
- Backup content (Git is primary backup)

## Contributing

This is a personal blog, but suggestions and corrections are welcome:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## Resources

- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Clean Blog Theme](https://github.com/StartBootstrap/startbootstrap-clean-blog-jekyll)
- [Markdown Guide](https://guides.github.com/features/mastering-markdown/)

## License

Content is licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
Code snippets are licensed under [MIT License](LICENSE).

## Author

**Davide de Paolis**
- Technical Lead at Goodgamestudios
- Location: Hamburg, Germany
- Blog: [https://dvddpl.github.io](https://dvddpl.github.io)
- DEV.to: [https://dev.to/dvddpl](https://dev.to/dvddpl)
- GitHub: [@dvddpl](https://github.com/dvddpl)

## Status

⚠️ **Note**: This blog was last updated in April 2021 and is currently being revived with updated dependencies and improved documentation.

### Current Version

- Jekyll: 3.10.0
- GitHub Pages: 209 → 232 (update recommended)
- Last Post: April 2021
- Total Posts: 9

### Roadmap

- [ ] Update dependencies to latest versions
- [ ] Standardize front matter across all posts
- [ ] Add search functionality
- [ ] Implement comments system
- [ ] Add dark mode
- [ ] Optimize images
- [ ] Resume regular posting schedule
