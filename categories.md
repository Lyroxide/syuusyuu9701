---
layout: page
title: Categories
permalink: /categories/
---

<div id="category-list">
  {% for category in site.categories %}
    <div class="category-item mb-3">
      <a href="{{ site.baseurl }}/categories/{{ category[0] | url_encode }}/" class="btn btn-lg btn-outline-primary d-block text-start">
        <h3 class="category-name d-inline-block mb-0">{{ category[0] }}</h3>
        <span class="category-count float-end pt-1">{{ category[1].size }} posts</span>
      </a>
    </div>
  {% endfor %}
</div>
