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

<!-- Simple JavaScript with pagination -->
<script>
  document.addEventListener('DOMContentLoaded', function() {
    // Get DOM elements
    const postList = document.getElementById('post-list-all');
    const categoryButtons = document.querySelectorAll('#category-buttons .btn');
    const prevPageBtn = document.getElementById('prev-page');
    const nextPageBtn = document.getElementById('next-page');
    const pageIndicator = document.getElementById('page-indicator');
    
    if (!postList) return;
    
    // Get all post items
    const postItems = Array.from(postList.getElementsByClassName('post-item'));
    
    // Pagination settings
    const postsPerPage = 5;
    let currentPage = 1;
    let filteredPosts = [];
    
    // Show posts for current page and category
    function updateDisplay() {
      // Calculate pagination
      const totalPages = Math.ceil(filteredPosts.length / postsPerPage);
      const startIndex = (currentPage - 1) * postsPerPage;
      const endIndex = Math.min(startIndex + postsPerPage, filteredPosts.length);
      
      // Update pagination controls
      pageIndicator.textContent = `Page ${currentPage} of ${totalPages || 1}`;
      prevPageBtn.disabled = currentPage <= 1;
      nextPageBtn.disabled = currentPage >= totalPages;
      
      // Hide all posts
      postItems.forEach(item => {
        item.style.display = 'none';
      });
      
      // Show only current page posts
      for (let i = startIndex; i < endIndex; i++) {
        if (filteredPosts[i]) {
          filteredPosts[i].style.display = 'list-item';
        }
      }
    }
    
    // Filter posts by category
    function filterByCategory(category) {
      // Reset to first page
      currentPage = 1;
      
      // Update active button
      categoryButtons.forEach(btn => {
        btn.classList.remove('active');
        if (btn.dataset.category === category) {
          btn.classList.add('active');
        }
      });
      
      // Filter posts
      if (category === 'all') {
        filteredPosts = [...postItems];
      } else {
        filteredPosts = postItems.filter(item =>
          item.dataset.category === category
        );
      }
      
      // Update display
      updateDisplay();
    }
    
    // Add click handlers to category buttons
    categoryButtons.forEach(button => {
      button.addEventListener('click', function() {
        const category = this.dataset.category;
        filterByCategory(category);
        
        // Update URL
        if (category === 'all') {
          history.replaceState(null, null, window.location.pathname);
        } else {
          history.pushState(null, null, '#' + category);
        }
      });
    });
    
    // Add click handlers to pagination buttons
    prevPageBtn.addEventListener('click', function() {
      if (currentPage > 1) {
        currentPage--;
        updateDisplay();
      }
    });
    
    nextPageBtn.addEventListener('click', function() {
      const totalPages = Math.ceil(filteredPosts.length / postsPerPage);
      if (currentPage < totalPages) {
        currentPage++;
        updateDisplay();
      }
    });
    
    // Handle URL hash on page load
    function handleUrlHash() {
      const hash = window.location.hash.substring(1);
      if (hash) {
        filterByCategory(decodeURIComponent(hash));
      } else {
        filterByCategory('all');
      }
    }
    
    // Initialize page
    handleUrlHash();
    
    // Handle browser back/forward
    window.addEventListener('popstate', handleUrlHash);
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
