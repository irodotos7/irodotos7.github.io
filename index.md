---
layout: default
title: Home
---

{% assign featured_post = site.posts.first %}
{% assign latest_post_date = site.posts.first.date | date: "%B %Y" %}

<section class="home-hero">
  <p class="eyebrow">Software Writing</p>
  <h1>Engineering notes that turn tricky backend problems into practical solutions.</h1>
  <p class="hero-copy">
    I write about Scala, AWS, Git workflows, and the kinds of production problems that usually only show up once a system starts carrying real weight.
  </p>

  <div class="hero-meta">
    <div class="hero-stat">
      <span class="hero-stat-value">{{ site.posts | size }}</span>
      <span class="hero-stat-label">Published posts</span>
    </div>
    <div class="hero-stat">
      <span class="hero-stat-value">{{ site.tags | size }}</span>
      <span class="hero-stat-label">Topics covered</span>
    </div>
    <div class="hero-stat">
      <span class="hero-stat-value">{{ latest_post_date }}</span>
      <span class="hero-stat-label">Latest article</span>
    </div>
  </div>
</section>

<section class="home-featured">
  <div class="section-heading">
    <p class="eyebrow">Featured</p>
    <h2>Latest deep dive</h2>
  </div>

  <a class="featured-card" href="{{ featured_post.url | relative_url }}">
    <p class="featured-date">{{ featured_post.date | date: "%B %-d, %Y" }}</p>
    <h3>{{ featured_post.title }}</h3>
    <p>{{ featured_post.excerpt | strip_html | strip_newlines | replace: "Setting the scene", "" | replace: "setting the scene", "" | truncate: 210 }}</p>
    <span class="featured-link">Read the article</span>
  </a>
</section>

<section class="home-posts" id="writing">
  <div class="section-heading">
    <p class="eyebrow">Archive</p>
    <h2>Selected writing</h2>
  </div>

  <div class="post-grid">
    {% for post in site.posts %}
      <article class="post-card">
        <p class="post-card-date">{{ post.date | date: "%b %-d, %Y" }}</p>
        <h3><a href="{{ post.url | relative_url }}">{{ post.title }}</a></h3>
        <p class="post-card-copy">{{ post.excerpt | strip_html | strip_newlines | replace: "Setting the scene", "" | replace: "setting the scene", "" | truncate: 180 }}</p>
        {% if post.tags %}
          <ul class="tag-list">
            {% for tag in post.tags limit: 4 %}
              <li>{{ tag }}</li>
            {% endfor %}
          </ul>
        {% endif %}
      </article>
    {% endfor %}
  </div>
</section>

