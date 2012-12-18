---
layout: post
title: "Getting Better At Testing"
date: 2012-02-22 15:11
comments: true
categories: software
---

Honesty time:

I think I might suck at testing my software.

I mean, I know enough to be dangerous. I favor RSpec/Capybara over Test::Unit or
Cucumber. I like autotest. I might not actually adhere to TDD, but I rarely let
a feature/bugfix branch into master without tests.

The problem is, I don't know if I'm writing the right tests. For instance, I had
a method that in certain circumstances was supposed to throw `ArgumentError`. So
call that method with those arguments and expect `ArgumentError`, right? Except
that test was passing for weeks before I realized that there was an error in the
test code itself that was causing `ArgumentError` to be raised before the
production code was ever being invoked. Just recently I had a test in a Rails
app that was setting a session variable and ensuring that the rendered view
looked right. Except there were several cases where the session variable wasn't
being set properly in the first place, so the production code wasn't working as
intended even though I'd supposedly tested the feature. Not to mention my
confusion around correctly stubbing/mocking.

I think I'm probably not alone in this. So what do we do? I've been watching
[Destroy All Software][das], and highly recommend it. I think it's helping. It's
probably also a good idea to continue to rigorously test. I've started to
develop a sense for when a test might not be "good enough" but I think it needs
more refinement.

In the meantime, I plan on <strike>spamming the hell out of</strike>
<em>utilizing</em> [local user groups][crb] by sending in code samples (gists,
screencasts, whatever) and asking for feedback. I figure that'll probably stir
up a few talks, maybe a few "let's test some stuff" nights during [hack nights][codejam],
and hopefully I can shore up the weak spots in my testing.



[das]: http://destroyallsoftware.com
[crb]: http://columbusrb.com
[codejam]: http://columbusrb.com/#code_jam
