---
layout: post
title: "Presenters: Cleaning Up Rails Views"
date: 2012-01-11 20:52
comments: true
categories: software
---

One of the first Rails apps I wrote for [Innova][innova] was a complex beast
that primarily consists of one controller managing a singleton resource. Most of
the actions end up redirecting to `ListController#show` or rendering that
template anyway.

The `show` template pulls data from a *boat-ton* of places: several
`before_filter`s, helper methods, and actions spread across two controllers, and
at least three view helpers. This has quickly become a maintenance headache, but
what do you do?

<!--more-->

### Presenters

[Presenters][presenter-wiki] are part of the Model-View-Presenter pattern, and
are rarely seen in Rails applications for some reason or another. Essentially, a
presenter is **a class that contains the entire state for a given view.**

In my example above, I'm pulling the following data:

* What medicare plan you're interested in
* What drugs you've looked up
* If you're a logged-in user or not
* What member id you've looked up (assuming you're logged in)
* A handful of UI/feature flags from various places.

Rather than magically let the view pull those from instance variables and
helpers, let's create a place for all of them.

Imagine we create a file called `app/presenters/list_presenter.rb`:

```ruby
class ListPresenter
  attr_accessor :drugs, :plan, :user, :member
  attr_accessor :ui_flag_1, :ui_flag_2

  def initialize(opts = {})
    @drugs = opts.fetch(:drugs) { [] }
    @plan = opts.fetch(:plan)
    @user = opts.fetch(:user) { nil }
    @member = opts.fetch(:member) { nil }
    @ui_flag_1 = opts.fetch(:ui_flag_1) { false }
    @ui_flag_2 = opts.fetch(:ui_flag_2) { true }
  end

  def each(&block)
    @drugs.each(&block)
  end

  def plan_name
    "<span class=\"planname\">#{@plan.name}</span> <span class=\"groupno\">#{@plan.group}</span>"
  end

  def show_member_grid?
    @member.present?
  end
    
end
```

Things to note:

* This is just a class -- a PORO, if you like. No need for additional
  dependencies.  
* In addition to just wrangling up a bunch of variables into a single
  class, we're also adding convenience methods: in this case, you can call
  `each` on the presenter and it'll do the right thing.
* The presenter is a great place to add presentational methods.
* [`Hash#fetch`][hash-fetch], which is pretty great if you've
  never heard of it. (That method name is a hyperlink to the API docs!) 

You'd use it like so:

```ruby

class ListController < ApplicationController

  def show
    @view = ListPresenter.new(
      :drugs => @drugs,
      :plan => @plan,
      :user => current_user,
      :member => @member,
      :ui_flag_1 => params[:ui_flag_1]
    )
  end

end
```

I haven't implemented this in production yet, but it seems like a good idea. You
get the benefits of non-procedural view helpers, and you get to move a lot of
presentational logic out of the view.

Thoughts appreciated [@joncanady][twitter]

[innova]: http://innova-partners.com
[presenter-wiki]: http://en.wikipedia.org/wiki/Model_View_Presenter
[twitter]: http://twitter.com/joncanady
[hash-fetch]: http://www.ruby-doc.org/core-1.9.3/Hash.html#method-i-fetch
