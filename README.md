# morganpartee.github.io

Archive of selected writing originally published on
[sensibledefaults.io](https://web.archive.org/web/2023/https://www.sensibledefaults.io/)
between 2021 and 2023. Rebuilt as a static Jekyll site on GitHub Pages.

## Deploy

1. Create a new GitHub repo named exactly `morganpartee.github.io`.
2. Push this folder as the `main` branch.
3. In **Settings → Pages**, set **Source: Deploy from a branch**, **Branch: `main` / `/ (root)`**. Save.
4. First build takes a minute. Site lives at https://morganpartee.github.io.

That's it — GitHub Pages builds Jekyll for you.

## Run locally

```bash
bundle install
bundle exec jekyll serve
# http://localhost:4000
```

## Adding a new post

Drop a file in `_posts/` named `YYYY-MM-DD-slug.md` with frontmatter:

```yaml
---
layout: post
title: "Post title"
date: 2026-04-24
original_date: 2026-04-24
---
```

Keep `date` and `original_date` in sync for new posts. For archived posts,
`original_date` is the publish date on sensibledefaults.io; the post layout
renders it as "Originally published on…" above the body.
