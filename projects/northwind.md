---
layout: default
title: "Northwind Data Analysis"
permalink: /projects/northwind/
---

<div class="project-page">

  <div class="project-page-image">
    <img
      src="{{ '/assets/images/Northwind_cardphoto.jpg' | relative_url }}"
      alt="Northwind SQL and Power BI analysis">
  </div>

  <h1>Northwind Data Analysis</h1>

  <div class="project-page-tags">
    <span>SQL</span>
    <span>SQL Server</span>
    <span>Power BI</span>
  </div>

  <h2>About the Project</h2>

  <p>
    This project combines SQL Server and Power BI to analyse
    the Northwind dataset, covering data import, SQL querying,
    advanced analysis and interactive dashboard development.
  </p>

  <h2>Project Posts</h2>

  {% assign northwind_posts = site.posts | where_exp: "post", "post.title contains 'Northwind'" %}

  {% for post in northwind_posts %}

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