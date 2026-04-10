# blog — Jekyll static blog

This repository contains a minimal Jekyll-based static blog and helper scripts for building and publishing the site.

Repository and site
- Repository: https://github.com/tim-fan/blog
- Public site: https://tim-fan.github.io/blog/

What's in this repository
- `spec.md` — project specification and implementation notes.
- `docs/` — Jekyll site source; GitHub Pages publishes directly from this folder.
- `scripts/embed_media` — Bash helper to upload images/videos to an S3-compatible endpoint (Cloudflare R2) and print Markdown/HTML embed markup.

Key files in `docs/`
- `_config.yml` — Jekyll configuration; `url` and `baseurl` are set for `tim-fan.github.io/blog`.
- `_layouts/` — page layouts (`default.html`, `post.html`).
- `_posts/` — place dated posts here using the `YYYY-MM-DD-title.md` convention; subdirectories are allowed and commonly used for project grouping.
- `posts.json` — Liquid template which renders a JSON array of post metadata at build time.
- `index.html` — client-side index that fetches `posts.json` and renders the feed in the browser.

Publishing with GitHub Pages
- GitHub Pages is configured to publish from the `docs/` folder on the `main` branch. Push changes to `docs/` and GitHub Pages will rebuild the site automatically.

Quick start — run locally
1. Install Ruby and Jekyll (if needed). See https://jekyllrb.com/docs/installation/.
2. Add a `Gemfile` inside `docs/` if one is not already present (see below), then serve the site:

```bash
cd docs
bundle install    # only needed the first time or when Gemfile changes
bundle exec jekyll serve
```

Visit the local address printed by Jekyll (typically http://127.0.0.1:4000/blog/).


Post authoring
- Add posts to `docs/_posts/` using the `YYYY-MM-DD-title.md` filename format and include YAML front matter with at least `title` and `date`. Subdirectories under `_posts/` are supported and are commonly used to group posts by project — Jekyll will derive categories from the path unless categories are set explicitly in front matter.

Media uploads
- Use `scripts/embed_media` to upload images and videos to Cloudflare R2 (S3-compatible). The script supports `--dry-run` and requires environment variables such as `R2_BUCKET` and `R2_ENDPOINT` when performing real uploads.

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
