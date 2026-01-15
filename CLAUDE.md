# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a template repository demonstrating Giscus (GitHub Discussions-based comments) integration with mkdocs-material sites. It serves as a reference implementation for adding comment functionality to MkDocs-based intelligent textbooks.

## Build Commands

```bash
# Start local development server
mkdocs serve

# Build static site
mkdocs build

# Deploy to GitHub Pages
mkdocs gh-deploy
```

## Architecture

### Theme Override System

MkDocs Material uses a theme override system via `docs/overrides/`. The key file is:

- `docs/overrides/partials/comments.html` - Giscus script injection and dark/light theme synchronization

This partial is included when a page has `comments: true` in its front matter.

### Comments Configuration

The Giscus configuration in `comments.html` requires these repository-specific values from https://giscus.app/:
- `data-repo` - GitHub repository (username/repo)
- `data-repo-id` - Repository ID
- `data-category` - Discussion category name
- `data-category-id` - Category ID

### Page-Level Comments

Enable comments on individual pages via front matter:
```yaml
---
comments: true
---
```

Enable comments for entire folders using `.meta.yml` files (requires `meta` plugin in mkdocs.yml).

### Feedback Widget Integration

The `mkdocs.yml` configures a feedback widget (happy/sad face ratings) in `extra.analytics.feedback`. Both positive and negative feedback responses direct users to the Giscus comments section.
