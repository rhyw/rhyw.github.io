---
layout: post
title: "Significant changes after category is introduced"
date: 2020-04-01 22:46
category: pelc, container
author: Yuguang Wang
tags: []
summary: 
---

## Significant changes

### WebUI
1. Product release is no longer required.
   During package import, we allow "orphan" packages which don't below to any product releases.
2. Show `category` in review page.
3. TODO: category-based report page.

### Migrations
1. A migration has been performed which creates one category for each product releases from product page.
2. All reviews that are under a specific product release are linked to respecitve categories, it's a tree-structure, flexible to add new nodes.
3. Module builds had been created, based on the module label.

### Siginificat API changes
1. /rest/v1/categories
2. /rest/v1/categorizedreviewitems/
3. /rest/v1/reviews/xxx/attach_categories/

### Conceptional changes
1. Multiple reviews can be grouped into one category.
   This makes it possible to reprensent composite review, module builds, containers, and category-based reports.
2. Link review / reuse data
   The challenge of linking review between product release turns into assigning reviews to different categories, which is simpler.
3. `Category` is more generic. as compared with `product release`
   1. `Generic` means we could add reviews/adhoc-reviews/containers into the same category, this makes it possible to specify arbitrary number of reviews(including different review types) in a single category.
   2. `Product release` makes it possible to group a number of packages, while category is a more generic compared of `product release`.

