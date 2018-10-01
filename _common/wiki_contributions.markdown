---
layout: post
title:  "Wiki Contributions"
date:   2018-09-27 11:43:16 +0200
permalink: /wiki_contributions/
weight: 100
---


## Reporting Issues

Issues can be reported on the [Github page of the wiki](https://github.com/TeamWisp/TeamWisp.github.io). But I highly discourage you from doing so. Instead fix the issue and submit code. We don't have an individual assigned to maintaining the wiki so don't be a lazy bum.

## Adding Pages

There are 3 types of pages: `renderer`, `engine` and `common`. These are *collections*. You create a new page into a collection by creating a new markdown file inside the corresponding folder.

```cpp
_common // Common Collection
_engine // Engine Collection
_renderer // Renderer Collection
```

I recommend you duplicate an existing page and modify the [font-matter](https://jekyllrb.com/docs/step-by-step/03-front-matter/) to suite the new page. This prevents you from forgetting important information when creating a new page.

## Making Modifications

You can make changes on the [Github page of the wiki](https://github.com/TeamWisp/TeamWisp.github.io) directly by clicking the edit button within Github. You can also simply clone the repository and make modifications locally. You however won't be able to see the site update before submitting it with the previously mentioned options. The `weight` option is used to order the navigation bar. The higher the number the lower it will appear in the list.

## Live Previewing

I recommend you install Ruby, Rubygems and Jekyll to preview the changes you are making. This is especially helpfull if you are working on CSS, HTML or working with in-website links.

You are required to clone the repository locally for this.

[You can find detailed instructions on how to set up Jekyll on their website.](https://jekyllrb.com/)
