---
title: Tags
permalink: /tags
layout: pages
---

<style>
.tag-box{
    list-style:none;
    margin: 1rem 0;
    padding:4px 0;
    overflow:hidden;*zoom:1}
.tag-box.inline li{
    float:left;
    font-size:0.875rem;
    line-height:1.75}
.tag-box a{
    padding:4px 6px;
    margin:2px;
    background-color:#e6e6e6;
    -webkit-border-radius:4px;
    -moz-border-radius:4px;
    border-radius:4px;
    text-decoration:none}
.tag-box a span{
    vertical-align:super;
    font-size:0.625rem}
</style>

{% comment %} 
    Liquid markup lifted from: http://pavdmyt.com/how-to-implement-tags-at-jekyll-website/
{% endcomment %} 

{% comment %} 
    Get the tag name for every tag on the site and set themto the `site_tags` variable.
{% endcomment %}

{% capture site_tags %}{% for tag in site.tags %}{{ tag | first }}{% unless forloop.last %},{% endunless %}{% endfor %}{% endcapture %}

{% comment %}
    `tag_words` is a sorted array of the tag names.
{% endcomment %}

{% assign tag_words = site_tags | split:',' | sort %}

{% comment %}
    Build the Page, List of all tags.
{% endcomment %}

<ul class="tag-box inline">
  {% for item in (0..site.tags.size) %}{% unless forloop.last %}

    {% capture this_word %}{{ tag_words[item] }}{% endcapture %}

    <li>
      <a href="#{{ this_word | cgi_escape }}" class="tag">{{ this_word }}
        <span>({{ site.tags[this_word].size }})</span>
      </a>
    </li>

  {% endunless %}{% endfor %}
</ul>

--- --- ---

{% comment %}
    Posts by Tag
{% endcomment %}

<div>
  {% for item in (0..site.tags.size) %}{% unless forloop.last %}

    {% capture this_word %}{{ tag_words[item] }}{% endcapture %}

    <h3 id="{{ this_word | cgi_escape }}">{{ this_word }}</h3>

    {% for post in site.tags[this_word] %}{% if post.title != null %}

      <div>
        <span style="float: left;">
          <a href="{{ post.url }}">{{ post.title | truncate: 46 }}</a>
        </span>
        <span style="float: right;">
          {{ post.date | date_to_string }}
        </span>
      </div>
      <div style="clear: both;"></div>

    {% endif %}{% endfor %}

  {% endunless %}{% endfor %}
</div>
<br />
