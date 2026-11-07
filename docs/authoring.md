# Authoring guide

This template lets several authors contribute content without touching the site
layout or the shared files. Everything an author writes lives in their own
folder, and the plugins mirror it into the site at build time.

## Who writes what

| Who | Where |
| --- | --- |
| Authors | `authors-src/<author>/` |
| Site owner | top level category pages (`author.md`, `guides.md`, ...), `index.html`, `about.md`, `_config.yml`, `docs/` |
| Either | sub category pages such as `guides/recipes.md`: the owner commits one, or an author provides one (see [Category pages](#category-pages)) |

## Author folder anatomy

```
authors-src/alice/
├── author.md                      # profile page, categories: [author]
├── _includes/note.html            # partials for {% include_author %}
├── _posts/
│   ├── 2025-01-15-hello.md        # dated post filename
│   └── using-this-template.md     # undated: the plugin adds the date prefix
├── _drafts/work-in-progress.md
├── guides/getting-started.md      # page that joins the guides category
├── guides/recipes.md              # becomes the page of guides/recipes
├── guides/recipes/basics.md       # page in the nested guides/recipes category
└── sandbox.md                     # uncategorized page
```

`authors-src` is the default; change it with `authors.source` in `_config.yml`.
Folder names become part of the site (`_includes/<author>/`), so prefer short
ASCII slugs.

## Mapping rules

| Source file under `authors-src/<author>/` | Published as |
| --- | --- |
| page with `categories: [guides]` and a `title` | `guides/<title slug>.md` → `/guides/<title slug>.html` |
| page with a nested category, `categories: [guides/recipes]` | `guides/recipes/<title slug>.md` |
| page with several usable categories | one copy per category, e.g. `guides/...` and `notes/...` |
| page with `category_page: true` | the category page itself, `guides/recipes.md`; no member copy |
| page whose categories are not usable | `authors/<author>/<relative path>` |
| `author.md` without categories | `authors/<author>/author.md` |
| `_posts/**` | `_posts/<author>/**` (a real Jekyll post) |
| `_drafts/**` | `_drafts/<author>/**` (built only with `--drafts`) |
| `_includes/**` | `_includes/<author>/**` |
| `_layouts/**`, `_sass/**`, `_data/**` | same directory, namespaced by author |
| `_plugins/**` | skipped (plugins load before the mirror) |

A category is usable only when its category page exists: `guides.md` for
`guides`, `guides/recipes.md` for `guides/recipes`. Copies for missing categories
are skipped with a warning, and the page falls back to `authors/<author>/...`.

Categories nest through a slash path. `categories: [guides, notes]` means two
sibling copies; `categories: [guides/recipes]` means a child of `guides`. There
is no depth limit.

A page that declares several categories appears in every matching category list,
but it joins the chapter (`order`, previous/next) of one category only: the
deepest one it declares. The build warns when that combination could be read two
ways.

The published file name comes from the front matter `title`, slugified by Jekyll
(Unicode is preserved). Without a title the source file name is used. Add
`permalink:` to the front matter if you need a custom URL.

## Front matter reference

| Key | Used for |
| --- | --- |
| `layout` | `page`, `article` (chapter root / ordered pages), `post` (posts) |
| `title` | Page title, category list label, target file name and up-link text |
| `categories` / `category` | Category membership; array or space separated string; a slash creates a sub category |
| `order` | Order inside a category chapter (`1`, `1.1`, `1.2`, `2`, ...) |
| `category_page` | `true` turns this author file into the category page of its category |
| `tags` | Tag chips and `/tags/<tag>.html` index pages |
| `date` | Publication date; used for posts without a dated filename |
| `modified_date` | "Updated on" date and recency sorting |
| `license` | Per-page license; inherited from the chapter root when using `layout: article` |
| `main_part` | Numeric section weight for the home page list |
| `category_list` | `false` disables the automatic list on a category page |

## Category pages

A category page is the file named after its category: `guides.md` for `guides`,
`guides/recipes.md` for `guides/recipes`. The site owner commits the top level
ones:

```markdown
---
layout: page
title: Guides
main_part: 3
---

Step-by-step guides.
```

During the build the page gets a list of:

- its direct sub categories, as links to their own category pages;
- every page directly in `guides/` (author content and pages committed in that
  directory), ordered by `order` when present and by path otherwise;
- every post whose `categories` include `guides`, newest first.

Deeper pages are not repeated here; they appear on the category page that owns
them. Each item shows the page title and links to it. Options:

- `category_list: false` — no automatic list;
- `{% category_list %}` — insert the list manually where you want it (the
  automatic append is then skipped);
- `{% category_list guides/recipes %}` — list another category's members.

### Category pages written by authors

An author can provide a sub category page from their own folder. Either declare
the category:

```markdown
---
layout: page
title: Tech notes
category_page: true
categories: [guides/tech]
---
```

or leave `categories` out and let the folder path supply it — this file sits in
`authors-src/alice/guides/tech/`, so it hosts `guides/tech`:

```markdown
---
layout: page
title: Tech notes
category_page: true
---
```

Rules:

- only sub categories (`guides/tech`); a top level category page stays with the
  site owner;
- a category page that already exists on disk always wins, and the author file
  falls back to `authors/<author>/...`;
- when two authors claim the same category page the first in sort order wins and
  the other one falls back, with a warning;
- the file is a mirror, so it is written for the build and removed afterwards —
  keep the source in `authors-src/`.

A category page is a navigation hub: it is not part of a chapter, so it never
gets previous/next links or a chapter root of its own.

### Category pages without the switch

`category_page: true` is only needed to place a category page under a path that
is not the one the mirror would choose anyway. A page whose target path already
*is* a category page becomes one on its own:

```yaml
# authors-src/alice/guides/recipes.md
title: Recipes
categories: [guides]
```

The member target for that page is `guides/recipes.md`, which is exactly where
the page of the category `guides/recipes` belongs. Because `basics.md` and
`timings.md` declare `categories: [guides/recipes]`, that category exists, so
this page becomes its page — the build logs
`Page(s) serving as their category page: alice/guides/recipes.md -> guides/recipes`.

Two limits keep this predictable:

- the category has to be declared by somebody, so a title that merely looks like
  a folder never turns a page into a category page;
- a page with an `order` value keeps its place in the chapter chain instead.

An existing category page, from the site owner or from another author, always
wins over this.

### Posts index

Posts normally join a normal category such as `blog`, which makes them live at
`/blog/<title>.html`. If you would rather collect every post on one page, use the
listing-only category `_posts` and a `_posts.md` at the site root:

```markdown
---
layout: page
title: Posts
main_part: 4
---
```

- every post declaring `categories: [_posts]` is listed on `/_posts.html`, newest
  first, and published at `/_posts/<title>.html`;
- use `[_posts]` on its own — a second category would be prepended to the URL;
- `_posts` is never a valid mirror target, so nothing is ever written into the
  `_posts/` collection directory;
- Jekyll skips entries starting with `_`, so the plugin adds `_posts.md` and
  `_posts.markdown` to `site.include` before the site is read.

## Author profiles

Every author creates `author.md` with `categories: [author]`:

```markdown
---
layout: page
title: Alice
categories: [author]
date: 2025-01-01
---

A short profile.
```

The site owner must create `author.md` at the site root once; after that all
profiles appear at `/author/<title>.html` and on the Authors page.

## Ordered chapters

Give pages in a category an `order` value and add a root page with `order: 0`:

```markdown
---
layout: article
title: Overview
categories: [guides]
order: 0
license: "CC BY 4.0"
---

Ordered guides in this category: `Getting started` (order 1) and
`Advanced usage` (order 2).
```

- ordered pages get previous/next links;
- every ordered page links back to the root page;
- the root page shows a nested table of contents with the numbers, followed by
  one entry per direct sub category;
- the category page lists its members in the same `order`;
- `layout: article` pages inherit the root page's license, and a sub category
  with no root of its own inherits from its parent category;
- the root of a sub category also links back to its parent category page.

A root page is optional for a sub category: without one the ordered members still
form a chain, they just have no table of contents of their own. A page that
declares several categories and an `order` joins one chapter only — the deepest
category it declares — and the build warns about it.

`_plugins/CategoryOrder.rb` implements the ordering and chapter navigation,
`_plugins/DefaultLicense.rb` the license inheritance.

## Up links

Every page that belongs to a category gets exactly one "back up" link, at the top
of the article or at the bottom of the page. The link text names its target, so a
reader never sees the generic word "category": `☰ Back to Guides`,
`☰ Back to Overview`, `☰ Back to Home`.

| Page | Link | Points at |
| --- | --- | --- |
| ordered member (`order: 1`) | Back to *chapter root title* | the chapter root page |
| ordered sub page (`order: 1.1`) | Back to *root*, plus Back to *order parent* | root page and the order parent |
| member without `order` | Back to *category page title* | the category page |
| post | Back to *category page title* | its category page |
| chapter root (`order: 0`) | Back to *category page title* | the category page |
| sub category page | Back to *parent category page title* | the parent category page |
| top level category page | Back to Home | `/` |

Pages that belong to no category (`about.md`, `index.html`, tag pages, the
uncategorised fallback pages, `404.html`) get no up link. When a page has both a
`root` and a `parent` pointing at the same URL, only one link is rendered.

The data comes from `_plugins/CategoryOrder.rb` (`page.root`, `page.parent`,
`page.parent_label`); `_includes/page-nav.html` and the `page`, `article` and
`post` layouts render it. A template of your own should keep those blocks.

## Per-author includes

Put reusable partials in `<author>/_includes/` and include them with:

```liquid
{% include_author note.html title="Tip" body="Rendered from this author's _includes folder." %}
```

The tag looks in `_includes/<author>/` first and falls back to the shared
`_includes/` folder. Author folder names must be ASCII slugs for this to work;
the build logs a warning otherwise.

## Posts, tags and drafts

- `_posts/**` is mirrored to `_posts/<author>/**` and behaves like a normal
  Jekyll post.
- With `categories: [blog]` and the default permalink, posts are published at
  `/blog/<title>.html`. See [Posts index](#posts-index) for the single page
  alternative.
- Post filenames do not need a date: the plugin prefixes the front matter
  `date` (or `modified_date`, or the file modification time).
- `tags:` generates `/tags/<tag>.html` index pages and tag chips in the footer.
- Drafts in `_drafts/` are mirrored but only built with
  `bundle exec jekyll serve --drafts`.
- `modified_date` adds an "Updated on" line and influences the "Recently
  updated" sidebar.
- Posts keep Jekyll's built-in previous/next links (ordered by date); the
  category `order` only applies to pages.

## Images and assets

There is no automatic mirroring of assets into category folders. Put images next
to your content and reference them under the author path they are published at:

```markdown
![Screenshot](/authors/alice/guides/screenshot.png)
```

Any file that is not a page with front matter (images, PDFs, ...) is mirrored to
`authors/<author>/<relative path>`.

## Warnings and fixes

| Warning | Meaning |
| --- | --- |
| `Category page missing (create <category>.md, or set category_page: true on an author file)` | No page exists for that category; the copy was skipped and the page fell back to `authors/<author>/...`. |
| `Invalid category value, skipped` | A category segment contains a path separator, starts with `_` or `.`, is a reserved name (`tmp`, `authors`, `authors-src`) or uses a character Windows rejects. |
| `Top level category pages are created by the site owner` | `category_page: true` with a single level category; only sub categories are accepted from authors. |
| `Category page is maintained by the site owner` | A file already exists at that path; the author copy fell back to `authors/<author>/...`. |
| `Another author already provides this category page` | Two authors claimed the same sub category; the first in sort order wins and the other falls back. |
| `category_page: true without a category and without a folder path` | Neither `categories` nor a folder below the author folder gives a category id. |
| `Invalid category for category_page: true, skipped` | The category id is malformed. |
| `category_page: true does not apply inside _posts/ or _drafts/` | The switch only works for regular content files. |
| `A category page should declare one category; using the deepest` | More than one category was declared on a category page. |
| `Copies skipped because the target is a category page` | A page title would collide with a category page; that copy falls back instead of overwriting it. |
| `Target exists and is not managed by this plugin` | A file already exists at the target path; delete it and rebuild to let the plugin map it. |
| `Target was modified externally` | The file was edited after the plugin wrote it; the plugin leaves it alone. |
| `Duplicate mapping target` | Two sources want the same output path; the first wins. |
| `Author folder ... is unsafe for include paths` | Rename the author folder to an ASCII slug. |
| `Skipped N author plugin file(s)` | `_plugins/` inside an author folder is not supported. |

Informational, not a problem:
`Page(s) serving as their category page: alice/guides/recipes.md -> guides/recipes`
(see [Category pages without the switch](#category-pages-without-the-switch)).

## Removing the demo content

1. Delete `authors-src/alice/` and `authors-src/bob/` (or replace them). That
   removes the guides, recipes, notes, author profiles and demo posts in one go.
2. Delete `guides/overview.md`.
3. Remove or rename `author.md`, `guides.md`, `blog.md` and update `header_pages`
   in `_config.yml`; also delete the empty `guides/` directory.
4. Replace the text in `index.html` and `about.md`.
