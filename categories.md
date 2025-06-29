---
layout: page
title: Categories
permalink: /categories/
---

<div id="category-buttons" class="mb-4">
  <button class="btn btn-outline-primary active" onclick="filterPosts('all')">All Posts</button>
  {% assign categories = site.categories | sort %}
  {% for category in categories %}
    <button class="btn btn-outline-primary" onclick="filterPosts('{{ category[0] | escape }}')">{{ category[0] }} ({{ category[1].size }})</button>
  {% endfor %}
</div>

<div id="pagination-controls" class="mt-4">
  <button id="prev-page" class="btn btn-sm btn-outline-secondary" onclick="prevPage()" disabled>Previous</button>
  <span id="page-indicator" class="mx-2">Page 1</span>
  <button id="next-page" class="btn btn-sm btn-outline-secondary" onclick="nextPage()">Next</button>
</div>

<ul id="post-list" class="post-list">
  {% for post in site.posts %}
    <li class="post-item" data-category="{{ post.categories | first | escape }}">
      <h2 class="post-title">
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </h2>
      <span class="post-meta">{{ post.date | date: "%Y年%m月%d日" }}</span>
    </li>
  {% endfor %}
</ul>

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
    display: none;
  }
</style>

<script>
  // Global variables
  let currentCategory = 'all';
  let currentPage = 1;
  const postsPerPage = 5;
  let filteredPosts = [];
  
  // DOM elements
  const postItems = document.querySelectorAll('.post-item');
  const categoryButtons = document.querySelectorAll('#category-buttons .btn');
  const prevPageBtn = document.getElementById('prev-page');
  const nextPageBtn = document.getElementById('next-page');
  const pageIndicator = document.getElementById('page-indicator');
  
  // Initialize on page load
  document.addEventListener('DOMContentLoaded', function() {
    // Check for hash in URL
    const hash = window.location.hash.substring(1);
    if (hash) {
      filterPosts(decodeURIComponent(hash));
    } else {
      filterPosts('all');
    }
    
    // Handle browser back/forward
    window.addEventListener('popstate', function() {
      const hash = window.location.hash.substring(1);
      if (hash) {
        filterPosts(decodeURIComponent(hash));
      } else {
        filterPosts('all');
      }
    });
  });
  
  // Filter posts by category
  function filterPosts(category) {
    currentCategory = category;
    currentPage = 1;
    
    // Update active button
    categoryButtons.forEach(function(button) {
      if (button.textContent.includes(category) || 
         (category === 'all' && button.textContent.includes('All Posts'))) {
        button.classList.add('active');
      } else {
        button.classList.remove('active');
      }
    });
    
    // Filter posts
    filteredPosts = [];
    postItems.forEach(function(item) {
      if (category === 'all' || item.dataset.category === category) {
        filteredPosts.push(item);
      }
    });
    
    // Update URL
    if (category === 'all') {
      history.replaceState(null, null, window.location.pathname);
    } else {
      history.pushState(null, null, '#' + category);
    }
    
    // Update display
    updateDisplay();
  }
  
  // Update display based on current page and filter
  function updateDisplay() {
    // Calculate pagination
    const totalPages = Math.ceil(filteredPosts.length / postsPerPage);
    const startIndex = (currentPage - 1) * postsPerPage;
    const endIndex = Math.min(startIndex + postsPerPage, filteredPosts.length);
    
    // Update pagination controls
    pageIndicator.textContent = 'Page ' + currentPage + ' of ' + (totalPages || 1);
    prevPageBtn.disabled = currentPage <= 1;
    nextPageBtn.disabled = currentPage >= totalPages;
    
    // Hide all posts
    postItems.forEach(function(item) {
      item.style.display = 'none';
    });
    
    // Show only current page posts
    for (let i = startIndex; i < endIndex; i++) {
      filteredPosts[i].style.display = 'block';
    }
  }
  
  // Go to previous page
  function prevPage() {
    if (currentPage > 1) {
      currentPage--;
      updateDisplay();
    }
  }
  
  // Go to next page
  function nextPage() {
    const totalPages = Math.ceil(filteredPosts.length / postsPerPage);
    if (currentPage < totalPages) {
      currentPage++;
      updateDisplay();
    }
  }
</script>
