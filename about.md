---
title: About
theme: jekyll-theme-slate
layout: default
---
<ul>
   {% for item in site.data.sitelist.docs %}
      <a href="{{ item.url }}">{{ item.title }}</a> - 
   {% endfor %}
</ul>

Just a cybersecurity guy with a love for learning new things. I'm passionate about red teaming, reversing, and analysis. I keep a blog to help remember the things that I've learned and hopefully, to help others as they learn.

> A little learning is a dangerous thing;  
> Drink deep, or taste not the Pierian spring:  
> There shallow draughts intoxicate the brain,  
> And drinking largely sobers us again.  
> -Alexander Pope
