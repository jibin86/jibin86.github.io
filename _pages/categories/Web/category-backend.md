---
title: "Back-End"
layout: archive
permalink: categories/web/backend
author_profile: true
sidebar_main: true
---


{% assign posts = site.categories.Back-End %}
{% for post in posts %} {% include archive-single.html type=page.entries_layout %} {% endfor %}