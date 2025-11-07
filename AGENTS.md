# Repository Guidelines

This guide is for contributors and automation agents working inside `context-mcp-docs`.

## Project Structure & Module Organization
- `docs.json` defines navigation and sidebar ordering—add new pages there or they will be orphaned.
- Top-level `.mdx` files (`index.mdx`, `quickstart.mdx`, `development.mdx`) are entry points surfaced in the main nav.
- Content lives primarily in `documentation/` (guides) and `snippets/` (reusable inserts). Keep related assets beside their articles.
- Static media belongs in `images/`, `logo/`, or `favicon.svg`; reference via relative paths so Mintlify can bundle them.

## Build, Test, and Development Commands
- `npm i -g mint` – install the Mintlify CLI once per machine.
- `mint dev` – run from the repo root to serve the docs at `http://localhost:3000` with live reload; failing MDX or bad frontmatter will surface here.
- `mint update` – refresh the CLI before debugging issues; many preview errors stem from outdated binaries.

## Coding Style & Naming Conventions
- Write all content in Markdown or MDX; prefer short frontmatter titles and keep metadata blocks at the very top.
- Use kebab-case filenames (`job-status.md`) to match the existing tree and ease linking.
- Favor sentence case headings, 80–100 character prose lines, and 2-space indents for nested lists for consistent diffs.
- Import custom components sparingly and colocate them with the page using relative paths to avoid breaking deploys.

## Testing Guidelines
- There is no automated test suite; rely on `mint dev` to catch syntax errors, broken imports, and link issues before opening a PR.
- Walk the generated sidebar to confirm new entries appear and that code blocks render with the expected language tags.
- When adding interactive snippets, exercise them in multiple viewports using your browser’s device toolbar.

## Commit & Pull Request Guidelines
- Current history (`git log --oneline`) shows imperative, single-line messages (e.g., “Initial commit”); continue that style: `<verb> <scope>`.
- Reference issue IDs when available and describe user-facing impact in the commit body if more context is needed.
- PRs should include: summary of the change, screenshots or GIFs for visual tweaks, confirmation that `mint dev` previewed cleanly, and any follow-up TODOs.

## Content & Configuration Tips
- Update `docs.json` and the relevant section index (for example, `documentation/overview.md`) together so breadcrumbs stay accurate.
- Keep reusable text in `snippets/` and embed via Mintlify components instead of duplicating paragraphs across guides.
