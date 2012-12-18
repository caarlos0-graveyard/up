---
layout: post
title: "Weekend Hacking: Diamond Invoice Parser"
date: 2011-12-18 21:23
comments: true
categories: software
---

Way back in the day as one of the first projects I ever did for pay, I wrote a
parser for [Comic Town][ct]'s website that took in their Diamond Comic
Distributor invoice files and spit out a list of the incoming items. It wasn't
very sophisticated, and it was baked into the rest of the PHP file that
generated that particular HTML page, but it mostly worked.

Now, beacuse of my need for a quick weekend project, you can have this same
basic functionality in a convienent RubyGem! Presenting,
[diamond_invoice][github_page].

It works like this:

{% codeblock parse_invoice_example.rb %}
invoice = DiamondInvoice.new(File.read('invoice.txt'))
invoice.new_items
# => ['avengers #257', 'batman: the brave and the bold special tpb', ...]
{% endcodeblock %}

I'll be damned shocked if anyone ever uses this in anger, but if you want to
critique my coding/testing style, here's a great project to do it on.


[ct]: http://comictown.net Comic Town in Columbus, OH
[github_page]: http://github.com/joncanady/diamond_invoice
