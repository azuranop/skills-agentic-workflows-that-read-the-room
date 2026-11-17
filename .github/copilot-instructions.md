# Copilot instructions

## Build, run, and validation

The website is an Astro project under `site/`. Run commands from that directory:

```bash
npm ci
npm run dev -- --host 0.0.0.0 --port 4321
npm run build
npm run preview -- --host 0.0.0.0 --port 4321
```

Port `4321` is the configured development/preview port. The VS Code launch configuration `Mona Astro: Dev Server` and tasks such as `site: dev` and `site: build` use the same commands.

There is no configured test runner, test suite, or lint script in `site/package.json`, so there is no single-test command. The primary validation for site changes is `npm run build`.

## Architecture

- `site/src/pages/index.astro` is the only page and contains the homepage composition, content parsing, date extraction, update grouping, and fallback release highlight.
- `site/content/github-info.md` is imported with Astro's `?raw` loader. Its Markdown headings and entries are parsed at build time into homepage update cards; preserve the expected `##` section and `###` entry structure when editing it.
- `site/src/layouts/BaseLayout.astro` supplies the HTML shell, metadata, favicon, and global stylesheet import. Shared visual styling lives in `site/src/styles/global.css`.
- Images and other static assets belong in `site/public/` or `site/assets/` according to how they are imported: public files use root-relative URLs, while images imported by Astro components come from `site/assets/`.
- The root repository is an interactive GitHub Skills exercise. `.github/workflows/0-start-exercise.yml` through `3-step.yml` use reusable workflows from `skills/exercise-toolkit` to initialize the exercise, check learner-created files, post the next step, and finish the review. Do not treat those workflows as the site's deployment pipeline.
- Agentic workflow definitions, when added under `.github/workflows/`, are authored as Markdown and compiled separately with `gh aw compile`; the generated `.lock.yml` is part of the workflow change. Keep website updates reviewable through pull requests rather than writing directly to `main`.

## Repository-specific conventions

- Keep Mona website summaries short, practical, and backed by official GitHub sources. Preserve source context when content comes from the GitHub Blog, Changelog, documentation, or Awesome Copilot.
- The homepage prioritizes the newest Blog/Changelog items, then other repository-backed updates, and finally deeper documentation references. New content should fit that review-first, newest-first model.
- `index.astro` intentionally treats `site/content/github-info.md` as a lightweight content format: source lines begin with `Source:`, bullets begin with `- `, and tables are ignored by the parser. Follow those patterns so generated cards retain dates, summaries, bullets, and links.
- For agentic workflows, use controlled `safe-outputs` such as `create-pull-request` for proposed site changes. Read `notes/mona-notes.md` before drafting updates and use the explicitly allowlisted web sources required by the workflow.
- Do not compile agentic workflows from an agent instruction task unless explicitly asked; author or edit the `.md` workflow first, then run `gh aw compile` from the terminal when compilation is required.
- Keep development servers bound to `0.0.0.0` in Codespaces and stop only the exact process listening on the requested port.
