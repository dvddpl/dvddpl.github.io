# Contributing to dvddpl.github.io

Thank you for your interest in contributing to this blog! While this is a personal blog, contributions in the form of corrections, suggestions, and improvements are welcome.

## How to Contribute

### Reporting Issues

If you find any issues with the blog (broken links, typos, technical errors), please:

1. Check if the issue already exists in [GitHub Issues](https://github.com/dvddpl/dvddpl.github.io/issues)
2. If not, create a new issue with:
   - Clear description of the problem
   - Link to the affected page/post
   - Screenshots if applicable
   - Suggested fix (if you have one)

### Suggesting Improvements

For suggestions about content, design, or functionality:

1. Open a GitHub Issue with the "enhancement" label
2. Describe your suggestion clearly
3. Explain the benefit or problem it solves

### Submitting Corrections

For typos, broken links, or factual corrections:

1. Fork the repository
2. Create a branch: `git checkout -b fix/description`
3. Make your changes
4. Test locally: `bundle exec jekyll serve`
5. Commit with clear message: `git commit -m "Fix: description"`
6. Push and create a Pull Request

## Development Setup

### Prerequisites

- Ruby 2.6.10 or higher
- Bundler 2.2.3
- Git

### Setup Steps

```bash
# Fork and clone
git clone https://github.com/YOUR-USERNAME/dvddpl.github.io.git
cd dvddpl.github.io

# Install dependencies
gem install bundler:2.2.3
bundle install

# Run locally
bundle exec jekyll serve

# Visit http://localhost:4000
```

## Content Standards

### Front Matter Format

All posts must use this exact structure:

```yaml
---
layout: post
title: "Post Title"
description: "SEO description (150-160 characters)"
tags: tag1 tag2 tag3
background: /assets/images/image.jpg
---
```

### File Naming

Posts must follow Jekyll's convention:

```
_posts/YYYY-MM-DD-title-with-dashes.md
```

Examples:
- ✅ `_posts/2026-02-11-my-post.md`
- ❌ `_posts/my-post.md` (missing date)
- ❌ `_posts/2026-2-11-my-post.md` (not zero-padded)

### Image Guidelines

- Store in `assets/images/`
- Use absolute paths: `/assets/images/filename.jpg`
- Optimize before adding (compress, resize)
- Use descriptive filenames: `react-error.png` not `screenshot1.png`
- Always include alt text
- Credit sources at end of post

### Code Standards

- Always specify language for syntax highlighting:
  ```javascript
  const example = "code";
  ```
- Add comments to explain non-obvious code
- Keep examples focused and minimal
- Use realistic variable names
- Test code before submitting

### Writing Style

- Use conversational tone
- Be honest about failures and learning
- Focus on practical, actionable advice
- Keep paragraphs short (3-5 sentences)
- Use headings to structure content
- Include code examples where relevant

## Testing

Before submitting a PR, ensure:

```bash
# Build succeeds
bundle exec jekyll build

# No errors in output
bundle exec jekyll doctor

# Site works locally
bundle exec jekyll serve
# Test in browser at http://localhost:4000
```

### Checklist

- [ ] Front matter follows standard format
- [ ] Filename follows YYYY-MM-DD-title.md convention
- [ ] Images use absolute paths starting with /assets/images/
- [ ] Code blocks specify language
- [ ] All links work
- [ ] Images load correctly
- [ ] Tested locally
- [ ] Mobile responsive
- [ ] Spelling and grammar checked

## Pull Request Process

1. **Create a branch** with descriptive name:
   - `fix/broken-link-in-post`
   - `docs/improve-readme`
   - `content/fix-typo-in-post`

2. **Make focused changes**:
   - One PR per issue/fix
   - Keep changes minimal and relevant
   - Don't mix unrelated changes

3. **Write clear commit messages**:
   ```
   Fix: Broken link in "Coding is Boring" post
   
   - Updated link to Hamburg Serverless Days
   - Verified new link works
   ```

4. **Test thoroughly**:
   - Build locally
   - Check affected pages
   - Verify no new errors

5. **Create Pull Request**:
   - Clear title describing the change
   - Reference any related issues
   - Explain what changed and why
   - Include screenshots if relevant

6. **Respond to feedback**:
   - Address review comments
   - Make requested changes
   - Be open to suggestions

## What We're Looking For

### Welcome Contributions ✅

- Typo and grammar fixes
- Broken link corrections
- Code example improvements
- Technical accuracy corrections
- Documentation improvements
- Accessibility enhancements
- Performance optimizations

### Not Accepting ❌

- New blog posts (this is a personal blog)
- Major design changes (without prior discussion)
- Dependency updates without testing
- Changes to personal information
- Unrelated or off-topic content

## Code of Conduct

### Our Standards

- Be respectful and professional
- Focus on constructive feedback
- Assume good intentions
- Be patient with responses
- Keep discussions on-topic

### Unacceptable Behavior

- Harassment or discriminatory language
- Personal attacks
- Spam or self-promotion
- Off-topic discussions
- Disrespectful comments

## Questions?

If you have questions about contributing:

1. Check existing [GitHub Issues](https://github.com/dvddpl/dvddpl.github.io/issues)
2. Review the [README.md](README.md)
3. Open a new issue with your question

## Recognition

Contributors will be acknowledged in:
- Pull request comments
- Commit history
- Future blog posts (for significant contributions)

## License

By contributing, you agree that your contributions will be licensed under:
- MIT License for code
- CC BY 4.0 for content

See [LICENSE](LICENSE) for details.

## Resources

- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Markdown Guide](https://guides.github.com/features/mastering-markdown/)
- [How to Contribute to Open Source](https://opensource.guide/how-to-contribute/)

## Thank You!

Your contributions help make this blog better for everyone. Thank you for taking the time to contribute! 🙏
