# Agentic Development Guide for ReedWriteExec Blog

This repository hosts the **ReedWriteExec** blog, a static site built with [Hugo](https://gohugo.io/).
The blog focuses on DevOps, AI, Homelab, and Engineering content with a distinct "Terminal/Unix" branding identity.

## 1. Environment & Build Commands

### Prerequisites
- **Hugo Extended**: The site requires the extended version of Hugo (SASS/SCSS support).
- **Git**: For version control and submodule management.
- **Node.js**: Not strictly required for the base build but often needed for specific theme features (check theme docs if issues arise).

### Primary Commands

| Action | Command | Description |
|--------|---------|-------------|
| **Development Server** | `hugo server -D` | Starts local server with drafts enabled. Access at `http://localhost:1313`. |
| **Production Build** | `hugo --minify` | Builds the static site to `./public` with minification. |
| **New Post** | `hugo new posts/my-new-post.md` | Creates a new content file using the default archetype. |
| **Update Theme** | `git submodule update --remote --merge` | Updates the `themes/ananke` submodule. |

### CI/CD (GitHub Actions)
- **Workflow**: `.github/workflows/gh-pages.yml`
- **Environment**: Ubuntu 24.04
- **Trigger**: Pushes to `main`.
- **Process**: Checks out code (with submodules), setups Hugo Extended, builds, and deploys to GitHub Pages.

### Testing
- **Unit Tests**: There are no formal unit tests (e.g., Jest/Pytest) as this is a content repository.
- **Validation**:
  - **Build Check**: Run `hugo` to ensure there are no template errors or broken shortcodes.
  - **Visual Check**: Run `hugo server -D` and manually verify layout and rendering.
  - **Link Checking**: (Recommended) Ensure all internal links are valid before committing.

## 2. Directory Structure

- `archetypes/`: Templates for new content files (`default.md`).
- `content/`: The actual blog posts and pages.
  - `content/posts/`: Main blog articles.
- `static/`: Raw assets (images, CSS, JS) copied directly to `public/`.
- `themes/`: Contains the `ananke` theme (Git submodule).
- `config.toml`: Main site configuration (TOML format).
- `public/`: Generated static site (ignored by Git, creating by build).

## 3. Code & Content Style Guidelines

### Content (Markdown)
- **Format**: Standard CommonMark Markdown.
- **Filename Convention**: `kebab-case.md` (e.g., `enterprise-homelab.md`).
- **Frontmatter**:
  - Format: **YAML** (enclosed in `---`).
  - Required Fields: `title`, `date`, `draft`.
  - Recommended Fields: `slug`, `tags`, `description`, `author`.
  - Example:
    ```yaml
    ---
    title: "My Awesome DevOps Guide"
    date: 2024-02-05T10:00:00Z
    draft: true
    tags: ["DevOps", "Terraform"]
    ---
    ```

### Branding & Voice
*Strictly adhere to the brand identity defined in README.md.*

- **Name**: ReedWriteExec (rwx).
- **Tagline**: "Permitted to Scale" or "Analyze. Build. Deploy."
- **Tone**: Professional, authoritative, "Senior Engineer", Unix-native.
- **Visuals**:
  - **Colors**: Dark mode, Neon Green/Purple (Dracula/Terminal theme).
- **Prohibited**: Self-deprecating humor (e.g., "ReedTard"), excessive emojis, non-technical fluff.

### Code Snippets
- Always specify the language for syntax highlighting (e.g., \`\`\`bash, \`\`\`yaml).
- Use `bash` for terminal commands.
- Provide context for code blocks (file paths, purpose).

### Git Conventions
- **Commit Messages**: Clear and descriptive.
  - `add: new post about kubernetes`
  - `fix: typo in homelab setup guide`
  - `chore: update hugo theme`
- **Branching**: Use feature branches for new posts or major theme changes.

## 4. Linting & Formatting Rules

- **Markdown**: Ensure headers are properly nested (#, ##, ###).
- **Line Length**: Soft wrap is preferred for prose; do not hard wrap lines arbitrarily unless necessary for specific formatting.
- **Images**: Place images in `static/images/` and reference them as `/images/filename.png`.
- **Hugo Shortcodes**: Use built-in shortcodes (e.g., `{{< figure >}}`, `{{< relref >}}`) where appropriate for richer layouts.

## 5. Development Workflow

1.  **Start**: `git checkout -b post/topic-name`
2.  **Create**: `hugo new posts/topic-name.md`
3.  **Edit**: Write content in `content/posts/topic-name.md`.
4.  **Preview**: Run `hugo server -D` and view changes live.
5.  **Finalize**: Set `draft: false` in frontmatter.
6.  **Commit**: `git add . && git commit -m "add: topic name post"`
7.  **Push**: `git push -u origin post/topic-name`

## 6. AI Agent Instructions (Cursor/Copilot)

*No specific `.cursorrules` or `.github/copilot-instructions.md` found.*

**General Rules for Agents:**
- **Context Awareness**: Always check `config.toml` and existing posts for style consistency before generating new content.
- **Asset Generation**: If asking to generate images/diagrams, ensure they fit the "Terminal/Dark Mode" aesthetic.
- **Safety**: Do not commit large binary files or secrets.
- **Fact Checking**: Verify technical claims (versions, commands) when writing technical guides.
