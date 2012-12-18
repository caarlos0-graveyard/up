---
layout: post
title: Internet Explorer (Silently) Rejecting Cookies
---

I spent an entire day last week trying to figure out why one of our
staging sites wasn't working properly in IE6, and the same chunk of
code at a different URL was working just fine. The problem ended up
being that IE was blocking cookies from the affected domain. There are
two reasons this might happen, and we were hit by both.

## Third-party cookies

If your site is setting cookies from `http://yourstuff.com`, but is being
`iframe`d (or similar) from `http://theirstuff.com`, Internet Explorer
will, by default, reject any cookies and display a privacy
notificaiton. This is because the domain of the cookie doesn't match
the domain the user is actually *visiting.*

It turns out that IE *will* accept third-party cookies that come with a proper [P3P
privacy header](http://evolt.org/node/20756) that fits with the user's
security settings. 

(An interesting side note: Safari comes with two settings for
third-party cookeis: Always or Never, regardless of P3P headers.)

## Invalid subdomains

What ended up being our major issue was an underscore in our staging
subdomain: `site_one.yourstuff.com` is, according to the [URI
RFC](http://www.ietf.org/rfc/rfc2396.txt), invalid. Internet Explorer
will do everything else with it, but the cookie handling module
apparently checks the domain field *very* strictly, and refuses to set
the cookie. Worse, it does so *without ever telling the user*, and the
only solution is to switch to a subdomain that doesn't contain invalid
characters.

