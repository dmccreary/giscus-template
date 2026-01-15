# Creating the giscus-installer Skill

**Date:** January 2025
**Purpose:** Document the process of creating a reusable Claude skill for installing Giscus into MkDocs Material sites

## Background

After successfully integrating Giscus (GitHub Discussions-based comments) into the giscus-template repository, the goal was to create a reusable skill that could automate this installation process for other MkDocs Material intelligent textbook projects.

## Prerequisites

Before creating the skill, the Anthropic skill-creator skill was installed:

```bash
# Skill-creator installed from:
# https://github.com/anthropics/skills/tree/main/skills/skill-creator
# Installed to: ~/.claude/skills/skill-creator
```

The skill-creator provides:
- `init_skill.py` - Initialize new skill directory structure
- `quick_validate.py` - Validate skill structure and frontmatter
- `package_skill.py` - Package skills for distribution
- Reference documentation on workflows and output patterns

## Step 1: Initialize the Skill Structure

Used the init_skill.py script to create the directory structure:

```bash
python3 ~/.claude/skills/skill-creator/scripts/init_skill.py giscus-installer --path skills
```

This created:
```
skills/giscus-installer/
├── SKILL.md           # Template with TODOs
├── scripts/
│   └── example.py     # Placeholder script
├── references/
│   └── example.md     # Placeholder reference
└── assets/
    └── README.md      # Placeholder asset docs
```

## Step 2: Write the SKILL.md

Replaced the template SKILL.md with comprehensive installation instructions:

### Frontmatter
```yaml
---
name: giscus-installer
description: Install and configure Giscus comment system into MkDocs Material sites. Use this skill when adding GitHub Discussions-based comments to intelligent textbooks or documentation sites built with MkDocs Material theme.
---
```

### Key Sections

1. **Overview** - What the skill does and key capabilities
2. **Prerequisites** - Requirements before running (GitHub Discussions enabled, Giscus app installed, gh CLI)
3. **Workflow** - 8-step installation process:
   - Step 1: Verify Prerequisites
   - Step 2: Create Comments Category
   - Step 3: Retrieve Repository Configuration (repo ID, category ID via GraphQL)
   - Step 4: Create Directory Structure
   - Step 5: Create comments.html
   - Step 6: Update mkdocs.yml
   - Step 7: Enable Comments on Pages
   - Step 8: Test the Installation
4. **Configuration Reference** - All Giscus options and .meta.yml patterns
5. **Troubleshooting** - Common issues and solutions

## Step 3: Create the Comments Template Asset

Created `assets/comments-template.html` with the working Giscus integration code:

Key features of the template:
- Hidden by default (`style="display: none;"`)
- Uses event capturing to detect feedback icon clicks (workaround for Material's disabled fieldset)
- MutationObserver as backup trigger when feedback note becomes visible
- Smooth scroll to comments when revealed

```html
{% if page.meta.comments %}
  <div id="giscus-container" style="display: none;">
    <!-- Giscus script with placeholder values -->
  </div>
  <script>
    // Event capturing + MutationObserver for feedback triggers
  </script>
{% endif %}
```

## Step 4: Create the Configuration Reference

Created `references/giscus-config.md` with:
- Required attributes table
- Optional attributes with all possible values
- GraphQL query for obtaining category ID
- Example configurations (basic and full-featured)
- JavaScript API for dynamic theme changes and message handling

## Step 5: Clean Up Template Files

Removed placeholder files:
- `scripts/example.py`
- `references/example.md`

Updated `assets/README.md` with actual usage instructions.

## Step 6: Validate the Skill

```bash
python3 ~/.claude/skills/skill-creator/scripts/quick_validate.py skills/giscus-installer
# Output: Skill is valid!
```

## Final Skill Structure

```
skills/giscus-installer/
├── SKILL.md                           # Main skill (8-step workflow)
├── assets/
│   ├── README.md                      # Asset documentation
│   └── comments-template.html         # Base template for comments.html
└── references/
    └── giscus-config.md               # Complete Giscus configuration reference
```

## Key Technical Decisions

### 1. Hidden by Default Pattern
The Giscus form is hidden initially and only appears when users click feedback icons. This keeps the page clean while still providing easy access to comments.

### 2. Event Capturing Workaround
MkDocs Material wraps feedback icons in a disabled fieldset, which blocks normal click events. The solution uses event capturing (third parameter `true` in addEventListener) to intercept clicks before they're blocked:

```javascript
document.addEventListener("click", function(e) {
  var target = e.target.closest(".md-feedback__icon");
  if (target) { showGiscusComments(); }
}, true);  // true = capturing phase
```

### 3. GraphQL for Category ID
The category ID isn't available via REST API, so the skill documents the GraphQL query:

```bash
gh api graphql -f query='
{
  repository(owner: "OWNER", name: "REPO") {
    discussionCategories(first: 10) {
      nodes { id name }
    }
  }
}'
```

### 4. .meta.yml Support
The skill documents both per-page frontmatter and folder-level .meta.yml configuration, requiring the `meta` plugin in mkdocs.yml.

## Usage

To use this skill on a new MkDocs project:

1. Ensure prerequisites are met (GitHub Discussions, Giscus app, gh CLI)
2. Follow the 8-step workflow in SKILL.md
3. Use `assets/comments-template.html` as the base for your comments.html
4. Refer to `references/giscus-config.md` for customization options

## Lessons Learned

1. **Skill structure matters** - The init_skill.py script ensures consistent structure
2. **Include working examples** - The comments-template.html provides copy-paste starting point
3. **Document workarounds** - The event capturing fix for Material theme is non-obvious
4. **Validation is helpful** - quick_validate.py catches frontmatter issues early
5. **References complement instructions** - Separating configuration reference from workflow keeps SKILL.md focused

## Related Files

- Working implementation: `docs/overrides/partials/comments.html`
- Site configuration: `mkdocs.yml`
- Tutorial documentation: `docs/tutorial/`
