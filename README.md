# blog — Jekyll static blog scaffold and source

This repository contains a minimal Jekyll-based static blog and helper scripts for building and publishing the site.

Repository and site
- Repository: https://github.com/tim-fan/blog
- Expected public site (project GitHub Pages): https://tim-fan.github.io/blog/  
  Note: the site URL above is the standard project-site address once Pages are enabled; if you publish as a user site the URL will be `https://tim-fan.github.io/`.

What’s in this repository
- `spec.md` — project specification and implementation notes.
- `jekyll/` — Jekyll site source and content (see below).
- `scripts/embed_media` — Bash helper to upload images/videos to an S3-compatible endpoint (Cloudflare R2) and print Markdown/HTML embed markup.

Key files in `jekyll/`
- `_config.yml` — Jekyll configuration; set `url` and `baseurl` according to how you publish the site.
- `_layouts/` — page layouts (`default.html`, `post.html`).
- `_posts/` — place dated posts here using the `YYYY-MM-DD-title.md` convention; subdirectories are allowed and commonly used for project grouping.
- `posts.json` — Liquid template which renders a JSON array of post metadata at build time.
- `index.html` — client-side index that fetches `posts.json` and renders the feed in the browser.

Quick start — run locally
1. Install Ruby and Jekyll (if needed). See https://jekyllrb.com/docs/installation/.
2. Serve the site locally from the `jekyll/` folder:

```bash
cd jekyll
bundle exec jekyll serve
```

Visit the local address printed by Jekyll (typically http://127.0.0.1:4000/). `posts.json` will be available at `/posts.json` for the client-side index to consume.

Publishing with GitHub Pages
- Option A (simple): move the contents of `jekyll/` into a top-level `docs/` directory and enable Pages from the `main` branch using the `/docs` folder. This requires no CI and is the simplest setup for a project site.
- Option B (recommended if you keep `jekyll/`): add a GitHub Actions workflow that runs `jekyll build` and publishes the generated `_site` to `gh-pages` or the Pages branch. This lets you keep the source in `jekyll/` and publish automatically.

Configuration notes
- If publishing as a project site (for example `tim-fan.github.io/blog`), set in `jekyll/_config.yml`:

```yaml
url: "https://tim-fan.github.io"
baseurl: "/blog"
```

This repository already uses `relative_url` in client-side fetch calls so the index will work with a non-empty `baseurl`.

Post authoring
- Add posts to `jekyll/_posts/` using the `YYYY-MM-DD-title.md` filename format and include YAML front matter with at least `title` and `date`. Subdirectories under `_posts/` are supported and are commonly used to group posts by project — Jekyll will derive categories from the path unless categories are set explicitly in front matter.
- See `jekyll/README.md` for guidance on editing the site in-place with Obsidian.

Media uploads
- Use `scripts/embed_media` to upload images and videos to Cloudflare R2 (S3-compatible). The script supports `--dry-run` and requires environment variables such as `R2_BUCKET` and `R2_ENDPOINT` when performing real uploads.

If you’d like, I can:
- move the site into `docs/` and push the change so Pages can publish immediately, or
- add a GitHub Actions workflow to build and publish from `jekyll/` automatically.
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
