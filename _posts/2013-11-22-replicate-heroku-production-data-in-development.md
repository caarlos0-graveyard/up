---
layout: post
title: "Replicate Heroku Production Data in Development"
category: ruby
---

### The Short Version

Quickly replicate a Heroku production applications database to your rails
development database with the following command:

{% highlight bash %}
./script/replicate
{% endhighlight %}

### The Long Version

Sometimes when I have a project in production a bug occurs which I am unable to
replicate on my development machine without a copy of the production data.
Grabbing production data is a task that should be automated. Here's a simple
bash script that I use in all of my heroku apps. Put the file in your rails
script folder:

{% highlight bash %}
#!/usr/bin/env bash

echo "Running: heroku pgbackups:capture --remote production"
heroku pgbackups:capture --remote production

echo "curl'ing the latest.dump"
curl -o latest.dump `heroku pgbackups:url --remote production`

echo "restoring the dump"
pg_restore --verbose --clean --no-acl --no-owner -h localhost -U postgres -d
project_name_development latest.dump

echo "removing latest.dump"
rm latest.dump
{% endhighlight %}

The git remote for my heroku production app is always named "production". If
yours is different make sure that you rename it in the script. Also change the
database name to the name of your development database. Finally, make the
script executable:

{% highlight bash %}
chmod+x script/replicate
{% endhighlight %}
