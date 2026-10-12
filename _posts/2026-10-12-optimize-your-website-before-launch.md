---
layout: post
title: "Optimize your website before launch"
subtitle: "A short checklist to run through before you go live"
tags: [web, performance, checklist]
---

A site that loads slowly loses visitors before they ever see your content. A few
minutes of tuning before launch pays off for years. This is the short checklist
I run through before putting a project online.

## Measure first

Never optimize by gut feeling. Start by measuring the real thing:

- [Google's PageSpeed / testmysite](https://testmysite.withgoogle.com/intl/en-gb)
- Your browser's DevTools **Network** and **Lighthouse** tabs
- A repeat visit, to see what the cache is doing

Write the numbers down. They are the baseline you compare against.

## The essentials

1. **Enable gzip / brotli compression** for HTML, CSS, JS and JSON.
2. **Set long cache lifetimes** on static assets and fingerprint their filenames.
3. **Serve images responsively** and compress them. A 3 MB hero image is the
   single most common cause of a slow first paint.
4. **Minify** CSS and JavaScript, and remove what you do not use.
5. **Put static files on a CDN** so they are served from close to the visitor.

## Before you publish

- Force **HTTPS** and redirect `http://` to it.
- Add a sensible `<title>` and meta description to every page.
- Provide `robots.txt` and a `sitemap.xml`.
- Add an **analytics** counter so you can keep measuring after launch.
- Check the site on a real phone, not just a resized window.

## A quick example

Even a one-line cache header goes a long way:

```nginx
location ~* \.(css|js|png|jpg|jpeg|gif|svg|woff2)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
}
```

Measure, fix the biggest problem, measure again. Repeat until the numbers stop
improving. That is the whole loop.
