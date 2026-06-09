---
layout: post
date: 2026-06-09 09:30:00-0400
inline: true
related_posts: false
---

{% assign latest_post = site.posts | first %}
Latest blog post: <a href="{{ latest_post.url | relative_url }}">{{ latest_post.title }}</a> ({{ latest_post.date | date: "%b %d, %Y" }}).
