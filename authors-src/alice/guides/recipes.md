---
layout: page
title: Recipes
categories: [guides]
---

This page is the landing page of the **Guides → Recipes** sub category, and it
never asked to be one: there is no `category_page: true` in the front matter.

A page mirrored into `guides/` with `title: Recipes` lands on `guides/recipes.md`,
which is exactly where the page of the category `guides/recipes` belongs. Because
`basics` and `timings` declare `categories: [guides/recipes]`, that category
really exists, so this page becomes its page. The list below is generated from
it, and the sub category also shows up in the `Guides` list and in the chapter's
table of contents.

Add an `order` value to this page and it stays an ordinary chapter member
instead: an ordered page keeps its place in the previous/next chain.

Nothing was written into `guides/recipes/` for this page — it is a navigation
hub, like every other category page.
