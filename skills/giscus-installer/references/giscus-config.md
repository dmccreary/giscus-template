# Giscus Configuration Reference

Complete reference for Giscus script configuration attributes.

## Required Attributes

| Attribute | Description | How to Obtain |
|-----------|-------------|---------------|
| `data-repo` | Repository in `owner/name` format | Your GitHub repository path |
| `data-repo-id` | Base64-encoded repository ID | `gh api repos/{owner}/{repo} --jq '.node_id'` |
| `data-category` | Discussion category name | Create in GitHub Discussions settings |
| `data-category-id` | Base64-encoded category ID | GraphQL query (see below) |

## Optional Attributes

### data-mapping

Controls how pages are mapped to discussions.

| Value | Description |
|-------|-------------|
| `pathname` | Uses page URL path (recommended) |
| `url` | Uses full URL including query params |
| `title` | Uses page title |
| `og:title` | Uses Open Graph title meta tag |
| `specific` | Uses `data-term` attribute value |
| `number` | Uses specific discussion number |

### data-strict

| Value | Description |
|-------|-------------|
| `0` | Fuzzy matching (default) |
| `1` | Strict matching only |

### data-reactions-enabled

| Value | Description |
|-------|-------------|
| `1` | Enable reactions (default) |
| `0` | Disable reactions |

### data-emit-metadata

| Value | Description |
|-------|-------------|
| `0` | Don't emit metadata (default) |
| `1` | Emit discussion metadata |

### data-input-position

| Value | Description |
|-------|-------------|
| `top` | Comment input at top (default) |
| `bottom` | Comment input at bottom |

### data-theme

| Value | Description |
|-------|-------------|
| `preferred_color_scheme` | Auto light/dark (recommended) |
| `light` | Always light theme |
| `dark` | Always dark theme |
| `dark_dimmed` | Dimmed dark theme |
| `transparent_dark` | Transparent dark background |
| `noborder_light` | Light without borders |
| `noborder_dark` | Dark without borders |
| Custom URL | Your own CSS file |

### data-lang

Supported languages: `en`, `es`, `fr`, `de`, `it`, `ja`, `ko`, `nl`, `pl`, `pt`, `ro`, `ru`, `tr`, `vi`, `zh-CN`, `zh-TW`

### data-loading

| Value | Description |
|-------|-------------|
| `lazy` | Lazy load (default) |
| `eager` | Load immediately |

## GraphQL Query for Category ID

```bash
gh api graphql -f query='
{
  repository(owner: "OWNER", name: "REPO") {
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

## Example Configurations

### Basic Setup

```html
<script
  src="https://giscus.app/client.js"
  data-repo="username/repo"
  data-repo-id="R_xxxxx"
  data-category="Comments"
  data-category-id="DIC_xxxxx"
  data-mapping="pathname"
  data-theme="preferred_color_scheme"
  data-lang="en"
  crossorigin="anonymous"
  async
></script>
```

### Full Featured

```html
<script
  src="https://giscus.app/client.js"
  data-repo="username/repo"
  data-repo-id="R_xxxxx"
  data-category="Comments"
  data-category-id="DIC_xxxxx"
  data-mapping="pathname"
  data-strict="1"
  data-reactions-enabled="1"
  data-emit-metadata="1"
  data-input-position="top"
  data-theme="preferred_color_scheme"
  data-lang="en"
  data-loading="lazy"
  crossorigin="anonymous"
  async
></script>
```

## JavaScript API

### Send Messages to Giscus

```javascript
const giscus = document.querySelector('iframe.giscus-frame');
giscus.contentWindow.postMessage({
  giscus: {
    setConfig: {
      theme: 'dark'
    }
  }
}, 'https://giscus.app');
```

### Receive Messages from Giscus

```javascript
window.addEventListener('message', function(event) {
  if (event.origin !== 'https://giscus.app') return;
  if (typeof event.data !== 'object') return;

  const giscusData = event.data.giscus;
  if (giscusData) {
    console.log('Discussion:', giscusData.discussion);
  }
});
```

## References

- [Giscus Official Documentation](https://giscus.app)
- [Giscus GitHub Repository](https://github.com/giscus/giscus)
- [GitHub Discussions API](https://docs.github.com/en/graphql/guides/using-the-graphql-api-for-discussions)
