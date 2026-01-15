# Customizing Which Pages Have Comments

This guide covers the different ways to control whether comments appear on your pages. You can enable or disable comments at the page level, folder level, or combine both approaches.

## Three Methods for Controlling Comments

| Method | Best For |
|--------|----------|
| **Page Front Matter** | Individual pages, quick overrides |
| **Folder .meta.yml** | Entire sections, chapters, or folders |
| **Combined Approach** | Folder-wide defaults with exceptions |

## Method 1: Page Front Matter

Add `comments: true` or `comments: false` to the YAML front matter at the top of any markdown file.

### Enabling Comments on a Page

```markdown
---
comments: true
---

# Page with Comments

Content here...
```

### Disabling Comments on a Page

```markdown
---
comments: false
---

# Page without Comments

Content here...
```

!!! note "Front Matter Must Come First"
    The `---` block must be the very first thing in your file. No blank lines or spaces before it.

### Multiple Front Matter Options

You can combine `comments` with other front matter options:

```markdown
---
title: My Custom Title
comments: true
hide:
  - navigation
---

# Page Content
```

## Method 2: Folder-Level with .meta.yml

Instead of adding front matter to every page, you can enable (or disable) comments for all pages in a folder using a `.meta.yml` file.

### Prerequisites

Make sure the `meta` plugin is in your `mkdocs.yml`:

```yaml
plugins:
  - search
  - meta
```

### Creating a .meta.yml File

Create a file called `.meta.yml` in the folder where you want to control comments:

```yaml
# .meta.yml
comments: true
```

All pages in that folder (and subfolders) will inherit this setting.

### Example: Enable Comments for All Chapters

```
docs/
├── chapters/
│   ├── .meta.yml          # comments: true
│   ├── chapter-1.md       # Has comments (inherited)
│   ├── chapter-2.md       # Has comments (inherited)
│   └── chapter-3.md       # Has comments (inherited)
├── index.md               # No comments (no .meta.yml here)
└── about.md               # No comments
```

### Example: Disable Comments Site-Wide

Place a `.meta.yml` in the `docs/` root to set a site-wide default:

```
docs/
├── .meta.yml              # comments: false (site-wide default)
├── index.md               # No comments
├── about.md               # No comments
└── contact.md             # No comments
```

## Method 3: Combining Both (Override Pattern)

Page front matter **always overrides** folder-level `.meta.yml` settings. This powerful pattern lets you:

- Enable comments for a folder, then disable on specific pages
- Disable comments for a folder, then enable on specific pages

### Example: Enable Folder-Wide, Disable Specific Pages

Use case: You want comments on all chapter pages except the solutions page.

```
docs/
├── chapters/
│   ├── .meta.yml          # comments: true (folder default)
│   ├── chapter-1.md       # Has comments (inherited)
│   ├── chapter-2.md       # Has comments (inherited)
│   ├── chapter-3.md       # Has comments (inherited)
│   └── solutions.md       # No comments (front matter override)
└── index.md               # No comments (not in the folder)
```

**chapters/.meta.yml:**
```yaml
comments: true
```

**chapters/solutions.md:**
```markdown
---
comments: false
---

# Solutions

This page has no comments to avoid spoilers...
```

### Example: Disable Folder-Wide, Enable Specific Pages

Use case: Most reference pages don't need comments, but a few important ones do.

```
docs/
├── reference/
│   ├── .meta.yml          # comments: false (folder default)
│   ├── api-overview.md    # Has comments (front matter override)
│   ├── function-list.md   # No comments (inherited)
│   └── constants.md       # No comments (inherited)
```

**reference/.meta.yml:**
```yaml
comments: false
```

**reference/api-overview.md:**
```markdown
---
comments: true
---

# API Overview

This important page has comments enabled...
```

## .meta.yml Inheritance Rules

The `.meta.yml` file follows these inheritance rules:

| Rule | Behavior |
|------|----------|
| **Inheritance** | Settings apply to all pages in the folder and subfolders |
| **Override** | Page front matter always overrides `.meta.yml` |
| **Nested folders** | A `.meta.yml` in a subfolder overrides the parent folder's settings |
| **No .meta.yml** | Pages default to no comments unless front matter specifies |

### Example: Nested Folder Inheritance

```
docs/
├── .meta.yml              # comments: false (site-wide default)
├── index.md               # No comments (inherited from docs/)
├── about.md               # No comments (inherited from docs/)
└── chapters/
    ├── .meta.yml          # comments: true (overrides parent)
    ├── intro.md           # Has comments (inherited from chapters/)
    └── advanced/
        ├── .meta.yml      # comments: false (overrides chapters/)
        └── secrets.md     # No comments (inherited from advanced/)
```

In this example:

1. **docs/.meta.yml** sets `comments: false` as the site default
2. **chapters/.meta.yml** overrides this to `comments: true` for all chapter content
3. **chapters/advanced/.meta.yml** overrides again to `comments: false` for sensitive content

## Quick Reference

| Scenario | Solution |
|----------|----------|
| Enable comments on one page | Add `comments: true` to front matter |
| Disable comments on one page | Add `comments: false` to front matter |
| Enable comments for a folder | Create `.meta.yml` with `comments: true` |
| Disable comments for a folder | Create `.meta.yml` with `comments: false` |
| Enable folder-wide, disable one page | `.meta.yml` with `true`, page front matter with `false` |
| Disable folder-wide, enable one page | `.meta.yml` with `false`, page front matter with `true` |

## Common Patterns

### Pattern: Comments Only on Content Pages

Enable comments only on chapters, not on index or utility pages:

```
docs/
├── index.md               # No comments
├── about.md               # No comments
├── glossary.md            # No comments
├── chapters/
│   ├── .meta.yml          # comments: true
│   └── *.md               # All have comments
└── appendix/
    ├── .meta.yml          # comments: true
    └── *.md               # All have comments
```

### Pattern: Comments Everywhere Except Specific Pages

Enable comments site-wide, disable on specific pages:

```
docs/
├── .meta.yml              # comments: true (site-wide)
├── index.md               # Has comments
├── chapters/
│   └── *.md               # All have comments
├── license.md             # No comments (front matter: comments: false)
└── privacy.md             # No comments (front matter: comments: false)
```

### Pattern: Gradual Rollout

Start with comments on a few pages, expand later:

**Phase 1:** Enable on specific pages only (use front matter)
**Phase 2:** Enable for entire sections (add .meta.yml files)
**Phase 3:** Enable site-wide (add docs/.meta.yml)

## Troubleshooting

### Comments Not Appearing

1. Check that `comments: true` is set (either in front matter or .meta.yml)
2. Verify the `meta` plugin is in your `mkdocs.yml` (required for .meta.yml)
3. Ensure front matter is at the very top of the file
4. Check for typos: `comments` not `comment`

### .meta.yml Not Working

1. Verify filename is exactly `.meta.yml` (with the leading dot)
2. Check that the `meta` plugin is listed in `mkdocs.yml`:
   ```yaml
   plugins:
     - search
     - meta
   ```
3. Restart `mkdocs serve` after adding .meta.yml files

### Override Not Working

1. Front matter must be valid YAML (check for syntax errors)
2. The `---` delimiters must be on their own lines
3. No spaces before the opening `---`

## Related Documentation

- [Getting Started](getting-started.md) - Initial setup guide
- [Debugging](debugging.md) - Troubleshooting common issues
- [MkDocs Material Meta Plugin](https://squidfunk.github.io/mkdocs-material/plugins/meta/) - Official documentation
