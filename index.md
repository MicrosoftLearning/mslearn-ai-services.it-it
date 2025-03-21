---
title: Esercizi sui Servizi di Azure AI
permalink: index.html
layout: home
---

# Esercizi sui Servizi di Azure AI

Gli esercizi seguenti sono progettati a supporto dei moduli su Microsoft Learn.


{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Exercises'" %} {% for activity in labs  %} {% unless activity.url contains 'ai-foundry' %}
- [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {% endunless %} {% endfor %}
