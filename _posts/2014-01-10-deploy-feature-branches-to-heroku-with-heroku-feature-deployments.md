---
layout: post
title: "Deploy Feature Branches to Heroku with Heroku Feature Deployments Gem"
category: ruby
---

### The problem

I often find that one major bottleneck in the process of building and shipping
software is testing features. Perhaps 1 feature is pushed to the staging
server for testing, and the feature owner is working with the developer to
iron out any kinks. While this is happening no other developers can push to
staging. Perhaps the other developers don't realise this, and keep pushing and
wiping out each others work before it can be tested. The more things that are
being worked on at once, the worse the problem can get.

### The (temporary) solution

When we developers use git we create a feature branch where all of our changes
go, then create a pull request in GitHub, and then it is merged in after a code
review. Why not also use feature deployments? Heroku is perfect for quickly
deploying temporary sites as long as your [infrastructure is
immutable](http://chadfowler.com/blog/2013/06/23/immutable-deployments/).

My idea for a process is this:

- Developer takes the next story/task/feature from the project managment tool
- Developer does required work
- Developer creates a pull request in GitHub
- Developer creates a new Heroku app and deploys the site there
- Developer sets up the dns so that the site runs on a subdomain.
  (http://FEATURE_NAME.mattbeedle.name for example)
- Developer adds this link to the project managment tool and marks the
  story/task/feature as ready for testing
- Feature Owner tests the functionality on the subdomain and accepts the work
  (hopefully)
- Developer deletes the Heroku app, dns settings, etc
- Developer marks the pull request as ready for review in GitHub
- Team reviews the pull request and merges into master
- CI server runs tests and if all passes then deploys to the live site

The only issue with this really is that no-one wants to spend so much time
creating Heroku apps, configuring DNS, etc.


In order to automate this we've been using a [very simple gem](https://github.com/mattbeedle/heroku_feature_deployments) which I hacked together in a couple of hours and have been gradually adding stuff to for the past year. This gem will handle the following functionality:

#### Deploying

- creating a Heroku app for the current branch
- adding any required Heroku addons (memcached, redis, etc)
- adding collaborators to Heroku app (the rest of the development team in case
  they also need to work)
- configuring DNS to setup a subdomain
- creating a pull request in GitHub
- adding a link to the story in the PivotalTracker

### Tearing Down

- Deleting the Heroku app
- Removing DNS settings

This process has worked very well for us so far. The reason it's a temporary
solution is that the gem right now sucks. It's does not have automated tests and
it's tied to Heroku/DNSimple/PivotalTracker. I'm going to start working soon on
a hosted configurable deployment tool which integrates with many other services
and has an API so that deployments may be started by a CampFire/HipChat/IRC bot.
