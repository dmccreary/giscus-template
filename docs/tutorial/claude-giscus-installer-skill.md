# Using the Giscus Installer Skill

This guide walks you through installing and using the **giscus-installer** Claude skill to add Giscus comments to your MkDocs Material site.

## What is a Claude Skill?

A Claude skill is a reusable set of instructions that teaches Claude Code how to perform a specific task. Skills are stored in markdown files and can be shared across projects. When you invoke a skill, Claude follows the documented workflow to complete the task.

The **giscus-installer** skill automates the process of integrating Giscus comments into an MkDocs Material site, handling everything from retrieving GitHub API credentials to creating the necessary template files.

## Prerequisites

Before using this skill, ensure you have:

1. **Claude Code CLI** installed and working
2. **An MkDocs Material site** in a GitHub repository
3. **GitHub CLI (gh)** installed and authenticated
4. **Python 3.8+** with MkDocs and mkdocs-material installed

## Step 1: Install the Skill

Claude skills are stored in the `~/.claude/skills/` directory. Copy the giscus-installer skill to this location.

### Option A: Copy from This Repository

If you've cloned this template repository:

```bash
# Create the skills directory if it doesn't exist
mkdir -p ~/.claude/skills

# Copy the skill
cp -r skills/giscus-installer ~/.claude/skills/
```

### Option B: Download from GitHub

```bash
# Create the skills directory
mkdir -p ~/.claude/skills

# Clone just the skill (or download manually)
cd ~/.claude/skills
git clone --depth 1 --filter=blob:none --sparse \
  https://github.com/dmccreary/giscus-template.git temp-repo
cd temp-repo
git sparse-checkout set skills/giscus-installer
mv skills/giscus-installer ../
cd ..
rm -rf temp-repo
```

### Verify Installation

Check that the skill is installed correctly:

```bash
ls ~/.claude/skills/giscus-installer/
```

You should see:

```
SKILL.md
assets/
references/
scripts/
```

## Step 2: Prepare Your Repository

Before running the skill, complete these one-time setup tasks on your target repository.

### Enable GitHub Discussions

```bash
# Navigate to your MkDocs project
cd /path/to/your-mkdocs-project

# Enable Discussions via GitHub CLI
gh api --method PATCH repos/OWNER/REPO -F has_discussions=true
```

Replace `OWNER/REPO` with your repository (e.g., `dmccreary/my-textbook`).

### Install the Giscus GitHub App

1. Visit [github.com/apps/giscus](https://github.com/apps/giscus)
2. Click **Install**
3. Select your repository
4. Click **Install**

### Create the Comments Category

1. Go to your repository's **Discussions** tab
2. Click the gear icon next to **Categories**
3. Click **New category**
4. Set:
   - **Name:** `Comments`
   - **Format:** `Announcement`
5. Click **Create**

## Step 3: Start Claude Code

Open your terminal and navigate to your MkDocs project:

```bash
cd /path/to/your-mkdocs-project
claude
```

This starts an interactive Claude Code session in your project directory.

## Step 4: Invoke the Skill

Ask Claude to use the giscus-installer skill:

```
Use the giscus-installer skill to add Giscus comments to this MkDocs site.
```

Or be more specific:

```
Install Giscus comments using the giscus-installer skill. My repository is
dmccreary/my-textbook and I want comments enabled on all chapter pages.
```

## Step 5: Follow Claude's Workflow

Claude will follow the skill's 8-step workflow:

### 5.1 Verify Prerequisites

Claude checks that:

- GitHub Discussions is enabled
- The Giscus app is installed
- MkDocs Material theme is configured

If anything is missing, Claude will guide you through fixing it.

### 5.2 Retrieve Configuration IDs

Claude runs GraphQL queries to get your repository's unique identifiers:

```bash
# Repository ID
gh api repos/OWNER/REPO --jq '.node_id'

# Category ID
gh api graphql -f query='{
  repository(owner: "OWNER", name: "REPO") {
    discussionCategories(first: 10) {
      nodes { id name }
    }
  }
}'
```

### 5.3 Create Directory Structure

Claude creates the theme override directories:

```bash
mkdir -p docs/overrides/partials
```

### 5.4 Create comments.html

Claude creates `docs/overrides/partials/comments.html` with:

- The Giscus script configured with your repository IDs
- JavaScript to show comments when feedback icons are clicked
- Theme synchronization for light/dark mode

### 5.5 Update mkdocs.yml

Claude adds or updates your `mkdocs.yml` with:

- `custom_dir: docs/overrides` under theme
- The `meta` plugin for folder-level settings
- Feedback widget configuration

### 5.6 Enable Comments

Claude helps you choose how to enable comments:

- **All pages:** Creates `docs/.meta.yml` with `comments: true`
- **Specific folders:** Creates `.meta.yml` in target directories
- **Individual pages:** Adds front matter to specific files

### 5.7 Test the Installation

Claude instructs you to:

1. Run `mkdocs serve`
2. Open `http://localhost:8000`
3. Navigate to a page with comments
4. Click a feedback icon
5. Verify the comment form appears

## Step 6: Deploy Your Site

Once testing is successful, deploy to GitHub Pages:

```bash
mkdocs gh-deploy
```

Visit your live site and verify comments work in production.

## Example Session

Here's what a typical session looks like:

```
$ cd ~/projects/my-textbook
$ claude

You: Use the giscus-installer skill to add comments to this site.
     My repo is dmccreary/my-textbook.

Claude: I'll use the giscus-installer skill to add Giscus comments
        to your MkDocs site.

        First, let me verify the prerequisites...

        [Claude checks GitHub Discussions status]

        GitHub Discussions is enabled. Now let me get your
        repository and category IDs...

        [Claude runs GraphQL queries]

        Found:
        - Repository ID: R_kgDOxxxxxx
        - Category ID: DIC_kwDOxxxxxx

        Creating the theme override directory...

        [Claude creates docs/overrides/partials/comments.html]

        Updating mkdocs.yml with custom_dir and feedback widget...

        [Claude updates mkdocs.yml]

        Would you like comments enabled on:
        1. All pages (recommended for textbooks)
        2. Specific folders only
        3. Individual pages only

You: All pages

Claude: Creating docs/.meta.yml with comments: true...

        Installation complete! To test:
        1. Run: mkdocs serve
        2. Open: http://localhost:8000
        3. Click a feedback icon on any page
        4. The comment form should appear

        When ready, deploy with: mkdocs gh-deploy
```

## Customizing After Installation

### Change Which Pages Have Comments

Edit `docs/.meta.yml` or add front matter to individual pages:

```yaml
---
comments: false
---
```

### Modify the Comment Form Appearance

Edit `docs/overrides/partials/comments.html` and change Giscus attributes:

| Attribute | Options |
|-----------|---------|
| `data-theme` | `light`, `dark`, `preferred_color_scheme` |
| `data-input-position` | `top`, `bottom` |
| `data-reactions-enabled` | `1`, `0` |
| `data-lang` | `en`, `es`, `fr`, etc. |

### Add Comments to New Sections

Create a `.meta.yml` file in any folder:

```yaml
# docs/new-section/.meta.yml
comments: true
```

## Troubleshooting

### "Skill not found"

Verify the skill is installed:

```bash
ls ~/.claude/skills/giscus-installer/SKILL.md
```

### "giscus is not installed on this repository"

1. Visit [github.com/apps/giscus](https://github.com/apps/giscus)
2. Click **Configure**
3. Ensure your repository is selected
4. Save

### Comments Don't Appear When Clicking Feedback Icons

Check the browser console (F12) for errors. The most common issues:

1. **Wrong repository ID** - Re-run the GraphQL query
2. **Category doesn't exist** - Create the "Comments" category in Discussions
3. **Front matter missing** - Ensure `comments: true` is set

### Theme Not Syncing

Verify both light and dark palettes are configured in `mkdocs.yml`:

```yaml
theme:
  palette:
    - scheme: default  # Light mode
    - scheme: slate    # Dark mode
```

## Skill Resources

The giscus-installer skill includes:

| File | Purpose |
|------|---------|
| `SKILL.md` | Main skill instructions |
| `assets/comments-template.html` | Base template to copy |
| `references/giscus-config.md` | Full configuration reference |

## Next Steps

- Read [Customizing Comment Pages](customizing-comment-pages.md) for advanced configuration
- Check [Debugging](debugging.md) if you encounter issues
- Explore the [Giscus documentation](https://giscus.app) for more options

## Related Documentation

- [Getting Started](getting-started.md) - Manual installation guide
- [Debugging](debugging.md) - Troubleshooting common issues
- [Customizing Comment Pages](customizing-comment-pages.md) - Control which pages have comments
