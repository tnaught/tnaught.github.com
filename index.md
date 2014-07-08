---
layout: default
title: Home
tagline: Supporting tagline
group: navigation
---
<!-- {% include JB/setup %} -->

<div class="blog-post">
  <h2 class="blog-post-title">{{ site.posts.first.title }}</h2>  
  <p class="blog-post-meta">
    {{ site.posts.first.date | date_to_long_string }}
  </p>
  <div class="content">{{site.posts.first.content}}</div>
  {% unless site.posts.first.categories == empty %}
    <ul class="tag_box inline">
      <li><i class="fa fa-folder-open"></i></li>
      {% assign categories_list = site.posts.first.categories %}
      {% include JB/categories_list %}
    </ul>
  {% endunless %}  

  {% unless site.posts.first.tags == empty %}
    <ul class="tag_box inline">
      <li><i class="fa fa-tags"></i></li>
      {% assign tags_list = site.posts.first.tags %}
      {% include JB/tags_list %}
    </ul>
  {% endunless %} 
   <div class="blog-pagination">
      <ul class="pager">
      {% if site.posts.first.previous %}
        <li><a href="{{ BASE_PATH }}{{ site.posts.first.previous.url }}" title="{{ site.posts.first.previous.title }}">&larr; Previous</a></li>
      {% else %}
        <li class="disabled"><a>&larr; Previous</a></li>
      {% endif %}
       
      {% if site.posts.first.next %}
        <li><a href="{{ BASE_PATH }}{{ site.posts.first.next.url }}" title="{{ site.posts.first.next.title }}">Next &rarr;</a></li>
      {% else %}
        <li class="disabled"><a>Next &rarr;</a></li>
      {% endif %}
      </ul>
    </div>
    <hr>
    {% include JB/comments %}
</div>

