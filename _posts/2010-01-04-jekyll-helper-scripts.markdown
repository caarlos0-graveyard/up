---
layout: post
title: Jekyll Helper Scripts
---
 
I have a couple of helper scripts that I couldn't use Jekyll without:

## deploy

Shamelessly stolen from [The Pug Automatic](http://henrik.nyh.se/), it's pretty quick (and all on one line, despite what word-wrapping occurs here):

{% highlight console linenos %}
jekyll --no-auto && rsync -avz --delete _site/ blog:/pub/jcanady/joncanady.com/
{% endhighlight %}

Run `jekyll` -- I keep auto-regeneration turned on in my script, so disable that -- then rsync the site directory up to my blog's host. `blog` is an ssh alias. I like to have my blog's repository hosted on Github, and for various reasons Github can't connect to my blog's server, so this is nearly as good.

## newpost

This one is a lifesaver. I can't remember where I found the original ruby script, but after sprinkling a little `optparse` over it, it became an indispensable utility; just drop it somewhere on your `$PATH` (`~/bin/` is nice).

{% highlight ruby linenos %}

#!/usr/bin/env ruby

###
# newpost -- creates a new Jekyll post
# Change the blog_dir variable below, and you should be set.
# For maximum win, put in ~/bin/ (and add that to your $PATH)
#
# Jon Canady, 2010. 
# Covered by the WTFPL: http://sam.zoy.org/wtfpl/
###

# omit the trailing slash, please
blog_dir = "/Users/jonc/Sites/joncanady-blog" 


require 'optparse'

options = {}

optparse = OptionParser.new do |opts|

  # banner
  opts.banner = "Usage: newpost [options] \"title of post\""

  options[:draft] = false
  opts.on('-d', '--draft', 'Create post as a draft') do 
    options[:draft] = true
  end

  options[:format] = :markdown
  opts.on('-f', '--format FORMAT', 'Post is in [markdown|textile] (default markdown)') do |format|
    valid_formats = %w(markdown textile)
    puts opts && exit unless valid_formats.include?(format)
    options[:format] = format
  end

  opts.on( '-h', '--help', 'Display this screen' ) do
    puts opts
    exit
  end

end



begin
optparse.parse!
rescue OptionParser::InvalidOption => e
  puts e
  puts o
  exit 1
end
 
if ARGV[0].nil?
  optparse.display
  exit 1
end

puts "Generating post..."

date_prefix = Time.now.strftime("%Y-%m-%d")
postname = ARGV[0].strip.downcase.gsub(/ /, '-')

post_dir = options[:draft] ? '_drafts' : '_posts'

post = "#{blog_dir}/#{post_dir}/#{date_prefix}-#{postname}.#{options[:format]}"
 
header = <<-END
---
layout: post
title: #{ARGV[0]}
---
 
END
 
File.open(post, 'w') do |f|
  f << header
end
 
system("mate", "-a", post)


{% endhighlight %}

I bundle both of these with my blog's source; the latest versions can always be found in my [`tasks/`](http://github.com/joncanady/joncanady.com/tree/master/tasks) directory.