# Getting Started

This tutorial walks you through integrating Giscus comments into your MkDocs Material site. By the end, you'll have a fully functional comment system powered by GitHub Discussions.

## Prerequisites

Before you begin, make sure you have:

1. **Python 3.8 or higher** installed on your computer
2. **A GitHub account** (free account works fine)
3. **A GitHub repository** where your MkDocs site lives
4. **Basic familiarity with the command line**

If you don't have MkDocs installed yet, open your terminal and run:

```bash
pip install mkdocs mkdocs-material
```

## Step 1: Enable GitHub Discussions on Your Repository

GitHub Discussions is where Giscus stores all comments. You need to enable it first.

1. Go to your repository on GitHub (e.g., `https://github.com/yourusername/your-repo`)
2. Click the **Settings** tab (you need to be the repository owner or have admin access)
3. Scroll down to the **Features** section
4. Check the box next to **Discussions**

![Enable Discussions](https://docs.github.com/assets/cb-46774/images/help/discussions/enable-or-disable-github-discussions-for-repository.png)

Once enabled, you'll see a new **Discussions** tab appear in your repository's navigation.

## Step 2: Install the Giscus GitHub App

Giscus needs permission to read and write to your repository's Discussions.

1. Visit [github.com/apps/giscus](https://github.com/apps/giscus)
2. Click the green **Install** button
3. Choose whether to install for:
    - **All repositories** (convenient if you have multiple projects)
    - **Only select repositories** (more secure, recommended)
4. If you chose "select repositories," pick your MkDocs repository
5. Click **Install**

You'll be redirected back to GitHub. The Giscus app is now authorized.

## Step 3: Create a Discussion Category

Comments need a home. We'll create a dedicated category for them.

1. Go to your repository's **Discussions** tab
2. In the left sidebar, click the gear icon (⚙️) next to **Categories**
3. Click **New category**
4. Fill in the details:
    - **Category name:** `Comments`
    - **Description:** `Page comments from the documentation site`
    - **Discussion Format:** Select **Announcement**

!!! tip "Why Announcement format?"
    The Announcement format means only maintainers can create new discussion threads, but anyone can reply. This prevents spam while allowing legitimate comments. Giscus automatically creates threads when users comment on pages.

5. Click **Create**

## Step 4: Get Your Giscus Configuration

Now we need to get the specific IDs for your repository and category.

1. Visit [giscus.app](https://giscus.app/)
2. Scroll down to the **Configuration** section
3. In the **Repository** field, enter your repository (e.g., `yourusername/your-repo`)
4. If everything is set up correctly, you'll see a green checkmark
5. Under **Discussion Category**, select **Comments** (the category you just created)
6. Keep scrolling to see the **Page ↔️ Discussions Mapping** section:
    - Select **pathname** (recommended - creates one discussion per page URL)
7. Continue to **Features**:
    - Check **Enable reactions for the main post**
    - Check **Emit discussion metadata**
    - Set **Comment input position** to **top** (users see the comment box first)
8. Scroll to the bottom where you see **Enable giscus**

You'll see a code snippet like this:

```html
<script src="https://giscus.app/client.js"
        data-repo="yourusername/your-repo"
        data-repo-id="R_kgDOxxxxxx"
        data-category="Comments"
        data-category-id="DIC_kwDOxxxxxx"
        ...
</script>
```

**Copy the values for:**

- `data-repo-id` (starts with `R_`)
- `data-category-id` (starts with `DIC_`)

Keep this page open; you'll need these values in the next step.

## Step 5: Create the Theme Override Directory Structure

MkDocs Material uses "theme overrides" to customize templates. We need to create a specific folder structure.

1. In your project's root directory, navigate to the `docs` folder
2. Create the following nested directories:

```bash
mkdir -p docs/overrides/partials
```

Your folder structure should now look like:

```
your-project/
├── docs/
│   ├── overrides/
│   │   └── partials/
│   │       └── (comments.html will go here)
│   ├── index.md
│   └── other-pages.md
└── mkdocs.yml
```

## Step 6: Create the Comments Template

Create a new file at `docs/overrides/partials/comments.html` with the following content:

```html
{% if page.meta.comments %}
  <h2 id="__comments">{{ lang.t("meta.comments") }}</h2>

  <!-- Giscus Comment System -->
  <script
    src="https://giscus.app/client.js"
    data-repo="yourusername/your-repo"
    data-repo-id="YOUR_REPO_ID_HERE"
    data-category="Comments"
    data-category-id="YOUR_CATEGORY_ID_HERE"
    data-mapping="pathname"
    data-strict="0"
    data-reactions-enabled="1"
    data-emit-metadata="1"
    data-input-position="top"
    data-theme="light"
    data-lang="en"
    data-loading="lazy"
    crossorigin="anonymous"
    async
  >
  </script>

  <!-- Synchronize Giscus theme with MkDocs Material palette -->
  <script>
    var giscus = document.querySelector("script[src*=giscus]")

    // Set palette on initial load
    var palette = __md_get("__palette")
    if (palette && typeof palette.color === "object") {
      var theme = palette.color.scheme === "slate"
        ? "transparent_dark"
        : "light"

      giscus.setAttribute("data-theme", theme)
    }

    // Register event handlers after document loaded
    document.addEventListener("DOMContentLoaded", function() {
      var ref = document.querySelector("[data-md-component=palette]")
      ref.addEventListener("change", function() {
        var palette = __md_get("__palette")
        if (palette && typeof palette.color === "object") {
          var theme = palette.color.scheme === "slate"
            ? "transparent_dark"
            : "light"

          // Instruct Giscus to change theme
          var frame = document.querySelector(".giscus-frame")
          frame.contentWindow.postMessage(
            { giscus: { setConfig: { theme } } },
            "https://giscus.app"
          )
        }
      })
    })
  </script>
{% endif %}
```

**Important:** Replace these placeholder values with your actual values from Step 4:

| Placeholder | Replace with |
|-------------|--------------|
| `yourusername/your-repo` | Your GitHub repository path |
| `YOUR_REPO_ID_HERE` | The `data-repo-id` value (starts with `R_`) |
| `YOUR_CATEGORY_ID_HERE` | The `data-category-id` value (starts with `DIC_`) |

## Step 7: Update mkdocs.yml

Open your `mkdocs.yml` file and make these changes:

### Add the Custom Directory

Under the `theme` section, add the `custom_dir` setting:

```yaml
theme:
  name: material
  custom_dir: docs/overrides    # Add this line
```

### Ensure Light/Dark Mode is Configured

For the theme synchronization to work, you need both light and dark palettes defined:

```yaml
theme:
  name: material
  custom_dir: docs/overrides
  palette:
    # Light mode
    - scheme: default
      primary: blue
      accent: orange
      toggle:
        icon: material/brightness-7
        name: Switch to dark mode
    # Dark mode
    - scheme: slate
      primary: blue
      accent: orange
      toggle:
        icon: material/brightness-4
        name: Switch to light mode
```

### Add the Meta Plugin (Optional but Recommended)

The meta plugin lets you enable comments for entire folders at once:

```yaml
plugins:
  - search
  - meta      # Add this line
```

## Step 8: Enable Comments on a Page

To enable comments on any page, add `comments: true` to the front matter at the top of your markdown file:

```markdown
---
comments: true
---

# Your Page Title

Your content goes here...
```

!!! note "Front Matter Must Come First"
    The `---` block must be the very first thing in your file. No blank lines or spaces before it.

## Step 9: Test Locally

Let's make sure everything works before deploying.

1. Open your terminal
2. Navigate to your project directory
3. Run the development server:

```bash
mkdocs serve
```

4. Open your browser to `http://localhost:8000`
5. Navigate to a page where you enabled comments
6. Scroll to the bottom - you should see the Giscus comment widget

!!! warning "Comments Won't Fully Work Locally"
    You'll see the Giscus widget, but you may not be able to post comments until the site is deployed. This is because Giscus needs to verify the page URL against your repository.

## Step 10: Deploy to GitHub Pages

When you're ready to go live:

```bash
mkdocs gh-deploy
```

This command:

1. Builds your site
2. Creates (or updates) a `gh-pages` branch
3. Pushes the built site to that branch

After a few minutes, your site will be live at `https://yourusername.github.io/your-repo/`

## Step 11: Verify Comments Work

1. Visit your deployed site
2. Navigate to a page with comments enabled
3. Sign in with your GitHub account
4. Post a test comment
5. Check your repository's **Discussions** tab - you should see a new discussion created

## Enabling Comments for Entire Folders

Instead of adding `comments: true` to every page, you can enable comments for all pages in a folder.

1. Make sure the `meta` plugin is in your `mkdocs.yml`
2. Create a file called `.meta.yml` in the folder:

```yaml
comments: true
```

All pages in that folder (and subfolders) will now have comments enabled automatically.

**Example structure:**

```
docs/
├── chapters/
│   ├── .meta.yml          # Contains: comments: true
│   ├── chapter-1.md       # Has comments (inherited)
│   ├── chapter-2.md       # Has comments (inherited)
│   └── chapter-3.md       # Has comments (inherited)
└── index.md               # No comments (not in the folder)
```

## Troubleshooting

### Comments Section Doesn't Appear

- Verify `custom_dir: docs/overrides` is in your `mkdocs.yml`
- Check file path is exactly `docs/overrides/partials/comments.html`
- Confirm `comments: true` is in the page's front matter
- Make sure front matter is at the very top of the file

### "Discussion Not Found" Error

- Verify your Discussion category exists and is named exactly as configured
- Check that the category ID matches your configuration
- Ensure Giscus app has access to your repository

### Theme Not Syncing

- Make sure you have both `default` and `slate` schemes defined in `mkdocs.yml`
- The theme sync JavaScript requires both palette options

### Can't Post Comments Locally

This is expected. Giscus validates the page URL against your repository settings. Deploy your site to test commenting functionality.

## Next Steps

Now that you have comments working:

- Enable comments on your most important pages
- Consider adding the [feedback widget](../sample-page.md) to gather quick reactions
- Monitor your repository's Discussions tab for new comments
- Customize the Giscus theme to match your brand

---

Need help? Check the [Giscus documentation](https://giscus.app/) or [open an issue](https://github.com/dmccreary/giscus-template/issues) on this template's repository.
