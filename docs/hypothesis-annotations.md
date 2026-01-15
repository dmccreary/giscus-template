# Adding Hypothesis Annotations to MkDocs

[Hypothesis](https://web.hypothes.is/) is an open-source annotation tool that allows users to highlight text and add comments directly on any web page. This guide walks you through adding Hypothesis to your MkDocs Material site.

## What is Hypothesis?

Hypothesis enables collaborative annotation of web content. Users can:

- Highlight specific text passages
- Add notes and comments to highlights
- Reply to other users' annotations
- Create public or private annotation groups
- Search and organize annotations

This is particularly valuable for educational content where students can:

- Ask questions about specific sentences
- Share insights with classmates
- Collaborate on peer review
- Point out exactly where they got confused

## How Hypothesis Differs from Giscus

| Feature | Hypothesis | Giscus |
|---------|------------|--------|
| Annotation target | Specific text selections | Entire page |
| Location | Inline sidebar | Bottom of page |
| Storage | Hypothesis servers | GitHub Discussions |
| Account required | Hypothesis account | GitHub account |
| Visibility | Public, private, or group | Public |
| Best for | Precise, contextual feedback | General discussion |

**Recommendation:** Use both together. Hypothesis for pointing out specific confusing text, Giscus for general questions and discussions.

## Installation Methods

There are two ways to enable Hypothesis on your site:

| Method | Pros | Cons |
|--------|------|------|
| **Publisher approach** | Works for all visitors automatically | You control the integration |
| **Browser extension** | No site changes needed | Each user must install extension |

This guide covers the **Publisher approach**, which enables Hypothesis for all visitors.

## Step 1: Create the Hypothesis JavaScript File

Create a new directory and file for the Hypothesis integration.

### Create the Directory

```bash
mkdir -p docs/javascripts
```

### Create the JavaScript File

Create `docs/javascripts/hypothesis.js` with the following content:

```javascript
// Hypothesis Annotation Integration for MkDocs Material
// This script loads the Hypothesis sidebar for all visitors

document$.subscribe(function() {
    // Check if Hypothesis is already loaded to avoid duplicates
    if (typeof window.hypothesisConfig === 'undefined') {

        // Create the Hypothesis embed script
        var script = document.createElement('script');
        script.src = 'https://hypothes.is/embed.js';
        script.async = true;

        // Append to document head
        document.head.appendChild(script);

        // Set configuration (optional)
        window.hypothesisConfig = function() {
            return {
                // Uncomment and modify options as needed:

                // Collapse sidebar by default (users click to open)
                // showHighlights: false,

                // Open sidebar automatically
                // openSidebar: true,

                // Focus on a specific annotation group
                // group: 'YOUR_GROUP_ID',

                // Branding options
                // branding: {
                //     appBackgroundColor: 'white',
                //     ctaBackgroundColor: '#3b5998',
                //     ctaTextColor: 'white',
                //     selectionFontFamily: 'helvetica, arial, sans serif'
                // }
            };
        };
    }
});
```

!!! note "Why use document$.subscribe()?"
    MkDocs Material uses "instant loading" which doesn't trigger normal page load events. The `document$` observable from Material ensures the script runs correctly when navigating between pages.

## Step 2: Update mkdocs.yml

Add the JavaScript file to your `mkdocs.yml` configuration:

```yaml
extra_javascript:
  - javascripts/hypothesis.js
```

If you already have `extra_javascript` entries, add it to the list:

```yaml
extra_javascript:
  - js/extra.js
  - javascripts/hypothesis.js
```

## Step 3: Test Locally

1. Start your development server:

    ```bash
    mkdocs serve
    ```

2. Open your browser to `http://localhost:8000`

3. Look for the Hypothesis sidebar on the right side of the page (a small arrow or tab)

4. Click to expand the sidebar

5. Try highlighting some text - you should see an option to annotate

## Step 4: Create a Hypothesis Account (Optional but Recommended)

To moderate or view annotations, create a Hypothesis account:

1. Visit [hypothes.is/signup](https://hypothes.is/signup)
2. Create an account
3. Verify your email

As the site owner, you can:

- View all public annotations on your site
- Create private groups for your class or team
- Moderate discussions

## Configuration Options

### Basic Configuration

The `hypothesisConfig` function can return various options:

```javascript
window.hypothesisConfig = function() {
    return {
        // Show highlights on page load
        showHighlights: true,

        // Open sidebar automatically when page loads
        openSidebar: false,

        // Focus on a specific group's annotations
        // group: '__world__',  // Public annotations
        // group: 'YOUR_GROUP_ID',  // Private group
    };
};
```

### Highlight Visibility

Control whether highlights are visible by default:

```javascript
// Show all highlights immediately
showHighlights: true

// Hide highlights until user opens sidebar
showHighlights: false
```

### Sidebar Behavior

Control the sidebar on page load:

```javascript
// Sidebar closed by default (recommended)
openSidebar: false

// Sidebar open automatically
openSidebar: true
```

### Private Groups

Create a private annotation group for your class:

1. Log in to [hypothes.is](https://hypothes.is)
2. Click your username → "Create new group"
3. Name your group and set permissions
4. Copy the group ID from the URL (e.g., `https://hypothes.is/groups/abc123/my-group` → `abc123`)
5. Add to your configuration:

```javascript
window.hypothesisConfig = function() {
    return {
        group: 'abc123'  // Your group ID
    };
};
```

Now only group members can see and create annotations.

## Advanced Configuration

### Custom Branding

Match Hypothesis colors to your site:

```javascript
window.hypothesisConfig = function() {
    return {
        branding: {
            appBackgroundColor: '#f5f5f5',
            ctaBackgroundColor: '#1976d2',
            ctaTextColor: '#ffffff',
            selectionFontFamily: 'Roboto, sans-serif'
        }
    };
};
```

### Conditional Loading

Only load Hypothesis on certain pages:

```javascript
document$.subscribe(function() {
    // Only load on pages in the /chapters/ directory
    if (window.location.pathname.includes('/chapters/')) {
        if (typeof window.hypothesisConfig === 'undefined') {
            var script = document.createElement('script');
            script.src = 'https://hypothes.is/embed.js';
            script.async = true;
            document.head.appendChild(script);
            window.hypothesisConfig = function() {
                return {};
            };
        }
    }
});
```

### Front Matter Control

You can also control Hypothesis via page front matter (requires additional JavaScript):

```javascript
document$.subscribe(function() {
    // Check for a meta tag indicating hypothesis should load
    var hypothesisMeta = document.querySelector('meta[name="hypothesis"]');

    if (hypothesisMeta && hypothesisMeta.content === 'true') {
        if (typeof window.hypothesisLoaded === 'undefined') {
            var script = document.createElement('script');
            script.src = 'https://hypothes.is/embed.js';
            script.async = true;
            document.head.appendChild(script);
            window.hypothesisLoaded = true;
        }
    }
});
```

Then in your markdown files, add a meta tag via front matter (requires custom template).

## Viewing Annotations

### As a Site Visitor

1. Click the Hypothesis sidebar arrow (right edge of page)
2. Log in with your Hypothesis account
3. View existing annotations
4. Create new annotations by highlighting text

### As a Site Owner

1. Log in to [hypothes.is](https://hypothes.is)
2. Go to your profile → "My Annotations"
3. Use the search to filter by URL (your site)
4. View, reply to, or moderate annotations

### Using the Hypothesis API

Query annotations programmatically:

```bash
curl "https://hypothes.is/api/search?uri=https://yoursite.github.io/page/" \
  -H "Authorization: Bearer YOUR_API_KEY"
```

Get an API key from your [Hypothesis developer settings](https://hypothes.is/account/developer).

## Troubleshooting

### Sidebar Not Appearing

1. Check browser console for JavaScript errors (F12 → Console)
2. Verify `hypothesis.js` is loading (Network tab)
3. Ensure the path in `mkdocs.yml` matches the actual file location
4. Try a hard refresh: `Ctrl+Shift+R` (Windows/Linux) or `Cmd+Shift+R` (Mac)

### Annotations Not Saving

1. Verify you're logged into Hypothesis
2. Check if you're in a private group you don't have access to
3. Ensure the page URL is publicly accessible (won't work on localhost for saving)

### Highlights Not Showing

1. Check `showHighlights` configuration
2. Click the sidebar to ensure it's connected
3. Verify annotations exist for the current page

### Conflicts with Other Scripts

If Hypothesis conflicts with other JavaScript:

```javascript
document$.subscribe(function() {
    // Delay loading to avoid conflicts
    setTimeout(function() {
        if (typeof window.hypothesisConfig === 'undefined') {
            var script = document.createElement('script');
            script.src = 'https://hypothes.is/embed.js';
            script.async = true;
            document.head.appendChild(script);
            window.hypothesisConfig = function() {
                return {};
            };
        }
    }, 1000);  // Wait 1 second
});
```

## Privacy Considerations

When using Hypothesis, be aware:

| Consideration | Details |
|---------------|---------|
| **Public by default** | Annotations are public unless you use private groups |
| **User accounts** | Users must create Hypothesis accounts to annotate |
| **Data storage** | Annotations are stored on Hypothesis servers, not yours |
| **GDPR** | Hypothesis is GDPR compliant; see their [privacy policy](https://web.hypothes.is/privacy/) |

### For Educational Use

If using with students:

1. Create a private group for your class
2. Inform students about the annotation feature
3. Provide guidelines for appropriate use
4. Consider age restrictions (Hypothesis requires users to be 13+)

## Complete Example

Here's a complete `hypothesis.js` file with common options:

```javascript
// Hypothesis Annotation Integration for MkDocs Material
// File: docs/javascripts/hypothesis.js

document$.subscribe(function() {
    // Prevent duplicate loading
    if (typeof window.hypothesisLoaded === 'undefined') {

        // Create and append the Hypothesis script
        var script = document.createElement('script');
        script.src = 'https://hypothes.is/embed.js';
        script.async = true;
        document.head.appendChild(script);

        // Configure Hypothesis
        window.hypothesisConfig = function() {
            return {
                // Keep sidebar closed by default
                openSidebar: false,

                // Show existing highlights on the page
                showHighlights: true,

                // Uncomment to restrict to a private group:
                // group: 'YOUR_GROUP_ID',

                // Uncomment to customize branding:
                // branding: {
                //     appBackgroundColor: '#fafafa',
                //     ctaBackgroundColor: '#1976d2',
                //     ctaTextColor: '#ffffff'
                // }
            };
        };

        // Mark as loaded
        window.hypothesisLoaded = true;
    }
});
```

## Resources

- [Hypothesis Website](https://web.hypothes.is/)
- [Hypothesis Documentation](https://web.hypothes.is/help/)
- [Publisher Integration Guide](https://web.hypothes.is/for-publishers/)
- [Hypothesis API Documentation](https://h.readthedocs.io/en/latest/api/)
- [Hypothesis Groups](https://web.hypothes.is/help/annotating-with-groups/)
- [Privacy Policy](https://web.hypothes.is/privacy/)

## Next Steps

After setting up Hypothesis:

1. Test the annotation workflow yourself
2. Create a private group if needed
3. Share the annotation feature with your users
4. Monitor annotations via the Hypothesis dashboard
5. Consider combining with [Giscus comments](tutorial/getting-started.md) for comprehensive feedback
