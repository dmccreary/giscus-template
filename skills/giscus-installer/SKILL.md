---
name: giscus-installer
description: Install and configure Giscus comment system into MkDocs Material sites. Use this skill when adding GitHub Discussions-based comments to intelligent textbooks or documentation sites built with MkDocs Material theme.
---

# Giscus Installer

Install the Giscus comment system into an existing MkDocs Material site, enabling GitHub Discussions-based comments on documentation pages.

## Overview

This skill automates the installation of Giscus, a comment system powered by GitHub Discussions. It creates the necessary theme overrides, configures the comments.html template, and updates mkdocs.yml to enable comments across your site.

**Key capabilities:**

- Retrieves GitHub repository and category IDs via GraphQL API
- Creates the comments.html theme override template
- Configures comments to appear only when feedback icons are clicked
- Updates mkdocs.yml with required plugins and theme settings
- Supports per-page and per-folder comment customization

## Prerequisites

Before running this skill, ensure:

1. **GitHub Discussions enabled** on the repository
2. **Giscus app installed** on the repository at https://github.com/apps/giscus
3. **MkDocs Material theme** is being used
4. **gh CLI** is installed and authenticated

## Workflow

### Step 1: Verify Prerequisites

Check that GitHub Discussions is enabled:

```bash
gh api repos/{owner}/{repo} --jq '.has_discussions'
```

If false, enable it:

```bash
gh api --method PATCH repos/{owner}/{repo} -F has_discussions=true
```

Verify the Giscus app is installed by visiting:
`https://github.com/apps/giscus` and checking the repository is listed.

### Step 2: Create Comments Category

Create a "Comments" category in GitHub Discussions if it doesn't exist:

1. Go to `https://github.com/{owner}/{repo}/discussions/categories`
2. Click "New category"
3. Name: `Comments`
4. Discussion Format: `Announcement` (recommended for moderation)
5. Click "Create"

### Step 3: Retrieve Repository Configuration

Get the repository ID:

```bash
gh api repos/{owner}/{repo} --jq '.node_id'
```

Get the category ID using GraphQL:

```bash
gh api graphql -f query='
{
  repository(owner: "{owner}", name: "{repo}") {
    discussionCategories(first: 10) {
      nodes {
        id
        name
      }
    }
  }
}'
```

Find the `id` where `name` is "Comments".

### Step 4: Create Directory Structure

Create the theme overrides directory:

```bash
mkdir -p docs/overrides/partials
```

### Step 5: Create comments.html

Create `docs/overrides/partials/comments.html` with the following content:

```html
{% if page.meta.comments %}
  <div id="giscus-container" style="display: none;">
    <h2 id="__comments">{{ lang.t("meta.comments") }}</h2>
    <script
      src="https://giscus.app/client.js"
      data-repo="{owner}/{repo}"
      data-repo-id="{REPO_ID}"
      data-category="Comments"
      data-category-id="{CATEGORY_ID}"
      data-mapping="pathname"
      data-strict="0"
      data-reactions-enabled="1"
      data-emit-metadata="0"
      data-input-position="top"
      data-theme="preferred_color_scheme"
      data-lang="en"
      crossorigin="anonymous"
      async
    ></script>
  </div>

  <script>
    function showGiscusComments() {
      var container = document.getElementById("giscus-container");
      if (container) {
        container.style.display = "block";
        container.scrollIntoView({ behavior: "smooth", block: "start" });
      }
    }

    // Use event capturing to catch clicks before Material's disabled fieldset blocks them
    document.addEventListener("click", function(e) {
      var target = e.target.closest(".md-feedback__icon");
      if (target) {
        showGiscusComments();
      }
    }, true);

    // Also watch for feedback note becoming visible (backup trigger)
    var observer = new MutationObserver(function(mutations) {
      mutations.forEach(function(mutation) {
        if (mutation.type === "attributes" && mutation.attributeName === "data-md-visible") {
          var note = mutation.target;
          if (note.hasAttribute("data-md-visible")) {
            showGiscusComments();
          }
        }
      });
    });

    var notes = document.querySelectorAll(".md-feedback__note");
    notes.forEach(function(note) {
      observer.observe(note, { attributes: true });
    });
  </script>
{% endif %}
```

Replace `{owner}/{repo}`, `{REPO_ID}`, and `{CATEGORY_ID}` with actual values.

### Step 6: Update mkdocs.yml

Add or update these sections in mkdocs.yml:

```yaml
theme:
  name: material
  custom_dir: docs/overrides

plugins:
  - meta  # Enables .meta.yml files for folder-level settings

extra:
  analytics:
    feedback:
      title: Was this page helpful?
      ratings:
        - icon: material/emoticon-happy-outline
          name: This page was helpful
          data: 1
          note: >-
            Thanks for your feedback! Help us improve by
            <a href="#__comments">adding a comment below</a>.
        - icon: material/emoticon-sad-outline
          name: This page could be improved
          data: 0
          note: >-
            Thanks for your feedback! Help us improve by
            <a href="#__comments">adding a comment below</a>.
```

### Step 7: Enable Comments on Pages

**Option A: Enable by default for all pages**

Create `docs/.meta.yml`:

```yaml
comments: true
```

**Option B: Enable per-page**

Add front matter to individual pages:

```yaml
---
comments: true
---
```

**Option C: Disable on specific pages**

Add front matter to exclude pages:

```yaml
---
comments: false
---
```

### Step 8: Test the Installation

1. Start local server: `mkdocs serve`
2. Navigate to a page with comments enabled
3. Click a feedback icon (happy or sad face)
4. Verify the Giscus comment form appears
5. Submit a test comment and verify it appears in GitHub Discussions

## Configuration Reference

### comments.html Options

| Attribute | Description | Options |
|-----------|-------------|---------|
| data-mapping | How to map pages to discussions | `pathname`, `url`, `title`, `og:title`, `specific`, `number` |
| data-reactions-enabled | Enable reactions on comments | `1` (enabled), `0` (disabled) |
| data-input-position | Where comment input appears | `top`, `bottom` |
| data-theme | Color theme | `light`, `dark`, `preferred_color_scheme`, `transparent_dark` |
| data-lang | UI language | `en`, `es`, `fr`, etc. |

### Hiding Comments on Specific Sections

Use `.meta.yml` in subdirectories:

```
docs/
├── .meta.yml          # comments: true (default)
├── chapters/
│   └── .meta.yml      # comments: true
├── admin/
│   └── .meta.yml      # comments: false
└── drafts/
    └── .meta.yml      # comments: false
```

## Troubleshooting

### "giscus is not installed on this repository"

1. Visit https://github.com/apps/giscus
2. Click "Configure"
3. Select the repository
4. Save

### Comments not appearing when clicking feedback icons

Check browser console for errors. The Material theme uses a disabled fieldset that can block events. The provided comments.html uses event capturing to work around this.

### Wrong discussion category

Verify the category ID matches your "Comments" category:

```bash
gh api graphql -f query='{ repository(owner: "{owner}", name: "{repo}") { discussionCategories(first: 10) { nodes { id name } } } }'
```

### Comments showing on wrong pages

Check for conflicting `.meta.yml` files or front matter. Page-level front matter overrides folder-level `.meta.yml`.

## Resources

- **assets/comments-template.html**: Base template for comments.html
- **references/giscus-config.md**: Full Giscus configuration documentation

## References

- [Giscus Documentation](https://giscus.app)
- [MkDocs Material Comments](https://squidfunk.github.io/mkdocs-material/setup/adding-a-comment-system/)
- [GitHub Discussions](https://docs.github.com/en/discussions)
