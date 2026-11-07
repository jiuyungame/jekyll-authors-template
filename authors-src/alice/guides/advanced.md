---
layout: article
title: Advanced usage
categories: [guides]
order: 2
tags: [guide]
---

## Optional plugin features

- `authors.keep_mirror: true` in `_config.yml` keeps the generated files around
  after the build, which is handy when debugging the mapping.
- Set `category_list: false` in a category page's front matter to skip its
  automatic member list and place `{% raw %}{% category_list %}{% endraw %}`
  somewhere else instead.
- `_plugins/PluginUtils.rb` normalises `categories` and `tags`, so both
  `categories: [a, b]` and the space separated `categories: a b` work.
- Categories nest: `categories: [guides/recipes]` mirrors a page to
  `guides/recipes/<title>.md`, however deep the tree goes.
- A page whose title matches a sub category's folder becomes that sub category's
  page without any switch — `recipes.md` in this demo hosts `guides/recipes`
  through nothing but its `title`, `categories` and the fact that somebody
  declares `guides/recipes`.
- Every page gets exactly one "back up" link — its chapter root, its sub category
  page, its category page, or the home page, depending on where it sits.

## Where to look next

- `docs/authoring.md` for the writer's guide.
- `docs/configuration.md` for every `_config.yml` key and plugin.
- `_plugins/Authors.rb` for the author to site mapping rules.
