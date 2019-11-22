---
title: blog
theme: jekyll-theme-slate
layout: default
---
<nav>
	<ul>
   		{% for item in site.data.sitelist.docs %}
      			<a href="{{ item.url }}">{{ item.title }}</a> - 
   		{% endfor %}
	</ul>
</nav>
  
<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> » <a href="{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
