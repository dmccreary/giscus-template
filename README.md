# Giscus Comments Integration for mkdocs-material

This example shows how to add Giscus (GitHub Discussions-based comments)  to your mkdocs-material site.

## Directory Structure

```
your-project/
├── mkdocs.yml
└── docs/
    ├── overrides/
    │   └── partials/
    │       └── comments.html    # Giscus integration template
    ├── index.md
    └── sample-page.md           # Example page with comments enabled
```

## Setup Instructions

### Step 1: Enable GitHub Discussions

1. Go to your GitHub repository
2. Click **Settings** → **General**
3. Scroll to **Features** section
4. Check **Discussions**

### Step 2: Install Giscus GitHub App

1. Visit [https://github.com/apps/giscus](https://github.com/apps/giscus)
2. Click **Install**
3. Select your repository (or all repositories)
4. Authorize the app

### Step 3: Create a Discussion Category

1. Go to your repository's **Discussions** tab
2. Click the gear icon (⚙️) next to Categories
3. Create a new category called **Comments** (or similar)
4. Set format to **Announcement** (recommended - only maintainers can create new discussions, but anyone can comment)

### Step 4: Generate Your Giscus Configuration

1. Visit [https://giscus.app/](https://giscus.app/)
2. Enter your repository (e.g., `dmccreary/intelligent-textbooks`)
3. Select your Discussion Category (e.g., **Comments**)
4. Choose mapping: **pathname** (recommended)
5. Enable features you want (reactions, metadata)
6. Copy the generated `<script>` snippet

### Step 5: Update comments.html

Replace the placeholder values in `docs/overrides/partials/comments.html`:

```html
data-repo="YOUR_USERNAME/YOUR_REPO"
data-repo-id="YOUR_REPO_ID_HERE"
data-category="Comments"
data-category-id="YOUR_CATEGORY_ID_HERE"
```

Use the values from the Giscus configuration tool (Step 4).

### Step 6: Update mkdocs.yml

Ensure your `mkdocs.yml` includes:

```yaml
theme:
  name: material
  custom_dir: docs/overrides   # Required for theme overrides
  palette:
    - scheme: default          # Light/dark mode for theme sync
      toggle:
        icon: material/brightness-7
        name: Switch to dark mode
    - scheme: slate
      toggle:
        icon: material/brightness-4
        name: Switch to light mode
```

### Step 7: Enable Comments on Pages

Add front matter to any page where you want comments:

```yaml
---
comments: true
---

# Your Page Title

Your content here...
```

## Enable Comments for Entire Folders

Use the [meta plugin](https://squidfunk.github.io/mkdocs-material/plugins/meta/) 
to enable comments for all pages in a folder:

1. Add to `mkdocs.yml`:
   ```yaml
   plugins:
     - meta
   ```

2. Create `.meta.yml` in any folder:
   ```yaml
   comments: true
   ```

All pages in that folder (and subfolders) will have comments enabled.

## Giscus Configuration Options

| Option | Description |
|--------|-------------|
| `data-mapping="pathname"` | Creates one discussion per page URL |
| `data-mapping="title"` | Creates one discussion per page title |
| `data-reactions-enabled="1"` | Allow emoji reactions on comments |
| `data-emit-metadata="1"` | Emit discussion metadata |
| `data-input-position="top"` | Comment box at top (or "bottom") |
| `data-theme="light"` | Initial theme (auto-syncs with palette) |
| `data-loading="lazy"` | Load comments only when scrolled into view |

## Troubleshooting

### Comments not appearing?

- Verify `custom_dir: docs/overrides` is in mkdocs.yml
- Check that the file path is exactly `docs/overrides/partials/comments.html`
- Ensure front matter `comments: true` is at the top of your markdown file
- Confirm Giscus app is installed on your repository

### Theme not syncing?

- Make sure you have both light and dark palette schemes defined
- The JavaScript in comments.html handles the sync automatically

### "Discussion not found" error?

- Verify your Discussion category exists
- Check that category ID matches your configuration
- Ensure the Giscus app has access to your repository

## Resources

- [Giscus Configuration Tool](https://giscus.app/)
- [mkdocs-material Comment System Docs](https://squidfunk.github.io/mkdocs-material/setup/adding-a-comment-system/)
- [Giscus GitHub Repository](https://github.com/giscus/giscus)
