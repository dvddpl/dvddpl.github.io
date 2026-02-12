# Content Guide

## Content Strategy

### Blog Focus
Technical blog covering:
- AWS and serverless architecture
- Node.js, JavaScript, React
- Database optimization and ORs
- Software engineering best practices
- Leadership and career development
- Personal growth and productivity

### Target Audience
- Software engineers (junior to senior)
- Technical leads and managers
- Developers learning AWS/serverless
- Career-focused developers
- Hamburg tech community

### Content Types

#### 1. Technical Tutorials
**Purpose**: Teach specific skills or solve specific problems
**Format**: Problem → Solution → Explanation → Code examples
**Length**: 1000-2000 words
**Examples**: 
- "Filter Operators in React Admin and Sequelize"
- "From 11 seconds to 160 milliseconds - Refactoring Chronicles"

#### 2. Debugging Chronicles
**Purpose**: Share problem-solving journey and lessons learned
**Format**: Problem → Investigation → Solution → Lessons
**Length**: 800-1500 words
**Examples**:
- "Range Not Satisfiable Error in React Admin"

#### 3. Career Reflections
**Purpose**: Share career insights and personal growth
**Format**: Story → Reflection → Lessons → Advice
**Length**: 800-1200 words
**Examples**:
- "Coding is Boring! Why do you love coding?"
- "Maybe you should start listening to that voice telling you that YOU SUCK!"

#### 4. Book Reviews
**Purpose**: Share learnings from books
**Format**: Context → Summary → Key takeaways → Application
**Length**: 600-1000 words
**Examples**:
- "Managing Incompetence: An Innovative Approach for Dealing with People"

#### 5. Retrospectives
**Purpose**: Reflect on periods of time, goals, achievements
**Format**: Goals → What happened → Lessons → Future plans
**Length**: 1000-1500 words
**Examples**:
- "A year without goals (2020 retrospective - 2021 resolutions)"

## Writing Standards

### Front Matter Standards

#### Standard Template
```yaml
---
layout: post
title: "Your Post Title Here"
description: "Brief description for SEO and social media previews (150-160 chars)"
tags: javascript nodejs aws serverless career
background: /assets/your-background.jpg
---
```

#### Field Guidelines

**layout**: Always `post` for blog posts

**title**: 
- Use title case
- Keep under 60 characters for SEO
- Be specific and descriptive
- Use quotes if title contains colons

**description**:
- 150-160 characters optimal for SEO
- Summarize key value/takeaway
- Include relevant keywords naturally
- Don't duplicate title exactly

**tags**:
- 2-5 tags per post
- Use lowercase
- Space-separated (no commas)
- Be consistent with existing tags
- Common tags: javascript, nodejs, aws, serverless, react, career, leadership, debugging, tutorial, personalgrowth

**background**:
- Always use `/assets/filename.jpg` format
- Image should be 1900x1000px or similar ratio
- Relevant to post topic
- Properly licensed (Unsplash, own photos, etc.)

### Content Structure

#### Opening (First 100 words)
- Hook reader with interesting question, story, or problem
- Clearly state what post is about
- Explain why reader should care

#### TL;DR (Optional but recommended)
- Brief summary of key points
- Solution/answer upfront
- Helps busy readers decide if they want to read more

#### Body
- Use ## for main sections (# is reserved for title)
- Use ### for subsections
- Keep paragraphs short (3-5 sentences)
- Use bullet points for lists
- Include code examples where relevant
- Add images to break up text

#### Conclusion
- Summarize key takeaways
- Provide next steps or call to action
- Link to related posts or resources

#### Credits
- Always credit image sources
- Link to referenced articles/docs
- Acknowledge people who helped

### Writing Style

#### Voice & Tone
- **Conversational**: Write like talking to a colleague
- **Honest**: Share failures and struggles, not just successes
- **Practical**: Focus on actionable advice
- **Humble**: Acknowledge limitations and learning
- **Encouraging**: Support readers in their journey

#### Language
- Use "I" and "we" (personal perspective)
- Use "you" to address reader directly
- Avoid jargon unless necessary (explain when used)
- Use active voice
- Keep sentences clear and concise

#### Technical Content
- Explain concepts before diving into code
- Provide context for why something matters
- Include complete, working code examples
- Add comments to explain complex code
- Show both problem and solution
- Mention edge cases and limitations

### Code Examples

#### Formatting
```javascript
// Use triple backticks with language identifier
// Add comments to explain non-obvious code
const example = (param) => {
    // Explain what this does
    return param.map(item => item.value);
};
```

#### Best Practices
- Keep examples focused and minimal
- Use realistic variable names
- Include necessary imports/context
- Show complete working examples when possible
- Highlight key lines or changes
- Test code before publishing

### Images

#### Image Guidelines
- **Size**: 1900x1000px for backgrounds, optimize for web
- **Format**: JPG for photos, PNG for screenshots/diagrams
- **Location**: Always in `/assets/`
- **Naming**: Descriptive, lowercase, hyphens (e.g., `react-admin-error.png`)
- **Alt text**: Always include descriptive alt text
- **Licensing**: Use properly licensed images (Unsplash, own photos)

#### Image Credits
```markdown
---
Photo by [Author Name](author-link) on [Unsplash](photo-link)
```

### Links

#### Internal Links
```markdown
[Related post]({% post_url 2021-01-04-managing-incompetence %})
```

#### External Links
```markdown
[Link text](https://example.com)
```

#### Best Practices
- Use descriptive link text (not "click here")
- Open external links in same tab (default)
- Verify links work before publishing
- Link to official documentation when possible
- Archive important external content if possible

## Tag System

### Existing Tags
- **Technical**: javascript, nodejs, react, aws, serverless, mysql, debugging, performance, tutorial
- **Career**: career, leadership, managingpeople, motivation, personalgrowth, personaldevelopment
- **Meta**: beginners, advice, softwareengineering

### Tag Guidelines
- Use 2-5 tags per post
- Choose most relevant tags
- Create new tags sparingly
- Keep tags lowercase
- Use existing tags when possible
- Tags appear on `/posts/` page automatically

## SEO Best Practices

### Title Optimization
- Include primary keyword
- Keep under 60 characters
- Make it compelling and specific
- Use numbers when relevant ("5 Ways to...")

### Description Optimization
- Include primary and secondary keywords
- 150-160 characters
- Compelling call to action
- Unique for each post

### Content Optimization
- Use headings hierarchically (##, ###)
- Include keywords naturally in first paragraph
- Use keywords in headings
- Add alt text to all images
- Link to related internal posts
- Link to authoritative external sources

### URL Structure
- Automatic from filename: `/YYYY/MM/DD/title-with-dashes/`
- Keep titles concise for cleaner URLs
- Use hyphens, not underscores

## Quality Checklist

### Before Publishing
- [ ] Title is clear and compelling
- [ ] Description is 150-160 characters
- [ ] Tags are relevant and consistent
- [ ] Opening hooks the reader
- [ ] Content delivers on title promise
- [ ] Code examples are tested and working
- [ ] Images are optimized and have alt text
- [ ] Links are verified and working
- [ ] Spelling and grammar checked
- [ ] Post tested locally
- [ ] Mobile responsive checked
- [ ] Image credits included

### Content Quality
- [ ] Provides clear value to reader
- [ ] Solves a specific problem or teaches something
- [ ] Includes practical examples
- [ ] Acknowledges limitations or edge cases
- [ ] Links to additional resources
- [ ] Appropriate length (not too short or too long)
- [ ] Well-structured with clear sections
- [ ] Engaging and easy to read

## Post Ideas

### Technical Topics
- AWS Lambda best practices
- Serverless architecture patterns
- React Admin customization
- Database optimization techniques
- Node.js performance tips
- API design patterns
- Testing strategies
- CI/CD for serverless

### Career Topics
- Technical leadership lessons
- Code review best practices
- Mentoring junior developers
- Work-life balance in tech
- Remote work tips
- Career transitions
- Interview preparation
- Salary negotiations

### Personal Growth
- Learning strategies for developers
- Time management techniques
- Dealing with imposter syndrome
- Building confidence
- Public speaking for developers
- Writing technical content
- Building a personal brand

## Promotion Strategy

### Cross-Posting
- DEV.to (original platform)
- Medium (optional)
- LinkedIn (share with network)
- Twitter (if active)

### Engagement
- Respond to comments
- Share on social media
- Engage with Hamburg tech community
- Present at meetups based on posts
- Link from related posts

## Content Calendar

### Frequency Goal
- Minimum: 1 post per month
- Target: 2 posts per month
- Ideal: 1 post per week

### Planning
- Keep list of post ideas
- Draft posts in advance
- Schedule writing time
- Batch similar content
- Plan around events/conferences

## Analytics & Improvement

### Track Metrics
- Page views per post
- Time on page
- Bounce rate
- Social shares
- Comments/engagement

### Iterate
- Review what performs well
- Double down on popular topics
- Experiment with new formats
- Update old posts with new information
- Repurpose content (talks, videos, etc.)

## Resources

### Writing Tools
- Grammarly: Grammar and spelling
- Hemingway App: Readability
- Carbon: Code screenshots
- Unsplash: Free stock photos
- TinyPNG: Image optimization

### Inspiration
- DEV.to trending posts
- Hacker News
- Reddit r/programming
- Twitter tech community
- Conference talks

### Learning
- "On Writing Well" by William Zinsser
- "Everybody Writes" by Ann Handley
- "Technical Writing" courses
- DEV.to writing guides
