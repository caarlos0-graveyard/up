---
layout: post
title: "Ember App Kit: EMFILE, too many open files"
category: javascript
---

Recently I ran into an issue with an Ember App Kit app that I'm working on. With livereload enabled the development server is reloaded every time a file is changed, including the bower dependencies installed in vendor. As the bower dependencies grow there is soon a point where the number of files being watched exceeds the allowed limit for the operating system.

Mac OS X limits the number of open files to 256 by default. There are two types of limit. The **soft limit** and the **hard limit**. The soft limit is the currently enforced limit. The hard limit is the maximum value which the soft limit may not exceed. The simple solution is to add the following to your .bashrc:

{% highlight bash %}
ulimit -n 2048
{% endhighlight %}

If this doesn't work you may need to increase the hard limit. To do this set the following in /etc/sysctl.conf:

{% highlight bash %}
kern.maxfilesperproc=166384
kern.maxfiles=8192
{% endhighlight %}