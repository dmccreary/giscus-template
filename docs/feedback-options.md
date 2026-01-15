# Feedback Options for MkDocs Sites

This page describes various options for collecting user feedback on MkDocs Material sites. You can combine multiple approaches to capture both quick sentiment and detailed responses.

## Feedback Methods Overview

| Method | User Effort | Data Type | Storage |
|--------|-------------|-----------|---------|
| Feedback Widget | One click | Quantitative | Google Analytics |
| Giscus Comments | Write comment | Qualitative | GitHub Discussions |
| Edit This Page | Submit PR | Direct fix | GitHub |
| Hypothesis | Highlight + annotate | Contextual | Hypothesis |
| Utterances | Write comment | Qualitative | GitHub Issues |
| Google Forms | Fill form | Structured | Google Sheets |
| Issue Templates | Fill template | Structured | GitHub Issues |

## 1. Feedback Widget (Built-in)

MkDocs Material includes a feedback widget that sends ratings to Google Analytics. This is the simplest way to collect quick sentiment data.

### Basic Configuration (2 Options)

```yaml
extra:
  analytics:
    provider: google
    property: G-XXXXXXXXXX
    feedback:
      title: Was this page helpful?
      ratings:
        - icon: material/emoticon-happy-outline
          name: This page was helpful
          data: 1
          note: Thanks for your feedback!
        - icon: material/emoticon-sad-outline
          name: This page could be improved
          data: 0
          note: Thanks! Please share details below.
```

### Extended Configuration (4 Options)

You can add up to 4 rating options for more granular feedback:

```yaml
extra:
  analytics:
    provider: google
    property: G-XXXXXXXXXX
    feedback:
      title: How would you rate this page?
      ratings:
        - icon: material/emoticon-sad-outline
          name: Confusing
          data: 1
          note: >-
            Sorry to hear that. Please share what was confusing
            in the comments below.
        - icon: material/emoticon-neutral-outline
          name: Could be better
          data: 2
          note: >-
            Thanks for the feedback. What could we improve?
        - icon: material/emoticon-happy-outline
          name: Helpful
          data: 3
          note: >-
            Great to hear! Feel free to share what helped.
        - icon: material/emoticon-excited-outline
          name: Excellent
          data: 4
          note: >-
            Wonderful! Consider sharing this page with others.
```

### Available Icons

You can use any [Material Design Icon](https://pictogrammers.com/library/mdi/). Common choices:

| Icon | Name |
|------|------|
| :material-emoticon-happy-outline: | `material/emoticon-happy-outline` |
| :material-emoticon-sad-outline: | `material/emoticon-sad-outline` |
| :material-emoticon-neutral-outline: | `material/emoticon-neutral-outline` |
| :material-emoticon-excited-outline: | `material/emoticon-excited-outline` |
| :material-thumb-up-outline: | `material/thumb-up-outline` |
| :material-thumb-down-outline: | `material/thumb-down-outline` |
| :material-star-outline: | `material/star-outline` |
| :material-heart-outline: | `material/heart-outline` |

### Pros and Cons

| Pros | Cons |
|------|------|
| Zero friction for users | Only quantitative data |
| No login required | Requires Google Analytics |
| Aggregate statistics available | No detailed feedback |
| Built into MkDocs Material | Can't respond to users |

## 2. Giscus Comments

[Giscus](https://giscus.app/) uses GitHub Discussions for comments. This is the primary method documented in this template.

### Key Features

- Comments stored in your GitHub repository
- GitHub authentication prevents spam
- Threaded conversations
- Emoji reactions
- Light/dark theme sync
- GraphQL API for analytics

### When to Use

- You want persistent, searchable feedback
- Your audience has GitHub accounts
- You need threaded discussions
- You want to respond to feedback

See the [Getting Started](tutorial/getting-started.md) guide for setup instructions.

## 3. Edit This Page

MkDocs Material can display an "Edit this page" button that links directly to the source file on GitHub.

### Configuration

```yaml
repo_url: https://github.com/username/repo
edit_uri: blob/main/docs/

theme:
  features:
    - content.action.edit
```

### Pros and Cons

| Pros | Cons |
|------|------|
| Direct contributions | Requires Git knowledge |
| Fixes go straight to source | Higher barrier to entry |
| Creates PR for review | Only for content fixes |
| No separate system needed | Not for questions/discussion |

### Best For

- Technical audiences comfortable with GitHub
- Open source documentation
- Catching typos and errors

## 4. Hypothesis Annotations

[Hypothesis](https://web.hypothes.is/) allows users to highlight specific text and add inline annotations.

### Installation

Add this script to your site (e.g., in `docs/js/extra.js` or a custom template):

```html
<script src="https://hypothes.is/embed.js" async></script>
```

Or add to `mkdocs.yml`:

```yaml
extra_javascript:
  - https://hypothes.is/embed.js
```

### How It Works

1. Users highlight any text on the page
2. A sidebar appears for adding annotations
3. Annotations are stored on Hypothesis servers
4. Other users can see and reply to annotations

### Pros and Cons

| Pros | Cons |
|------|------|
| Precise, contextual feedback | Requires Hypothesis account |
| Points to exact text | Annotations are public by default |
| Great for peer review | External dependency |
| Discussion threads | May clutter the interface |

### Best For

- Academic content
- Collaborative editing
- Peer review
- Pointing out specific confusing sentences

## 5. Utterances

[Utterances](https://utteranc.es/) is similar to Giscus but uses GitHub Issues instead of Discussions.

### Configuration

Create `docs/overrides/partials/comments.html`:

```html
{% if page.meta.comments %}
<script src="https://utteranc.es/client.js"
        repo="username/repo"
        issue-term="pathname"
        theme="github-light"
        crossorigin="anonymous"
        async>
</script>
{% endif %}
```

### Giscus vs Utterances

| Feature | Giscus | Utterances |
|---------|--------|------------|
| Storage | GitHub Discussions | GitHub Issues |
| Reactions | Yes | Limited |
| Threading | Full support | Limited |
| API | GraphQL | REST |
| Repo requirement | Discussions enabled | Issues enabled |

### When to Choose Utterances

- Your repo doesn't have Discussions enabled
- You prefer Issues over Discussions
- You have an existing Utterances setup

## 6. Google Forms

Embed a Google Form for structured feedback with specific questions.

### Creating the Form

1. Create a form at [forms.google.com](https://forms.google.com)
2. Add questions relevant to your content
3. Get the embed code

### Embedding in a Page

```html
<iframe
  src="https://docs.google.com/forms/d/e/FORM_ID/viewform?embedded=true"
  width="100%"
  height="600"
  frameborder="0"
  marginheight="0"
  marginwidth="0">
  Loading...
</iframe>
```

### Example Questions for Textbooks

- What topic brought you to this page?
- Did you find what you were looking for? (Yes/No)
- How would you rate this explanation? (1-5)
- What's your experience level with this topic?
- What would you add or change?

### Pros and Cons

| Pros | Cons |
|------|------|
| Structured responses | Extra click to open form |
| Custom questions | Responses in separate system |
| Easy to analyze | No public discussion |
| No GitHub required | Requires Google account to create |

## 7. GitHub Issue Templates

Create pre-filled issue templates for different types of feedback.

### Creating Templates

Create `.github/ISSUE_TEMPLATE/feedback.yml`:

```yaml
name: Page Feedback
description: Provide feedback on a documentation page
title: "[Feedback] "
labels: ["feedback"]
body:
  - type: input
    id: page
    attributes:
      label: Page URL
      placeholder: https://example.com/page
    validations:
      required: true
  - type: dropdown
    id: type
    attributes:
      label: Feedback Type
      options:
        - Confusing explanation
        - Missing information
        - Error or typo
        - Suggestion for improvement
        - Other
    validations:
      required: true
  - type: textarea
    id: details
    attributes:
      label: Details
      placeholder: Please describe your feedback...
    validations:
      required: true
```

### Linking from Pages

Add a feedback button to your pages:

```markdown
[Provide Feedback](https://github.com/USERNAME/REPO/issues/new?template=feedback.yml&title=[Feedback]+PAGE_TITLE){ .md-button }
```

## 8. Content-Specific Questions

Different content types benefit from different feedback questions:

| Content Type | Question | Why |
|--------------|----------|-----|
| Tutorial | "Were you able to complete this?" | Measures effectiveness |
| Concept | "Do you understand this now?" | Checks clarity |
| Reference | "Did you find what you needed?" | Checks completeness |
| Example | "Was this example helpful?" | Validates relevance |
| Quiz | "Was this quiz fair?" | Checks alignment with content |

### Implementation

Use different feedback widget notes per section via `.meta.yml`:

```yaml
# In tutorials/.meta.yml
comments: true
# Custom note could be added via template override
```

## Recommended Combinations

### For Educational Content (Textbooks)

1. **Feedback widget** - Quick sentiment on every page
2. **Giscus comments** - Detailed questions and discussions
3. **Edit this page** - Direct fixes from advanced users
4. **Issue templates** - Structured error reports

### For Technical Documentation

1. **Feedback widget** - Quick "was this helpful?"
2. **Edit this page** - Direct contributions
3. **GitHub Issues** - Bug reports and feature requests

### For Collaborative Projects

1. **Hypothesis** - Inline annotations and peer review
2. **Giscus comments** - Page-level discussions
3. **Edit this page** - Direct contributions

## Viewing and Analyzing Feedback

| Method | Where to View Data |
|--------|-------------------|
| Feedback Widget | Google Analytics → Events → feedback |
| Giscus | GitHub → Discussions tab |
| Edit This Page | GitHub → Pull Requests |
| Hypothesis | hypothesis.is dashboard |
| Utterances | GitHub → Issues |
| Google Forms | Google Forms → Responses |
| Issue Templates | GitHub → Issues |

## Summary

The best feedback system depends on your audience and goals:

- **Quick sentiment**: Feedback widget
- **Detailed discussion**: Giscus comments
- **Direct contributions**: Edit this page
- **Contextual annotations**: Hypothesis
- **Structured responses**: Google Forms or Issue Templates

This template implements the first three methods, with Giscus appearing when users click a feedback icon. This captures both quantitative data (Google Analytics) and qualitative feedback (GitHub Discussions) in a single, streamlined flow.
