---
layout: default
title: Ellie's Blog!
tagline: Supporting tagline
---
<!-- {% include JB/setup %} -->

<div class="page-header">
  <h1>{{ site.posts.first.title }} {% if site.posts.first.tagline %}<small>{{site.posts.first.tagline}}</small>{% endif %}</h1>
</div>

<div class="row-fluid post-full">
  <div class="span12">
    <div class="date">
      <span>{{ site.posts.first.date | date_to_long_string }}</span>
    </div>
    <div class="content">
      {{ site.posts.first.content }}
    </div>

  {% unless site.posts.first.categories == empty %}
    <ul class="tag_box inline">
      <li><i class="icon-folder-open"></i></li>
      {% assign categories_list = site.posts.first.categories %}
      {% include JB/categories_list %}
    </ul>
  {% endunless %}  

  {% unless site.posts.first.tags == empty %}
    <ul class="tag_box inline">
      <li><i class="icon-tags"></i></li>
      {% assign tags_list = site.posts.first.tags %}
      {% include JB/tags_list %}
    </ul>
  {% endunless %}  

    <hr>
    <div class="pagination">
      <ul>
      {% if site.posts.first.previous %}
        <li class="prev"><a href="{{ BASE_PATH }}{{ site.posts.first.previous.url }}" title="{{ site.posts.first.previous.title }}">&larr; Previous</a></li>
      {% else %}
        <li class="prev disabled"><a>&larr; Previous</a></li>
      {% endif %}
        <li><a href="{{ BASE_PATH }}{{ site.JB.archive_path }}">Archive</a></li>
      {% if site.posts.first.next %}
        <li class="next"><a href="{{ BASE_PATH }}{{ site.posts.first.next.url }}" title="{{ site.posts.first.next.title }}">Next &rarr;</a></li>
      {% else %}
        <li class="next disabled"><a>Next &rarr;</a></li>
      {% endif %}
      </ul>
    </div>
    <hr>
    {% include JB/comments %}
  </div>
</div>


