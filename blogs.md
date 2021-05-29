---
title: Blogs
layout: default
bodyClass: page-blogs-list
---

<div class="intro">
  <div class="container">
    <div class="row">
      <div class="col-12">
        <h1>What are we up to?</h1>
        <p>
          This page provides an overview of all the ongoing and past activities.
        </p>
      </div>
    </div>
  </div>
</div>

<div class="container pb-6">
  <div class="row">
    {% for blog in site.blogs %}
    <div class="col-12 col-md-4 mb-1">
      <div class="blog blog-summary">
        <div class="blog-content">
          <h2 class="blog-title">
            <a href="{{site.baseurl}}{{ blog.url }}">{{ blog.title }}</a>
          </h2>
          {{ blog.content | markdownify | strip_html | truncate: 100 }}
        </div>
      </div>
    </div>
    {% endfor %}
  </div>
</div>
