---
layout: post
title: Cookieless sessions in Rails
---
 
I'm doing some cross-domain JSONP work that requires a session, but
Safari doesn't like third-party cookies, even if you use the
ridiculous [P3P protocol](http://en.wikipedia.org/wiki/P3P) stuff that
IE supports.

Rails generally requires cookies for storing session ids, but you can
get around that with by changing the `cookie_only` session option to
false (it's true by default):

{% highlight ruby %}
# in config/initializers/session_store.rb
ActionController::Base.session = {
  :key         => '_session_name',
  :secret      => 'abunchofrandomcharacters',
  :cookie_only => false 
}

# without cookies, you'll need a different session store
ActionController::Base.session_store = :active_record_store
{% endhighlight %}

Now, if you have a URL parameter like `_session_name=mysessionidhere`,
Rails will pick up on that and initialize the session using that
id. Do be aware that this increases the liklihood of [session fixation
attacks](http://guides.rubyonrails.org/security.html), so determine if
that's an acceptable tradeoff for your application.


