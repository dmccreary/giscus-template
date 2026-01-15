# Glossary of Terms

#### Category ID

A Base64-encoded unique identifier for a GitHub Discussion category, used by Giscus to associate page comments with the correct discussion category.

**Example:** A category ID looks like `DIC_kwDOQ6Dmxc4C0-IF` and is retrieved via the GitHub GraphQL API.

#### Comment System

A software component that enables users to post text-based feedback on web pages, typically storing comments in a database or external service.

**Example:** Giscus is a comment system that stores comments in GitHub Discussions rather than a traditional database.

#### Custom Directory

A folder in an MkDocs project that contains template overrides, allowing customization of theme elements without modifying the original theme files.

**Example:** Setting `custom_dir: docs/overrides` tells MkDocs to look for template customizations in the `docs/overrides/` folder.

#### Discussion Category

A classification grouping for GitHub Discussions that organizes conversations by topic or purpose within a repository.

**Example:** Creating a "Comments" category with the Announcement format ensures only maintainers can create new discussions while anyone can reply.

#### DOM

Document Object Model; a programming interface that represents an HTML page as a tree structure of objects, enabling JavaScript to dynamically access and modify page content.

**Example:** The JavaScript code `document.getElementById("giscus-container")` uses the DOM to find and manipulate the comment container element.

#### Event Capturing

A JavaScript event propagation phase where event handlers are triggered from the outermost element down to the target element, before the bubbling phase.

**Example:** Using `addEventListener("click", handler, true)` enables event capturing to detect clicks even when the target element is inside a disabled fieldset.

#### Feedback Widget

A user interface component in MkDocs Material that displays rating icons (typically happy/sad faces) to collect quick user sentiment about page quality.

The feedback widget sends data to Google Analytics and is configured separately from comment systems like Giscus.

**Example:** Clicking the happy face icon sends a rating value of `1` to Google Analytics and can trigger the comment form to appear.

#### Front Matter

A YAML-formatted metadata block at the beginning of a Markdown file, delimited by triple dashes (`---`), that configures page-specific settings.

**Example:** Adding `comments: true` to front matter enables the Giscus comment form on that specific page.

#### gh CLI

GitHub Command Line Interface; a terminal tool that enables users to interact with GitHub repositories, issues, pull requests, and APIs directly from the command line.

**Example:** Running `gh api repos/owner/repo -F has_discussions=true` enables GitHub Discussions via the command line.

#### Giscus

A comment system that uses GitHub Discussions as its data store, embedding an iframe-based comment form directly into web pages.

Giscus requires the Giscus GitHub App to be installed and GitHub Discussions to be enabled on the repository.

**Example:** When a user posts a comment via Giscus, it creates or updates a discussion thread in the repository's Discussions tab.

#### GitHub Discussions

A collaborative forum feature built into GitHub repositories that enables threaded conversations, reactions, and organized question-and-answer exchanges.

**Example:** Each page with Giscus comments creates a corresponding discussion thread, allowing maintainers to respond to user feedback directly in GitHub.

#### GitHub Pages

A static site hosting service provided by GitHub that publishes websites directly from a repository branch.

**Example:** Running `mkdocs gh-deploy` builds the documentation and publishes it to `https://username.github.io/repo-name/`.

#### GraphQL

A query language for APIs that allows clients to request specific data fields, commonly used to retrieve GitHub repository and discussion metadata.

**Example:** The GitHub GraphQL API is used to retrieve discussion category IDs required for Giscus configuration.

#### iframe

An HTML element that embeds another HTML document within the current page, creating an isolated browsing context.

**Example:** Giscus renders its comment form inside an iframe with the class `giscus-frame`, isolating its styles and scripts from the host page.

#### Intelligent Textbook

An educational resource that incorporates interactive elements, feedback mechanisms, and adaptive features to enhance learning beyond static text.

**Example:** By integrating Giscus comments, an MkDocs site becomes an intelligent textbook where students can ask questions and receive answers within the content.

#### Markdown

A lightweight markup language that uses plain text formatting syntax to create structured documents, commonly converted to HTML for web display.

**Example:** MkDocs converts `.md` files written in Markdown into HTML pages that form the documentation site.

#### Meta Plugin

An MkDocs plugin that enables folder-level metadata configuration through `.meta.yml` files, allowing settings to apply to all pages within a directory.

**Example:** With the meta plugin enabled, placing a `.meta.yml` file with `comments: true` in a folder enables comments on all pages in that folder.

#### .meta.yml

A configuration file recognized by the MkDocs meta plugin that applies metadata settings to all Markdown files in its containing folder and subfolders.

**Example:** Creating `docs/chapters/.meta.yml` with `comments: true` enables comments on all chapter pages without editing each file individually.

#### MkDocs

A static site generator designed for building documentation websites from Markdown files, configured through a `mkdocs.yml` file.

**Example:** Running `mkdocs serve` starts a local development server that automatically rebuilds the site when files change.

#### MkDocs Material

A feature-rich theme for MkDocs that provides responsive design, search functionality, dark mode, and numerous customization options.

**Example:** The feedback widget with happy/sad face icons is a built-in feature of MkDocs Material.

#### MutationObserver

A JavaScript API that monitors changes to the DOM tree, triggering callbacks when specified elements or attributes are modified.

**Example:** A MutationObserver watches for the feedback note's visibility change to trigger the comment form appearance.

#### Node ID

A Base64-encoded unique identifier assigned by GitHub to repositories, issues, discussions, and other objects, used in GraphQL queries.

**Example:** A repository node ID like `R_kgDOQ6DmxQ` is required as the `data-repo-id` attribute in Giscus configuration.

#### Palette

A color scheme configuration in MkDocs Material that defines the visual appearance, including primary colors, accent colors, and light or dark mode settings.

**Example:** Switching from the `default` scheme to `slate` scheme changes the site to dark mode, and Giscus can sync its theme accordingly.

#### Pathname Mapping

A Giscus configuration option that associates each page with a discussion thread based on the page's URL path rather than title or other identifiers.

**Example:** With pathname mapping, the page at `/chapters/intro/` creates a discussion thread identified by that path, ensuring consistent comment association.

#### Repository ID

A unique identifier assigned to a GitHub repository, used by Giscus to verify that comment requests originate from authorized pages.

**Example:** The repository ID is retrieved using `gh api repos/owner/repo --jq '.node_id'` and configured as `data-repo-id` in Giscus.

#### Theme Override

A customization mechanism in MkDocs Material that replaces default theme templates with custom versions stored in a designated directory.

**Example:** Creating `docs/overrides/partials/comments.html` overrides the default comments template, enabling custom Giscus integration.

#### YAML

YAML Ain't Markup Language; a human-readable data serialization format used for configuration files and data exchange, featuring indentation-based structure.

**Example:** The `mkdocs.yml` file uses YAML syntax to configure site settings, theme options, and plugin configurations.
