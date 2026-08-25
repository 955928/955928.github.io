---
layout: default
title: "Superstore Analysis"
permalink: /projects/superstore/
---

<div class="project-page">

  <div class="project-page-image">
    <img
      src="{{ '/assets/images/Superstore/PowerBI/POST_IMAGE.png' | relative_url }}"
      alt="Superstore Power BI analysis">
  </div>

  <h1>Superstore Analysis</h1>

  <div class="project-page-tags">
    <span>Power BI</span>
    <span>Power Query</span>
    <span>DAX</span>
  </div>

  <h2>About the Project</h2>

  <p>
    This project focuses on analysing the Superstore dataset
    using Power BI, covering data preparation, transformation,
    modelling, DAX calculations and interactive visualisation.
  </p>

  <h2>Project Posts</h2>

  {% assign superstore_posts = site.posts | where_exp: "post", "post.title contains 'Superstore'" %}

  {% for post in superstore_posts %}

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