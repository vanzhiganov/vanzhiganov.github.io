# vanzhiganov.github.io

Personal site of **vyacheslav anzhiganov**, built with [Jekyll](https://jekyllrb.com/)
and the [Type Theme](https://github.com/rohanchandra/type-theme).

## Local development

Install Ruby and Bundler, then run:

```sh
bundle install
bundle exec jekyll serve
```

The site is served at <http://localhost:4000>.

## Content

- `index.html` &ndash; home page with contacts and projects.
- `web_start.html` &ndash; notes on optimizing a website before launch.
- `_posts/` &ndash; blog posts (drop a Markdown file named `YYYY-MM-DD-title.md`
  with `layout: post` front matter to publish).
- `_config.yml` &ndash; site and theme settings (title, social icons, analytics, etc.).

## Custom domain

The custom domain (`anzhiganov.com`) is stored as `CNAME.bak`. Rename it to
`CNAME` and set `url: "https://anzhiganov.com"` in `_config.yml` to enable it.

## Deployment

This repository is a GitHub Pages user site, so any push to `master` is built
and published automatically by GitHub Pages.
