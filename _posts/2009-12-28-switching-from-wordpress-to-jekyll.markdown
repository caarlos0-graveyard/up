---
layout: post
title: Switching to Jekyll
---

Overnight there's been some pretty major changes around here.

## Design

Inspired by various blogs and products, I've taken up a more minimalist design. I've long since grown tired of overly-active blog themes with way too much cruft in the sidebars that detracts from the actual content. It's a work-in-progress, and comments are welcome.  Speaking of comments:

## Comments Gone

As a side effect of updating blog software (more below), comments are now gone. The new system doesn't support comments out-of-the-box, and I'm seeing that as a feature. I can't remember a single time that comments on a blog have been very helpful, but can point to thousands of examples of comments detracting from a piece.

If you want to comment on a post, my twitter and email are well-published here; feel free to send me feedback. Or, feel free to write something on your own site; if it's well done I'm happy to update my post and link to it. Manual trackbacks, I suppose.

## Jekyll

The meat of the update is that I've ditched Wordpress for [Jekyll](http://github.com/mojombo/jekyll), a blog-aware, static site generator written in Ruby.  Instead of having a database of blog posts and pages, I now have HTML/Markdown and files that Jekyll turns into a static web site.  The benefits of this system are enormous:

### Everything's a File

Since everything's based on flat files, I can store *everything* -- posts, layout, content pages -- in [Git](http://git-scm.org), edit everything in whatever editor I'm feeling like using (TextMate, Emacs, Vim, what have you), and that's all there is to it. Gone are the days of periodically running mysqldump on a wordpress database and stashing tarballs somewhere.

### No More Wordpress Themes

I never got my mind around writing Wordpress themes, which means if I ever wanted my site to have a different visual theme I had to crack open Wordpress docs and try to modify whatever theme I was using.  Nuts to that.

Now I write CSS and HTML with a little [Liquid](http://www.liquidmarkup.org/) templating thrown in and I'm good to go. If I wanted, I could switch to [Haml/Sass](http://haml-lang.com/) and use that instead, which is amazing.

### No Backend

Git and rsync are all the backend I have to speak of. There's no database to get overloaded, and no admin panel that needs [esoteric](http://www.seoegghead.com/software/wordpress-firewall.seo) [plugins](http://www.bad-neighborhood.com/login-lockdown.html) to secure it because the default install can't figure security out.  My webserver serves non-interpreted HTML files, so the only limit is the capacity of my server and pipe.

If this all sounds awesome to you, I'm putting together a quick tutorial on how to migrate a Wordpress site to Jekyll which should be up in a few days.