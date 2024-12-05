# Multi(s)tool(s)

I do useful things sometimes! I should write about it.

Uses `daattali/beautiful-jekyll@6.0.1` through `jekyll-remote-theme`.

To test locally, I use:

```bash
docker build -t jekyll-w-remote-theme .
docker run --rm -it -v $PWD:/srv/jekyll -v $PWD/vendor/bundle:/usr/local/bundle -p 4000:4000 jekyll-w-remote-theme jekyll serve
```

## Gotchyas

Posts need to be added to `_posts/[YEAR]-[MONTH]-[DAY]-[snakecased-title].md` with exactly that form, else they won't be registered as "posts".

Posts are markdown, but need to have a header like:

```
---
layout: post
title: Test post
author: Brandon Victor
readtime: true
date: 2024-09-18
tags: debugging
---
```
