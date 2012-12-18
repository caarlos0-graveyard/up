---
layout: post
title: GPS Failures and User Experience
---
 
My GPS taught me a valuable lesson about user experience the other day.

I normally drive on the highways whenever possible, but there was a massive snowstorm in Columbus one evening and I told my GPS to ignore highways due to numerous accidents.

I'd forgotten that I'd set that option the next time I needed the GPS. So I turn it on, enter my destination, and get on the highway before it's able to get good enough reception to calculate a route. Of course, since I'd told it to forbid highways, it wasn't able to find a route that it could accept, and it failed.

The problem is, it told me so with a paragraph-long error message about how it couldn't locate the origin. In the middle of driving down a freeway, it's hard to parse that kind of crap.

Web applications also run into similar issues: how do you handle the case where a user asks to do something, but an option she's set prevents her from doing it?

**Good**: Explain to the user the problem using non-domain-specific terms. In my example, a quick "We're unable to generate a route that matches your options." would work.

**Better**: Explain the problem, and provide a suggested solution. "We cannot generate a valid route. Please check Route Options (under Settings) and try again."

**Best**: Explain the issue and provide a one-click way to fix it. "We can't generate a valid route because you've turned *Forbid Highways* on. [Allow highway access] [Cancel this route]"