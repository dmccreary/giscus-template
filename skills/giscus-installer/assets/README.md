# Assets

This directory contains template files used by the giscus-installer skill.

## Contents

- **comments-template.html** - Base template for the Giscus comments integration with MkDocs Material theme

## Usage

Copy `comments-template.html` to `docs/overrides/partials/comments.html` in your MkDocs project, then replace the placeholder values:

- `OWNER/REPO` - Your GitHub repository (e.g., `dmccreary/my-textbook`)
- `REPO_ID` - Repository node ID from `gh api repos/{owner}/{repo} --jq '.node_id'`
- `CATEGORY_ID` - Discussion category ID from GraphQL query

See SKILL.md for complete installation instructions.
