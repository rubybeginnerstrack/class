---
layout: post
title:  February - Customizing your irb
date:   2014-03-09
---
by [Jeffrey Matthias][jeffrey matthias]

For the first lesson, I went over IRB a little, mostly on using it to explore ruby, and then showed how to customize your IRB to make it a little more user friendly.

The tweaks I made really came in two flavors. Colors and tab-completion came courtesy of the gem called "wirble." The second bit was modifying your .irbrc file to add customizations to your irb that are loaded up every time you enter irb.

Let's take a look at getting wirble up and running first. Assuming that you're dealing with your global gemset—the default way of handling gems—it is pretty easy to get it installed.

```bash
$ gem install wirble
```

This will make the gem available. We'll go over making it load automatically in irb, but first try it out.

```ruby
1.9.3p448 :001 > require 'wirble'
 => true
1.9.3p448 :002 > Wirble.init
 => nil
1.9.3p448 :003 > Wirble.colorize
 => nil
1.9.3p448 :004 > "test string"
 => "test string"
```

Your colors will look a little different, but you will now have colors and tab-completion. That means that hitting tab will complete class and methods names for you. Ok, great. But requiring and intializing wirble every time you use irb isn't really cool.

Let's take a look at editing your .irbrc file so that it is automatic from this point on.

First, you need to figure out where your .irbrc file lives. On a linux/unix machine it is typcally in your /home directory. On a mac, it will be in your user home, which you can get to by typing

```bash
$ cd
```
and hitting enter. Then type

```bash
$ ls -al
```

to make sure you see the file listed. 

```bash
-rw-r--r--    1 jeffreymatthias  staff    304 Feb 18 18:34 .irbrc
```

Once you've located it, you'll need to edit it. I use Sublime Text 2 and it set up so I can type

```bash
$ subl .irbrc
```

to launch it from the command line. If you don't have an alias set up for your editor, here's instructions on how to [launch sublime from the command line on a mac][sublime].

If your .irbrc file doesn't exist, this command will open one up anyway. Again, assuming you're dealing with the global gem set, this should do the trick.

After you're in the editor, here is the method that I wrote and showed in class.

```ruby
# make wirble available in the file
require 'wirble' 

def wirble
  begin
    # enable features
    Wirble.init
    Wirble.colorize
  rescue LoadError => err
    # deal with it if wirble isn't available for some reason
    $stderr.puts "Couldn't load Wirble: #{err}"
  end
end 

# call the method, causing wirble to be run
wirble
```


**NOTE:** If you were in the Denver class, you may recall that I had some "clever" code that allowed me to build a list of features to enable. I call it "clever" because it was totally unnecessary for the situation. As developers, we often are tempted to be clever. Pro Tip: DON'T BE CLEVER. Clever is the enemy of easy to understand code.

Save your irb file and try it out.

Ok... you now know how to customize an .irbrc file. Here is the full file that I showed in class.

```ruby
require 'wirble' # make wirble available in the file
require 'pp' # sets up output to be a little nicer

def wirble
  begin
    # enable features
    Wirble.init
    Wirble.colorize
  rescue LoadError => err
    # deal with it if wirble isn't available for some reason
    $stderr.puts "Couldn't load Wirble: #{err}"
  end
end 

class Object # list methods on anything without the ones inhereted from object
  def local_methods
    (methods - Object.instance_methods).sort
  end
end

alias q exit # use 'q' to quit
wirble # kick off the wirble method above
```

If you want to dig more into what you can do to tweak your irb, check out these:

[ThoughtBot: irb & script/console tips][tbot]

[Stack Overflow: what's your favourite irb trick][stack]

Here's the thing... if you really find yourself getting too crazy about what you can do in your .irbrc file, you should probably start looking into [Pry][pry]. It is a replacement for irb that is crazy powerful and adds all sorts of features. But, learning it can get in the way of learning Ruby, so only go down that route for now if you find yourself spending as much time tweaking irb as you do actually coding.


I'll start getting these lesson notes up WAY sooner now that [Mr. JonathanTR][jonriles] has set this lovely blog up.


[jeffrey matthias]: http://twitter.com/idlehands
[sublime]: https://www.sublimetext.com/docs/2/osx_command_line.html
[pry]: http://pryrepl.org/
[jonriles]: http://twitter.com/jonriles
[tbot]: http://robots.thoughtbot.com/irb-script-console-tips
[stack]: http://stackoverflow.com/questions/123494/whats-your-favourite-irb-trick