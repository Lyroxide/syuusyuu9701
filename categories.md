---
layout: page
title: Categories
permalink: /categories/
---

<!-- This section lists all the category names as clickable buttons.
     The 'onclick' attributes have been REMOVED. -->
<div id="category-buttons" class="mb-4">
  <button class="btn btn-outline-primary" data-category="all">All Posts</button>
  {% assign categories = site.categories | sort %}
  {% for category in categories %}
    <button class="btn btn-outline-primary" data-category="{{ category[0] | escape }}">{{ category[0] }} ({{ category[1].size }})</button>
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

<!-- This is the final, correct JavaScript -->
<script>
  document.addEventListener('DOMContentLoaded', function() {
    const postList = document.getElementById('post-list-all');
    if (!postList) return;

    const postItems = Array.from(postList.getElementsByClassName('post-item'));
    const categoryButtons = document.querySelectorAll('#category-buttons .btn');

    // This function is now defined inside the DOMContentLoaded listener,
    // so it's guaranteed to exist before any events are attached.
    function filterPosts(category) {
      categoryButtons.forEach(button => {
        button.classList.toggle('active', button.dataset.category === category);
      });

      postItems.forEach(item => {
        const shouldBeVisible = (category === 'all' || item.dataset.category === category);
        item.style.display = shouldBeVisible ? 'list-item' : 'none';
      });
    }

    // This is the FIX. We attach listeners here instead of using onclick.
    categoryButtons.forEach(button => {
      button.addEventListener('click', function() {
        const categoryToFilter = this.dataset.category;
        filterPosts(categoryToFilter);
        
        // Update the URL hash without reloading the page
        if (history.pushState) {
          if (categoryToFilter === 'all') {
            // Use replaceState for the default to not clutter history
            history.replaceState(null, null, window.location.pathname + window.location.search);
          } else {
            history.pushState(null, null, '#' + encodeURIComponent(categoryToFilter));
          }
        }
      });
    });

    // This part handles links from other pages (e.g., from a post page)
    function filterByUrlHash() {
      const hash = decodeURIComponent(window.location.hash.substring(1));
      if (hash) {
        filterPosts(hash);
      } else {
        filterPosts('all'); // Default to showing all posts
      }
    }
    
    // Initial filter when the page loads
    filterByUrlHash();

    // Listen for back/forward browser button presses
    window.addEventListener('popstate', filterByUrlHash);
  });
</script>

<style>
  #category-buttons .btn {
    margin: 0.25rem;
  }
</style>
