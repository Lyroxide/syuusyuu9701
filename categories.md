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
    <li class="post-item" data-category="{{ post.categories | first | escape }}">
      <h2 class="post-title" style="margin-bottom: 0.2rem;">
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </h2>
      <span class="post-meta">{{ post.date | date: "%Y年%m月%d日" }}</span>
    </li>
  {% endfor %}
</ul>

<!-- Pagination controls -->
<div id="pagination-controls" class="mt-4">
  <button id="prev-page" class="btn btn-sm btn-outline-secondary" disabled>Previous</button>
  <span id="page-indicator" class="mx-2">Page 1</span>
  <button id="next-page" class="btn btn-sm btn-outline-secondary">Next</button>
</div>

<!-- Updated JavaScript with pagination -->
<script>
  document.addEventListener('DOMContentLoaded', function() {
    const postList = document.getElementById('post-list-all');
    if (!postList) return;

    const postItems = Array.from(postList.getElementsByClassName('post-item'));
    const categoryButtons = document.querySelectorAll('#category-buttons .btn');
    
    // Pagination elements
    const prevPageBtn = document.getElementById('prev-page');
    const nextPageBtn = document.getElementById('next-page');
    const pageIndicator = document.getElementById('page-indicator');
    
    // Pagination variables
    const postsPerPage = 5;
    let currentPage = 1;
    let filteredPosts = [];
    let currentCategory = 'all';
    
    // Filter posts by category and update pagination
    function filterPosts(category) {
      currentCategory = category;
      currentPage = 1; // Reset to first page when changing categories
      
      categoryButtons.forEach(button => {
        button.classList.toggle('active', button.dataset.category === category);
      });

      // Filter posts by category
      filteredPosts = postItems.filter(item =>
        category === 'all' || item.dataset.category === category
      );
      
      // Hide all posts first
      postItems.forEach(item => {
        item.style.display = 'none';
      });
      
      // Update pagination and display current page
      updatePagination();
    }
    
    // Update pagination controls and display current page of posts
    function updatePagination() {
      const totalPages = Math.ceil(filteredPosts.length / postsPerPage);
      
      // Update page indicator
      pageIndicator.textContent = `Page ${currentPage} of ${totalPages || 1}`;
      
      // Enable/disable pagination buttons
      prevPageBtn.disabled = currentPage <= 1;
      nextPageBtn.disabled = currentPage >= totalPages || totalPages === 0;
      
      // Calculate which posts to show
      const startIndex = (currentPage - 1) * postsPerPage;
      const endIndex = startIndex + postsPerPage;
      
      // Show only posts for current page
      filteredPosts.forEach((item, index) => {
        item.style.display = (index >= startIndex && index < endIndex) ? 'list-item' : 'none';
      });
    }
    
    // Event listeners for pagination buttons
    prevPageBtn.addEventListener('click', function() {
      if (currentPage > 1) {
        currentPage--;
        updatePagination();
      }
    });
    
    nextPageBtn.addEventListener('click', function() {
      const totalPages = Math.ceil(filteredPosts.length / postsPerPage);
      if (currentPage < totalPages) {
        currentPage++;
        updatePagination();
      }
    });

    // Category button click handlers
    categoryButtons.forEach(button => {
      button.addEventListener('click', function() {
        const categoryToFilter = this.dataset.category;
        filterPosts(categoryToFilter);
        
        // Update the URL hash without reloading the page
        if (history.pushState) {
          if (categoryToFilter === 'all') {
            history.replaceState(null, null, window.location.pathname + window.location.search);
          } else {
            history.pushState(null, null, '#' + encodeURIComponent(categoryToFilter));
          }
        }
      });
    });

    // Handle URL hash changes (e.g., from links or browser history)
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
  
  .btn.active {
    background-color: #007bff;
    color: white;
  }
  
  #pagination-controls {
    text-align: center;
    margin-top: 2rem;
    margin-bottom: 2rem;
  }
  
  .post-item {
    margin-bottom: 1.5rem;
  }
</style>
