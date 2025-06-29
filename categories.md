---
layout: page
title: Categories
permalink: /categories/
---

<div class="categories">
  <h2 class="post-list-heading">Categories</h2>
  
  <div class="category-list">
    <a href="javascript:;" data-category="all" class="category-link active">
      All
    </a>
    
    {% assign categories = site.categories | sort %}
    {% for category in categories %}
      <a href="javascript:;" data-category="{{ category[0] | escape }}" class="category-link">
        {{ category[0] }} ({{ category[1].size }})
      </a>
    {% endfor %}
  </div>
  
  <div class="post-wrapper">
    <div id="post-list">
      {% for post in site.posts %}
        <div class="post-item" data-category="{{ post.categories | first | escape }}">
          <h3 class="post-title">
            <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
          </h3>
          <span class="post-date">{{ post.date | date: "%Y年%m月%d日" }}</span>
        </div>
      {% endfor %}
    </div>
    
    <div id="pagination" class="pagination">
      <button id="prev-page" class="btn btn-sm btn-outline-primary" disabled>Previous</button>
      <span id="page-indicator">Page 1</span>
      <button id="next-page" class="btn btn-sm btn-outline-primary">Next</button>
    </div>
  </div>
</div>

<style>
  .category-list {
    margin-bottom: 2rem;
  }
  
  .category-link {
    display: inline-block;
    margin: 0.25rem;
    padding: 0.375rem 0.75rem;
    border: 1px solid #007bff;
    border-radius: 0.25rem;
    color: #007bff;
    text-decoration: none;
  }
  
  .category-link.active {
    background-color: #007bff;
    color: white;
  }
  
  .post-item {
    margin-bottom: 1.5rem;
    display: none;
  }
  
  .pagination {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 2rem;
  }
  
  .pagination button {
    margin: 0 0.5rem;
  }
  
  #page-indicator {
    margin: 0 0.5rem;
  }
</style>

<script>
  document.addEventListener('DOMContentLoaded', function() {
    // Variables
    const postsPerPage = 5;
    let currentPage = 1;
    let currentCategory = 'all';
    
    // DOM elements
    const categoryLinks = document.querySelectorAll('.category-link');
    const postItems = document.querySelectorAll('.post-item');
    const prevPageBtn = document.getElementById('prev-page');
    const nextPageBtn = document.getElementById('next-page');
    const pageIndicator = document.getElementById('page-indicator');
    
    // Helper function to show posts for current page and category
    function updateDisplay() {
      // Get posts for current category
      const filteredPosts = Array.from(postItems).filter(post => {
        return currentCategory === 'all' || post.dataset.category === currentCategory;
      });
      
      // Calculate pagination
      const totalPages = Math.max(1, Math.ceil(filteredPosts.length / postsPerPage));
      const startIndex = (currentPage - 1) * postsPerPage;
      const endIndex = Math.min(startIndex + postsPerPage, filteredPosts.length);
      
      // Update pagination controls
      pageIndicator.textContent = `Page ${currentPage} of ${totalPages}`;
      prevPageBtn.disabled = currentPage <= 1;
      nextPageBtn.disabled = currentPage >= totalPages;
      
      // Hide all posts
      postItems.forEach(post => {
        post.style.display = 'none';
      });
      
      // Show posts for current page
      for (let i = startIndex; i < endIndex; i++) {
        if (filteredPosts[i]) {
          filteredPosts[i].style.display = 'block';
        }
      }
    }
    
    // Category link click handler
    categoryLinks.forEach(link => {
      link.addEventListener('click', function(e) {
        e.preventDefault();
        
        // Update active category
        categoryLinks.forEach(l => l.classList.remove('active'));
        this.classList.add('active');
        
        // Update current category and reset to page 1
        currentCategory = this.dataset.category;
        currentPage = 1;
        
        // Update display
        updateDisplay();
      });
    });
    
    // Pagination button handlers
    prevPageBtn.addEventListener('click', function() {
      if (currentPage > 1) {
        currentPage--;
        updateDisplay();
      }
    });
    
    nextPageBtn.addEventListener('click', function() {
      const filteredPosts = Array.from(postItems).filter(post => {
        return currentCategory === 'all' || post.dataset.category === currentCategory;
      });
      
      const totalPages = Math.ceil(filteredPosts.length / postsPerPage);
      
      if (currentPage < totalPages) {
        currentPage++;
        updateDisplay();
      }
    });
    
    // Initialize display
    updateDisplay();
  });
</script>
