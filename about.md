---
title: About
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

Just a cyber nerd with a love for learning new things. I'm passionate about red teaming, reversing, and analysis. I blog to help myself remember the things I've learned and to help others as they learn (hopefully!).

> A little learning is a dangerous thing;  
> Drink deep, or taste not the Pierian spring:  
> There shallow draughts intoxicate the brain,  
> And drinking largely sobers us again.  
> -Alexander Pope
