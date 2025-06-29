---
layout: page
title: Categories
permalink: /categories/
---

<!-- This section lists all the category names as clickable buttons -->
<!-- We've added a data-category attribute for more reliable matching -->
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

<!-- This is the new, robust JavaScript -->
<script>
  document.addEventListener('DOMContentLoaded', function() {
    const postList = document.getElementById('post-list-all');
    if (!postList) return;

    const postItems = Array.from(postList.getElementsByClassName('post-item'));
    const categoryButtons = document.querySelectorAll('#category-buttons .btn');

    function filterPosts(category) {
      // Update active button state
      categoryButtons.forEach(button => {
        if (button.dataset.category === category) {
          button.classList.add('active');
        } else {
          button.classList.remove('active');
        }
      });

      // Show/hide posts based on the data-category attribute
      let postsFound = false;
      postItems.forEach(item => {
        if (category === 'all' || item.dataset.category === category) {
          item.style.display = 'list-item';
          postsFound = true;
        } else {
          item.style.display = 'none';
        }
      });
    }

    // Add click event listeners to all buttons
    categoryButtons.forEach(button => {
      button.addEventListener('click', function() {
        // Use the button's data-category to filter
        const categoryToFilter = this.dataset.category;
        filterPosts(categoryToFilter);
        // Update the URL hash without reloading the page
        if (history.pushState) {
          if (categoryToFilter === 'all') {
            history.pushState(null, null, '#');
          } else {
            history.pushState(null, null, '#' + encodeURIComponent(categoryToFilter));
          }
        }
      });
    });

    // This is the fix for links from other pages
    // Check for a category in the URL hash when the page loads
    function filterByUrlHash() {
      const hash = decodeURIComponent(window.location.hash.substring(1));
      if (hash) {
        filterPosts(hash);
      } else {
        filterPosts('all'); // Default to showing all posts
      }
    }
    
    // Initial filter on page load
    filterByUrlHash();
  });
</script>

<style>
  #category-buttons .btn {
    margin: 0.25rem;
  }
</style>
