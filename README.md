# Giscus Comments Template for MkDocs Material

[![MkDocs](https://img.shields.io/badge/Made%20with-MkDocs-526CFE?logo=materialformkdocs)](https://www.mkdocs.org/)
[![Material for MkDocs](https://img.shields.io/badge/Material%20for%20MkDocs-526CFE?logo=materialformkdocs)](https://squidfunk.github.io/mkdocs-material/)
[![Claude Code](https://img.shields.io/badge/Built%20with-Claude%20Code-DA7857?logo=anthropic)](https://claude.ai/code)
[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc-sa/4.0/)

## Overview

This template repository provides a complete, working example of integrating [Giscus](https://giscus.app/) (GitHub Discussions-based comments) into MkDocs Material sites. It is designed for educators and developers building intelligent textbooks who want to add community-driven discussions to their documentation.

The template includes:

- Pre-configured Giscus integration with theme synchronization (light/dark mode)
- Feedback widget that directs users to the comments section
- Support for page-level and folder-level comment enabling
- Step-by-step setup instructions for your own repository

Whether you're building an online course, technical documentation, or an intelligent textbook, this template provides everything you need to foster community engagement through GitHub Discussions.

## Features

- **GitHub Discussions Integration** - Comments are stored as GitHub Discussions, providing full GitHub features
- **Theme Synchronization** - Comments automatically match your site's light/dark mode
- **Flexible Enabling** - Enable comments per-page via front matter or per-folder via `.meta.yml`
- **Feedback Widget** - Integrated "Was this helpful?" widget that encourages discussion
- **No Server Required** - Runs entirely through GitHub, no backend infrastructure needed
- **Spam Protection** - GitHub authentication prevents anonymous spam

## Getting Started

### Prerequisites

- Python 3.8+
- A GitHub repository with Discussions enabled
- [Giscus GitHub App](https://github.com/apps/giscus) installed on your repository

### Clone the Repository

```bash
git clone https://github.com/dmccreary/giscus-template.git
cd giscus-template
```

### Install Dependencies

```bash
pip install mkdocs mkdocs-material
```

### Configure Giscus

1. Visit [giscus.app](https://giscus.app/) and configure for your repository
2. Copy the generated values and update `docs/overrides/partials/comments.html`:

```html
data-repo="YOUR_USERNAME/YOUR_REPO"
data-repo-id="YOUR_REPO_ID_HERE"
data-category="Comments"
data-category-id="YOUR_CATEGORY_ID_HERE"
```

### Build and Serve Locally

```bash
mkdocs serve
```

Open your browser to `http://localhost:8000`

### Deploy to GitHub Pages

```bash
mkdocs gh-deploy
```

## Repository Structure

```
giscus-template/
├── docs/
│   ├── overrides/
│   │   └── partials/
│   │       └── comments.html    # Giscus integration template
│   ├── css/
│   │   └── extra.css            # Custom styles
│   ├── js/
│   │   └── extra.js             # Custom JavaScript
│   ├── index.md                 # Home page
│   ├── sample-page.md           # Example with comments enabled
│   └── about.md                 # About page
├── mkdocs.yml                   # MkDocs configuration
├── CLAUDE.md                    # Claude Code guidance
└── README.md                    # This file
```

## Enabling Comments

### Per-Page

Add front matter to any markdown file:

```yaml
---
comments: true
---

# Your Page Title

Content here...
```

### Per-Folder

Create a `.meta.yml` file in any folder (requires `meta` plugin):

```yaml
comments: true
```

All pages in that folder will have comments enabled.

## Configuration Options

| Option | Description |
|--------|-------------|
| `data-mapping="pathname"` | One discussion per page URL (recommended) |
| `data-mapping="title"` | One discussion per page title |
| `data-reactions-enabled="1"` | Allow emoji reactions |
| `data-input-position="top"` | Comment box at top (or "bottom") |
| `data-theme="light"` | Initial theme (auto-syncs with palette) |
| `data-loading="lazy"` | Load comments when scrolled into view |

## Reporting Issues

Found a bug or have a suggestion? Please report it:

[Open an Issue](https://github.com/dmccreary/giscus-template/issues)

## License

This work is licensed under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).

**You are free to:**

- **Share** - copy and redistribute the material
- **Adapt** - remix, transform, and build upon the material

**Under the following terms:**

- **Attribution** - Give appropriate credit with a link to the original
- **NonCommercial** - No commercial use without permission
- **ShareAlike** - Distribute contributions under the same license

## Acknowledgements

- [MkDocs](https://www.mkdocs.org/) - Static site generator for project documentation
- [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) - Beautiful, responsive theme
- [Giscus](https://giscus.app/) - GitHub Discussions-powered comments
- [GitHub Pages](https://pages.github.com/) - Free hosting for open source projects

## Contact

**Dan McCreary**

- LinkedIn: [linkedin.com/in/danmccreary](https://www.linkedin.com/in/danmccreary/)
- GitHub: [@dmccreary](https://github.com/dmccreary)
