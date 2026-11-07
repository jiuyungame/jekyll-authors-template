---
layout: article
title: Recipe basics
categories: [guides/recipes]
order: 1
tags: [guide]
---

Categories nest more than one level deep. `categories: [guides/recipes]` mirrors
this file to `guides/recipes/recipe-basics.md`, so it is published at
`/guides/recipes/recipe-basics.html` — two directories below the site root,
without anybody creating those directories by hand.

`order: 1` and `order: 2` on the two recipes pages put them into one chain: they
get previous/next links, and "Back to Recipes" points at the sub category page,
which in turn links back to `Guides`.

The chapter root is optional. A sub category without a page of `order: 0` still
gets a chain; it just has no table of contents of its own.
