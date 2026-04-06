# Jekyll workspace (for editing in Obsidian)

You can open this folder as an Obsidian vault (or add it as a folder) to edit posts directly in-place.

Naming posts for Jekyll

- Jekyll expects posts in `_posts/` to have filenames that start with the date in `YYYY-MM-DD` format, e.g.: `2026-04-05-my-post.md`.
- If you place files in subdirectories under `_posts/` (for example `_posts/project-a/2026-04-05-update.md`), Jekyll will still pick them up; the subdirectory name is commonly used for categories (and will appear in `post.categories` when front matter doesn't override it).
- Each post should include YAML front matter at the top, for example:

```yaml
---
title: "My Post Title"
date: 2026-04-05 12:00:00 +0000
categories: [project-a]
---
```

If you prefer not to use date-prefixed filenames, consider using a Jekyll collection (`_config.yml` -> `collections`) instead; tell me and I can add a collection configuration and adapt `posts.json` to include collection items.

Git note

The `.gitignore` in this folder ignores Obsidian's `.obsidian/` folder and common editor artifacts so you won't accidentally commit local editor settings.
