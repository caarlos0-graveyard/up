--- 
wordpress_id: 87
layout: post
title: Multi-Developer Git Suggestions
wordpress_url: http://joncanady.com/2009/11/multi-developer-git-suggestions/
---
Git's pretty simple to use when it's just you and your Github repo, but as developers increases, complexity increases. 

## Each Developer Has Her Own Branch

Make each developer on the team maintain their own branch where their changes initially go before being merged into `master`. Bonus: you can vet new developer's changes before they get pushed into whatever the production branch is.

So, here's what I do:

    jonc$ git checkout -b jonc

Create and switch to the branch `jonc`.

    jonc$ git checkout master
    jonc$ git merge jonc

Merge all the changes from `jonc` into `master`
(This, of course, works in reverse, if you need to pull new stuff from `master` into your personal branch)

    jonc$ git push origin jonc:refs/heads/jonc
    
Now the `jonc` branch exists on Github.  Anyone else that wants to check out my work is free to do so:

    otherdev$ git checkout --track -b jonc origin/jonc

## Features Have Their Own Branches

If a feature is going to take longer than one sitting to completely implement -- including tests -- then it goes in its own branch.  Every time I've excepted this rule I've regretted it later.

    jonc$ git checkout -b bugfix_553
    
If you're going to be collaborating on something with another developer, create it remotely first, then both of you can push/pull from the get-go.

    jonc$ git push origin origin:refs/heads/bugfix_553
    
That creates the remote branch, and any developer that wants to start working on it does the following:
    
    jonc$ git fetch origin    
    jonc$ git checkout --track -b bugfix_553 origin/bugfix_553

When you're on the `bugfix_553` branch, pushes and pulls go to and from there instead of `master`.  When ready, you merge it into master as usual.  Once you're 100% done with the branch, delete it as normal (`git branch -d bugfix_553`) and remove it from the server if necessary:

    jonc$ git push origin :heads/bugfix_553
    
No, that command isn't intuitive in the slightest.

## Production Always Builds

The production branch should *never* have code in it that breaks.  Hopefully it's got a good test suite, and with any luck it doesn't incur much technical debt, but at the very least it needs to:

1. Run
2. Produce correct output

This means that deployments can happen whenever. Developing directly in the production branch or merging changes prematurely means that if critical bugfixes need to go out **now** you can handle that without having to clean up messes first.

Developers that consistently push bad code into the production branch should be beaten. Having some Continuous Integration process watching that branch isn't a bad idea either.

(I've always used `master` as the production branch, but use whatever makes you feel good.)
