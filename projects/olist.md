---
layout: default
title: "Olist E-Commerce Analysis"
permalink: /projects/olist/
---

<div class="project-page">

  <div class="project-page-image">
    <img
      src="{{ '/assets/images/Olist_cardphoto.jpeg' | relative_url }}"
      alt="Olist e-commerce analysis">
  </div>

  <h1>Olist E-Commerce Analysis</h1>

  <div class="project-page-tags">
    <span>Python</span>
    <span>Pandas</span>
    <span>Data Analysis</span>
  </div>

  <h2>About the Project</h2>

  <p>
    This project explores the Brazilian Olist e-commerce dataset
    using Python and Pandas, following the complete data analysis
    workflow from raw data to meaningful insights.
  </p>

  <h2>Project Posts</h2>

  {% assign olist_posts = site.posts | where_exp: "post", "post.title contains 'Olist'" %}

  {% for post in olist_posts %}

  <a class="project-post-card" href="{{ post.url | relative_url }}">

    {% if post.image and post.image.path %}

    <div class="project-post-image">
      <img
        src="{{ post.image.path | relative_url }}"
        alt="{{ post.image.alt | default: post.title }}">
    </div>

    {% endif %}

    <div class="project-post-content">

      <h3>{{ post.title }}</h3>

      <time datetime="{{ post.date | date_to_xmlschema }}">
        {{ post.date | date: "%B %d, %Y" }}
      </time>

    </div>

  </a>

  {% endfor %}

</div>