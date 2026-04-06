# Blog repository (scaffold)

This repository contains an Obsidian vault (your working Markdown files) and a minimal Jekyll scaffold for generating a public static site. The objectives and plan were preserved in `spec.md`.

Layout created by this scaffold

- `spec.md` — project specification (renamed from your original plan)
- `jekyll/` — the Jekyll site source
  - `_config.yml` — site config
  - `_layouts/default.html` — simple page layout
  - `_posts/` — posts go here (Jekyll convention)
  - `index.html` — client-side feed that fetches `/posts.json`
  - `posts.json` — Liquid template that outputs a JSON array at build time
- `scripts/embed_media` — a small Bash helper to upload media to Cloudflare R2 (via aws-cli) and print embed markup

Quick notes

- I created a minimal Jekyll scaffold under `jekyll/` rather than moving your Obsidian files. This keeps authoring (Obsidian vault) and build infrastructure separate as you requested.
- The `embed_media` script expects the following environment variables when uploading:
  - `R2_BUCKET` — your R2 bucket name
  - `R2_ENDPOINT` — the S3 endpoint for your R2 account (e.g. https://<accountid>.r2.cloudflarestorage.com)
  - optionally `AWS_PROFILE` — if using a non-default aws profile
  - optionally `R2_PUBLIC_DOMAIN` — public domain to use for embeds (default: `${R2_BUCKET}.r2.dev`)

Usage examples

Dry-run (no upload):

```bash
./scripts/embed_media ~/Downloads/clip.mp4 --dry-run
```

Real upload (requires aws cli configured):

```bash
export R2_BUCKET=mybucket
export R2_ENDPOINT=https://<account>.r2.cloudflarestorage.com
export AWS_PROFILE=myr2profile
./scripts/embed_media ~/Downloads/pic.jpg
```

Local testing

To test the Jekyll site locally (if you have Ruby/Jekyll installed):

```bash
cd jekyll
bundle exec jekyll serve
```

This will build the site and make `posts.json` available at `http://127.0.0.1:4000/posts.json`.

Next steps

- Wire your Obsidian exports (or use symlinks) into `_posts/` or a build step that copies markdown into `jekyll/_posts/`.
- Provision Cloudflare R2 and test `embed_media` with `--dry-run` first.
- Expand the index UI as needed (tag filters, infinite scroll).
