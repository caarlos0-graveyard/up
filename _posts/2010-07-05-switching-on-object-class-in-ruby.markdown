---
layout: post
title: Switching on object class in Ruby
---

So it turns out, if you're trying to do one of these:

{% highlight ruby linenos %}
case obj.class
  when ActiveRecord::Base: # do something
  else # do something else
end
{% endhighlight %}
 
it always falls through to the else, no matter what `obj` happens to
be!

Here's how that's done instead:

{% highlight ruby linenos %}
case obj # just use the object itself
  when ActiveRecord::Base: # do something
  else # do something else
end
{% endhighlight %}

My next exercise is figuring out why this is. 
