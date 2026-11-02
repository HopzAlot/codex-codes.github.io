---
layout: page
title: Blog
permalink: /blog/
description: Learning notes and writeups.
nav: true
nav_order: 4
pagination:
  enabled: true
  collection: posts
  permalink: /page/:num/
  per_page: 5
  sort_field: date
  sort_reverse: true
  trail:
    before: 1
    after: 3
---

<div class="post">
  <ul class="post-list">
    {% if page.pagination.enabled %}
      {% assign postlist = paginator.posts %}
    {% else %}
      {% assign postlist = site.posts %}
    {% endif %}

    {% for post in postlist %}
      <li>
        {% assign read_time = post.content | number_of_words | divided_by: 180 | plus: 1 %}
        {% assign year = post.date | date: "%Y" %}
        {% assign tags = post.tags | join: "" %}
        {% assign categories = post.categories | join: "" %}

        <h3>
          {% if post.redirect == blank %}
            <a class="post-title" href="{{ post.url | relative_url }}">{{ post.title }}</a>
          {% elsif post.redirect contains "://" %}
            <a class="post-title" href="{{ post.redirect }}" target="_blank">{{ post.title }}</a>
            <svg width="2rem" height="2rem" viewBox="0 0 40 40" xmlns="http://www.w3.org/2000/svg">
              <path d="M17 13.5v6H5v-12h6m3-3h6v6m0-6-9 9" class="icon_svg-stroke" stroke="#999" stroke-width="1.5" fill="none" fill-rule="evenodd" stroke-linecap="round" stroke-linejoin="round"></path>
            </svg>
          {% else %}
            <a class="post-title" href="{{ post.redirect | relative_url }}">{{ post.title }}</a>
          {% endif %}
        </h3>

        <p>{{ post.description }}</p>

        <p class="post-meta">
          {{ read_time }} min read &nbsp; &middot; &nbsp;
          <a href="{{ year | prepend: '/blog/' | relative_url }}">
            <i class="fa-solid fa-calendar fa-sm"></i> {{ year }}
          </a>

          {% if tags != "" %}
            &nbsp; &middot; &nbsp;
            {% for tag in post.tags %}
              <a href="{{ tag | slugify | prepend: '/blog/tag/' | relative_url }}">
                <i class="fa-solid fa-hashtag fa-sm"></i> {{ tag }}</a>
              {% unless forloop.last %}
                &nbsp;
              {% endunless %}
            {% endfor %}
          {% endif %}

          {% if categories != "" %}
            &nbsp; &middot; &nbsp;
            {% for category in post.categories %}
              <a href="{{ category | slugify | prepend: '/blog/category/' | relative_url }}">
                <i class="fa-solid fa-tag fa-sm"></i> {{ category }}</a>
              {% unless forloop.last %}
                &nbsp;
              {% endunless %}
            {% endfor %}
          {% endif %}
        </p>
      </li>
    {% endfor %}
  </ul>

  {% if page.pagination.enabled %}
    {% include pagination.liquid %}
  {% endif %}
</div>

