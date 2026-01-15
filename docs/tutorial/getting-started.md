# Getting Started

This tutorial walks you through integrating Giscus comments into your MkDocs Material site. By the end, you'll have a fully functional comment system powered by GitHub Discussions that appears when users click feedback icons.

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

**Option A: Using the GitHub Web Interface**

1. Go to your repository on GitHub (e.g., `https://github.com/yourusername/your-repo`)
2. Click the **Settings** tab (you need to be the repository owner or have admin access)
3. Scroll down to the **Features** section
4. Check the box next to **Discussions**

**Option B: Using the GitHub CLI**

If you have the GitHub CLI installed, you can enable Discussions with a single command:

```bash
gh api --method PATCH repos/yourusername/your-repo -F has_discussions=true
```

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
2. In the left sidebar, click the gear icon next to **Categories**
3. Click **New category**
4. Fill in the details:
    - **Category name:** `Comments`
    - **Description:** `Page comments from the documentation site`
    - **Discussion Format:** Select **Announcement**

!!! tip "Why Announcement format?"
    The Announcement format means only maintainers can create new discussion threads, but anyone can reply. This prevents spam while allowing legitimate comments. Giscus automatically creates threads when users comment on pages.

5. Click **Create**

## Step 4: Get Your Giscus Configuration IDs

Now we need to get the specific IDs for your repository and category.

**Option A: Using the Giscus Web Tool**

1. Visit [giscus.app](https://giscus.app/)
2. Scroll down to the **Configuration** section
3. In the **Repository** field, enter your repository (e.g., `yourusername/your-repo`)
4. If everything is set up correctly, you'll see a green checkmark
5. Under **Discussion Category**, select **Comments** (the category you just created)
6. Scroll to the bottom where you see **Enable giscus**

You'll see a code snippet with values like:

- `data-repo-id="R_kgDOxxxxxx"`
- `data-category-id="DIC_kwDOxxxxxx"`

Copy these values for the next step.

**Option B: Using the GitHub CLI (GraphQL)**

You can also retrieve the category ID using the GitHub CLI:

```bash
gh api graphql -f query='
{
  repository(owner: "yourusername", name: "your-repo") {
    id
    discussionCategories(first: 10) {
      nodes {
        id
        name
      }
    }
  }
}'
```

This returns:

- The repository `id` (use as `data-repo-id`)
- Each category's `id` (use the Comments category ID as `data-category-id`)

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

Create a new file at `docs/overrides/partials/comments.html` with the following content.

This template:

- Hides the Giscus comment form by default
- Shows comments when users click a feedback icon (happy/sad face)
- Automatically syncs the Giscus theme with your site's light/dark mode

```html
{% if page.meta.comments %}
  <!-- Giscus Comment Container - Hidden by default, shown when feedback is clicked -->
  <div id="giscus-container" style="display: none;">
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
  </div>

  <!-- Synchronize Giscus theme with MkDocs Material palette (light/dark mode) -->
  <script>
    var giscus = document.querySelector("script[src*=giscus]")

    // Set palette on initial load
    var palette = __md_get("__palette")
    if (palette && typeof palette.color === "object") {
      var theme = palette.color.scheme === "slate"
        ? "transparent_dark"
        : "light"

      // Instruct Giscus to set theme
      giscus.setAttribute("data-theme", theme)
    }

    // Function to show Giscus comments
    function showGiscusComments() {
      var container = document.getElementById("giscus-container")
      if (container) {
        container.style.display = "block"
        setTimeout(function() {
          container.scrollIntoView({ behavior: "smooth", block: "start" })
        }, 300)
      }
    }

    // Use event capturing to catch feedback clicks
    document.addEventListener("click", function(e) {
      var target = e.target.closest(".md-feedback__icon")
      if (target) {
        showGiscusComments()
      }
    }, true)

    // Also watch for feedback note visibility changes
    document.addEventListener("DOMContentLoaded", function() {
      var observer = new MutationObserver(function(mutations) {
        mutations.forEach(function(mutation) {
          if (mutation.type === "attributes" && mutation.attributeName === "hidden") {
            var target = mutation.target
            if (target.hasAttribute("data-md-value") && !target.hidden) {
              showGiscusComments()
            }
          }
        })
      })

      var feedbackNote = document.querySelector(".md-feedback__note")
      if (feedbackNote) {
        observer.observe(feedbackNote, {
          attributes: true,
          subtree: true,
          attributeFilter: ["hidden"]
        })
      }

      // Handle palette changes for Giscus theme sync
      var paletteRef = document.querySelector("[data-md-component=palette]")
      if (paletteRef) {
        paletteRef.addEventListener("change", function() {
          var palette = __md_get("__palette")
          if (palette && typeof palette.color === "object") {
            var theme = palette.color.scheme === "slate"
              ? "transparent_dark"
              : "light"

            var frame = document.querySelector(".giscus-frame")
            if (frame) {
              frame.contentWindow.postMessage(
                { giscus: { setConfig: { theme } } },
                "https://giscus.app"
              )
            }
          }
        })
      }
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

### Configure the Feedback Widget

Add the feedback widget configuration under `extra`. This creates the happy/sad face icons that trigger the comments to appear:

```yaml
extra:
  analytics:
    feedback:
      title: Was this page helpful?
      ratings:
        - icon: material/emoticon-happy-outline
          name: This page was helpful
          data: 1
          note: >-
            Thanks for your feedback! Feel free to share what you found helpful
            in the comments below
            or <a href="https://github.com/yourusername/your-repo/issues/new"
            target="_blank" rel="noopener">open an issue</a>.
        - icon: material/emoticon-sad-outline
          name: This page could be improved
          data: 0
          note: >-
            Thanks for your feedback! Please share details in the comments below
            or <a href="https://github.com/yourusername/your-repo/issues/new"
            target="_blank" rel="noopener">open an issue</a>.
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
6. Click one of the feedback icons (happy or sad face) at the bottom of the page
7. The Giscus comment form should appear and scroll into view

!!! warning "Comments Won't Fully Work Locally"
    You'll see the Giscus widget appear, but you may not be able to post comments until the site is deployed. This is because Giscus needs to verify the page URL against your repository.

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
3. Click a feedback icon (happy or sad face)
4. The comment form should appear
5. Sign in with your GitHub account
6. Post a test comment
7. Check your repository's **Discussions** tab - you should see a new discussion created

## How It Works

Here's what happens when a user interacts with your site:

1. User reads a page and scrolls to the bottom
2. User sees "Was this page helpful?" with happy/sad face icons
3. User clicks an icon to provide feedback
4. The "Thanks for your feedback!" message appears
5. The Giscus comment form slides into view
6. User can now leave a detailed comment (requires GitHub login)
7. Comments are stored as GitHub Discussions in your repository

This approach:

- **Reduces page load time** - Giscus only fully loads when needed
- **Encourages feedback** - Users provide quick feedback first, then detailed comments
- **Keeps pages clean** - Comment form doesn't clutter the page until requested

## Next Steps

Now that you have comments working:

- Learn how to [control which pages have comments](customizing-comment-pages.md) using front matter and `.meta.yml` files
- Monitor your repository's Discussions tab for new comments
- Customize the Giscus theme to match your brand
- Check the [Debugging Guide](debugging.md) if you run into issues

---

Need help? Check the [Giscus documentation](https://giscus.app/) or [open an issue](https://github.com/dmccreary/giscus-template/issues) on this template's repository.
