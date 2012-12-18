--- 
wordpress_id: 88
layout: post
title: How To Fix a Shitty Project
wordpress_url: http://joncanady.com/2009/11/how-to-fix-a-shitty-project/
---
Way back in the Dark Ages of the Internet (2000), Joel Spolsky [wrote](http://www.joelonsoftware.com/articles/fog0000000069.html) of Netscape's impending version 6 release:

> It's a bit smarmy of me to criticize them for waiting so long between releases. They didn't do it on purpose, now, did they?
>
>Well, yes. They did. They did it by making the **single worst strategic mistake** that any software company can make:
>
>They decided to rewrite the code from scratch.

Most developers can instantly conjure up at least one project they'd love to rewrite from the ground up.  This time, you'll use the right language, the right libraries, the right style -- you'll [do it *right*](http://www.jwz.org/doc/cadt.html).

Don't do it.  It's a horrible idea.  Now you've reset your entire timeline back to zero, and you don't know *when* you'll be done.  (Say, where's [TextMate 2](http://macromates.com)?)  Not to mention the regression bugs you'll undoubtedly let slip in.

Instead of rewriting the whole thing from nothing, **incrementally fix things.**  Don't commit a *single* change that doesn't leave the code better than you found it.  Move business logic out of controllers and into models.  Changing a feature?  Cover the whole feature with tests.  

Eventually, you'll find you've rewritten all the shitty parts of the project anyway and it's not so bad anymore.
