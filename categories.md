---
# categories.md
layout: page
title: Categories
---

Here is a list of all post categories in this archive.

<div id="category-list" class="mt-4">
  {% for category in site.categories %}
  <div class="category-item">
    <a href="{{ site.baseurl }}/categories/{{ category[0] | slugify }}/" class="category-link">
      <h3 class="category-name">{{ category[0] | capitalize }}</h3>
      <span class="category-count">{{ category[1].size }} posts</span>
    </a>
  </div>
  {% endfor %}
</div>