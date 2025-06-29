---
layout: page
title: Categories
permalink: /categories/
---

<!-- This section lists all the category names as clickable buttons -->
<div id="category-buttons" class="mb-4">
  <button class="btn btn-outline-primary active" onclick="filterPosts('all')">All Posts</button>
  {% assign categories = site.categories | sort %}
  {% for category in categories %}
    <button class="btn btn-outline-primary" onclick="filterPosts('{{ category[0] | escape }}')">{{ category[0] }} ({{ category[1].size }})</button>
  {% endfor %}
</div>

<!-- This section contains a list of ALL posts, initially hidden -->
<ul id="post-list-all" class="post-list">
  {% for post in site.posts %}
    <li class="post-item" data-category="{{ post.categories | first | escape }}" style="display: none;">
      <h2 class="post-title" style="margin-bottom: 0.2rem;">
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </h2>
      <span class="post-meta">{{ post.date | date: "%Y年%m月%d日" }}</span>
    </li>
  {% endfor %}
</ul>

<!-- This is the JavaScript that does the filtering -->
<script>
  const postList = document.getElementById('post-list-all');
  const postItems = Array.from(postList.getElementsByClassName('post-item'));
  const categoryButtons = document.querySelectorAll('#category-buttons .btn');
  let currentCategory = 'all';

  function filterPosts(category) {
    // Update active button
    categoryButtons.forEach(button => {
      if (button.innerText.startsWith(category) || (category === 'all' && button.innerText === 'All Posts')) {
        button.classList.add('active');
      } else {
        button.classList.remove('active');
      }
    });

    // Show/hide posts
    postItems.forEach(item => {
      if (category === 'all' || item.dataset.category === category) {
        item.style.display = 'list-item';
      } else {
        item.style.display = 'none';
      }
    });
    currentCategory = category;
  }

  // Show all posts by default when the page loads
  document.addEventListener('DOMContentLoaded', function() {
    filterPosts('all');
  });
</script>

<style>
  #category-buttons .btn {
    margin: 0.25rem;
  }
</style>
