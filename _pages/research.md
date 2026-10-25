---
layout: page
title: research
permalink: /research/
nav: true
nav_order: 2
description: Selected research by theme.
display_categories: ["Weather risk, risk management, and assistance", "Temperature effects", "Meta science and many-researcher projects"]
category_intros:
  "Meta science and many-researcher projects": "I am interested in how research itself is conducted, and in what empirical work can and cannot deliver. In recent years, I have contributed to several meta-science projects."
---

<div class="research-list">
{% for category in page.display_categories %}
  <h2 class="research-category">{{ category }}</h2>
  {% assign intro = page.category_intros[category] %}
  {% if intro %}<p class="research-intro">{{ intro }}</p>{% endif %}
  {% assign items = site.projects | where: "category", category | sort: "importance" %}
  {% for project in items %}
  <div class="research-item">
    <h3 class="research-title">{{ project.title }}</h3>
    {% if project.authors or project.journal or project.status %}
    <p class="research-meta">
      {% if project.authors %}<span class="research-authors">{{ project.authors }}</span>{% endif %}
      {% if project.journal %}<br><em>{{ project.journal }}</em>{% endif %}
      {% if project.status %}<br><span class="research-status">{{ project.status }}</span>{% endif %}
    </p>
    {% endif %}
    <div class="research-body">
      {% if project.img %}
      <div class="research-figure">
        {% include figure.liquid loading="eager" path=project.img sizes="(min-width: 768px) 38vw, 100vw" alt=project.title %}
      </div>
      {% endif %}
      <div class="research-text">
        {{ project.content }}
      </div>
    </div>
  </div>
  {% endfor %}
{% endfor %}
</div>

<style>
.research-category {
  margin-top: 2.5rem;
  margin-bottom: 1rem;
  padding-bottom: 0.4rem;
  border-bottom: 1px solid var(--global-divider-color);
}
.research-intro {
  margin-bottom: 1.5rem;
  color: var(--global-text-color);
}
.research-item {
  margin-bottom: 3rem;
}
.research-title {
  font-size: 1.4rem;
  margin-bottom: 0.4rem;
}
.research-meta {
  margin-bottom: 1rem;
}
.research-authors {
  color: var(--global-text-color-light);
}
.research-meta em {
  color: var(--global-text-color);
  font-style: italic;
}
.research-status {
  color: var(--global-text-color);
}
.research-body {
  display: flex;
  flex-wrap: wrap;
  gap: 1.5rem;
  align-items: flex-start;
}
.research-figure {
  flex: 0 0 38%;
  max-width: 38%;
}
.research-figure img {
  width: 100%;
  height: auto;
}
.research-text {
  flex: 1;
  min-width: 0;
}
@media (max-width: 768px) {
  .research-figure, .research-text {
    flex: 0 0 100%;
    max-width: 100%;
  }
}
</style>