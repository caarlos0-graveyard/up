---
layout: post
title: The Security Magic Bullet
---

> Screw input validation, love output normalization.

That was a recent comment on Hacker News, probably made by a programmer. How are there still people that don't understand *there's no magic bullet*? 

0. Assume hostility
1. Input validation
2. Enforce business rules
3. Output sanitization

Output normalization fixes your XSS attacks and SQL injections, sure. But it doesn't help at all if I send 'admin=true' to your `/users/me/update` URL or I give you a comma-separated list of 50,000 emails to add to your mailing list.