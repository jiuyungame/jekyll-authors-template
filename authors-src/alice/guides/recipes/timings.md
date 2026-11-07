---
layout: article
title: Timing notes
categories: [guides/recipes]
order: 2
tags: [guide]
---

The second page of the `guides/recipes` chain. Its previous/next links come from
the `order` values, and the license of the `article` layout is inherited from the
nearest chapter root that has one — here that is `guides/overview.md`, because
`guides/recipes` has no root of its own.

Nesting also works for the license lookup: a page deep inside a sub category
walks up its categories until it finds a root page with a `license`.
