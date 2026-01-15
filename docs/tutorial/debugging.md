# Debugging Guide

This guide helps you troubleshoot common issues when integrating Giscus comments with MkDocs Material.

## Quick Diagnostic Checklist

Before diving into specific issues, run through this checklist:

- [ ] Is `custom_dir: docs/overrides` in your `mkdocs.yml`?
- [ ] Does `docs/overrides/partials/comments.html` exist?
- [ ] Does your page have `comments: true` in the front matter?
- [ ] Is the Giscus app installed on your repository?
- [ ] Are GitHub Discussions enabled on your repository?
- [ ] Does your Comments category exist in Discussions?

## Common Issues

### "Giscus is not installed on this repository"

**Symptoms:** Error message appears where comments should be.

**Causes and Solutions:**

1. **Giscus app not installed**
    - Visit [github.com/apps/giscus](https://github.com/apps/giscus)
    - Click **Install** and select your repository

2. **Wrong repository in comments.html**
    - Check that `data-repo` matches your actual repository
    - Format should be `username/repo-name` (e.g., `dmccreary/giscus-template`)

3. **Repository ID mismatch**
    - Get the correct ID from [giscus.app](https://giscus.app/) or via CLI:
    ```bash
    gh api graphql -f query='{repository(owner:"USERNAME",name:"REPO"){id}}'
    ```

### Comments Section Doesn't Appear

**Symptoms:** No comment section visible on the page.

**Debugging Steps:**

1. **Check the browser console** (F12 → Console tab)

    Look for JavaScript errors. Common ones:
    - `__md_get is not defined` - Material theme not loading properly
    - `Cannot read property of null` - Element selectors failing

2. **Verify the template path**

    The file must be at exactly:
    ```
    docs/overrides/partials/comments.html
    ```

3. **Check front matter**

    Open your markdown file and verify:
    ```yaml
    ---
    comments: true
    ---
    ```

    The `---` must be the very first line with no spaces before it.

4. **Inspect the HTML**

    Right-click on the page → Inspect. Search for:
    - `giscus-container` - Should exist if template loaded
    - `giscus-frame` - Should exist if Giscus loaded

### Feedback Icons Don't Trigger Comments

**Symptoms:** Clicking happy/sad face doesn't show the comment form.

**Debugging Steps:**

1. **Check if feedback widget exists**

    Open browser console and run:
    ```javascript
    document.querySelector(".md-feedback")
    ```

    If this returns `null`, the feedback widget isn't configured in `mkdocs.yml`.

2. **Check feedback button structure**

    Run in console:
    ```javascript
    document.querySelectorAll(".md-feedback__icon")
    ```

    Should return a NodeList with 2 elements (happy and sad icons).

3. **Verify the fieldset isn't permanently disabled**

    Inspect the feedback widget HTML. Look for:
    ```html
    <fieldset disabled="">
    ```

    This is normal - Material for MkDocs enables it when ready.

4. **Test the click handler manually**

    Run in console:
    ```javascript
    document.getElementById("giscus-container").style.display = "block"
    ```

    If the comments appear, the issue is with the click event listener.

5. **Check for event capturing**

    The click listener must use event capturing (third parameter `true`):
    ```javascript
    document.addEventListener("click", handler, true)
    ```

    This is necessary because the feedback fieldset is disabled.

### Theme Not Syncing (Light/Dark Mode)

**Symptoms:** Comments stay light when you switch to dark mode (or vice versa).

**Causes and Solutions:**

1. **Missing palette configuration**

    Both schemes must be defined in `mkdocs.yml`:
    ```yaml
    palette:
      - scheme: default    # Light mode
        toggle:
          icon: material/brightness-7
      - scheme: slate      # Dark mode
        toggle:
          icon: material/brightness-4
    ```

2. **Palette selector not found**

    The JavaScript looks for `[data-md-component=palette]`. If your theme customization removed this, the sync won't work.

3. **Giscus iframe not loaded**

    The theme sync only works after Giscus loads. Check console for:
    ```javascript
    document.querySelector(".giscus-frame")
    ```

### "Discussion not found" Error

**Symptoms:** Giscus loads but shows "Discussion not found".

**Causes and Solutions:**

1. **Category doesn't exist**
    - Go to your repository's Discussions tab
    - Verify the "Comments" category exists
    - Category name must match `data-category` exactly

2. **Category ID mismatch**

    Get the correct category ID:
    ```bash
    gh api graphql -f query='
    {
      repository(owner: "USERNAME", name: "REPO") {
        discussionCategories(first: 10) {
          nodes {
            id
            name
          }
        }
      }
    }'
    ```

3. **Strict mode enabled**

    If `data-strict="1"`, Giscus requires an exact URL match. Try setting:
    ```html
    data-strict="0"
    ```

### Comments Work Locally but Not When Deployed

**Symptoms:** Everything works on `localhost:8000` but fails on GitHub Pages.

**Causes and Solutions:**

1. **URL mismatch**

    Giscus validates the page URL. Make sure your `site_url` in `mkdocs.yml` matches your deployed URL:
    ```yaml
    site_url: https://username.github.io/repo-name/
    ```

2. **Cache issues**

    Hard refresh the page: `Ctrl+Shift+R` (Windows/Linux) or `Cmd+Shift+R` (Mac)

3. **GitHub Pages not updated**

    Changes can take a few minutes to propagate. Check the Actions tab for deployment status.

## Using Browser Developer Tools

### Console Commands for Debugging

**Check if Giscus container exists:**
```javascript
document.getElementById("giscus-container")
```

**Check if Giscus iframe loaded:**
```javascript
document.querySelector(".giscus-frame")
```

**Check feedback widget:**
```javascript
document.querySelector(".md-feedback")
```

**List all feedback icons:**
```javascript
document.querySelectorAll(".md-feedback__icon")
```

**Manually show comments:**
```javascript
document.getElementById("giscus-container").style.display = "block"
```

**Check current palette:**
```javascript
__md_get("__palette")
```

### Network Tab Debugging

1. Open Developer Tools (F12)
2. Go to the **Network** tab
3. Reload the page
4. Filter by "giscus"
5. Check if `client.js` loads successfully (status 200)

### Elements Tab Debugging

1. Open Developer Tools (F12)
2. Go to the **Elements** tab
3. Search for `giscus` (Ctrl+F)
4. Verify the script tag has correct attributes
5. Check if `giscus-frame` iframe exists

## Verifying Your Configuration

### Test GitHub Discussions Access

```bash
# Check if discussions are enabled
gh api repos/USERNAME/REPO --jq '.has_discussions'

# Should return: true
```

### Test Giscus App Installation

```bash
# List installed apps (look for giscus)
gh api repos/USERNAME/REPO/installation --jq '.id'
```

### Get All Configuration Values

```bash
gh api graphql -f query='
{
  repository(owner: "USERNAME", name: "REPO") {
    id
    name
    hasDiscussionsEnabled
    discussionCategories(first: 10) {
      nodes {
        id
        name
        slug
      }
    }
  }
}'
```

## Still Having Issues?

If you've tried everything above and still have problems:

1. **Check the Giscus documentation:** [giscus.app](https://giscus.app/)
2. **Search existing issues:** [giscus/giscus/issues](https://github.com/giscus/giscus/issues)
3. **Open an issue on this template:** [dmccreary/giscus-template/issues](https://github.com/dmccreary/giscus-template/issues)

When reporting an issue, include:

- Browser and version
- Error messages from the console
- Relevant configuration from `mkdocs.yml`
- The `comments.html` file contents
- Screenshots if applicable
