---
title: blog
theme: jekyll-theme-slate
layout: default
---
<nav>
	<ul>
		<a href="/">Home</a> - <a href="/resources">Resources</a> - <a href="/blog">Blog</a> - <a href="/about">About</a>
	</ul>
</nav>
  
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> » <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
