---
layout: post
title: SVN Diff Files With Vimdiff!
category: posts
---

Talking with some developers who use a "no unix-based system" as a development environment and visual clients for SVN, I asked for them why they like to use this? (in this case visual clients for svn but for me both of them are bullshit).

Surprisingly the answer was expected...

"I use it because I can ~see easily~ the revisions and diffs, And I can't do it on text mode..."

Srsly?


So I'd like to show them the vimdiff and how to use it with svn.


## Using Vimdiff

To use vimdiff simply just run the command `$ vimdiff file1 file2` or `$ vim -d file1 file1`. Both files are opened side by side and the differences between the files are highlighted.

### Keyboard Shortcuts

- do : Get changes from other window into the current window.

- dp : Put the changes from current window into the other window.

- ]c : Jump to the next change.

- [c : Jump to the previous change.

- ctrl w + ctrl w : Switch to the other split window.

After merge de differences just save the files normally with vim.


### Using vimdiff with SVN

When `svn diff file` is executed svn can provide two files, a work copy and a "remote" copy, so to use svn with vimdiff I must create a script that receive the params created by svn and call vimdiff.

Following [this example](http://svnbook.red-bean.com/en/1.4/svn.advanced.externaldifftools.html#svn.advanced.externaldifftools.diff.ex-1) I create the follow script in `/home/lucas/.scripts/svnvimdiff.sh`

{% highlight sh %} #!/bin/sh

/usr/bin/vimdiff $6 $7
{% endhighlight %}


I changed svn configs to set the script as default diff program, just open the configs `~/.subversion/config` and look for `diff-cmd` then cheng it, something like this `diff-cmd = /home/lucas/.scripts/svnvimdiff.sh`

Now just run `svn diff file` and enjoy...
    
