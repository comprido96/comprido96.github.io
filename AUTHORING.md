# Site authoring

The public site is built by GitHub Pages with Jekyll. No CMS or custom build dependency is required.

## Publish a Field Note

1. Copy an existing file in `_posts/` to `_posts/YYYY-MM-DD-slug.md`.
2. Update every front-matter field. `order` controls the archive and homepage order; lower numbers appear first.
3. Use `visual: test-stack`, `visual: ablation`, or omit it for the generic archive visual.
4. Write the article in Markdown. HTML is allowed for the existing callout, table, and metric components.
5. Commit and push. GitHub Pages rebuilds `/notes/`, `/notes/feed.xml`, and `/sitemap.xml` automatically.

The post permalink is `/notes/:slug/`. Never change an existing `slug` without adding a redirect.

## Prepare Coaching

The offer lives in `_drafts/coaching.md` and is not emitted by the production build.

Before launch:

1. Replace every bracketed decision in the draft with a concrete offer.
2. Move the file to `coaching/index.md`.
3. Confirm the public page, form or booking flow, privacy implications, and sitemap entry.
4. Set `coaching_enabled: true` in `_config.yml` only after the public URL exists.

The navigation checks that flag. With the current `false` value, Coaching remains invisible.
