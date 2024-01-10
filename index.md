---
layout: archive
---

<div id="main" role="main">
  {% include sidebar.html %}

  <div class="archive">
    {% unless page.header.overlay_color or page.header.overlay_image %}
      <h1 id="page-title" class="page__title">{{ page.title }}</h1>
    {% endunless %}
    {% for post in page.posts %}
      {% include archive-single.html %}
    {% endfor %}
  </div>
</div>

<h3 class="archive__subtitle">{{ site.data.ui-text[site.locale].recent_posts | default: "Latest Posts" }}</h3>

{% include paginator.html %}