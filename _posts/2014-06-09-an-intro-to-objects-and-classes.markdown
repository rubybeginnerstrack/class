---
layout: post
title:  An intro to Objects and Classes
date:   2014-06-09
---
by [Jeffrey Matthias][jeffrey matthias]

An object in Ruby is data bundled up with methods that can act on that data. Let's take something that we use regularly, a string, and put it under the microscope.

```ruby
2.1.0 :005 > "bob"
 => "bob"
2.1.0 :006 > "bob".class
 => String
2.1.0 :007 > "bob".length
 => 3
2.1.0 :012 > "bob".upcase
 => "BOB"
```
Ok... so, ```"bob"``` is a String. We proved that with the ```.class``` method. Because it is a specific string, and not just an generic string, we call it an instance of a String. We'll need to remember that in just a little bit. There are a bunch of other methods that we utilize on our instance, as well. As mentioned in our February lecture, ["Customizing your irb"][irb], I use a custom method to see what methods are available on an object.

```
2.1.0 :011 > "bob".local_methods
=> [:, :, :, :, :, :, :, :, :[], :[], :ascii_only?, :b, :between?, :bytes, :bytesize,
:byteslice, :capitalize, :capitalize!, :casecmp, :center, :chars, :chomp, :chomp!,
:chop, :chop!, :chr, :clear, :codepoints, :concat, :count, :crypt, :delete, :delete!,
:downcase, :downcase!, :dump, :each_byte, :each_char, :each_codepoint, :each_line,
:empty?, :encode, :encode!, :encoding, :end_with?, :force_encoding, :getbyte, :gsub,
:gsub!, :hex, :include?, :index, :insert, :intern, :length, :lines, :ljust, :lstrip,
:lstrip!, :match, :next, :next!, :oct, :ord, :partition, :prepend, :replace, :reverse,
:reverse!, :rindex, :rjust, :rpartition, :rstrip, :rstrip!, :scan, :scrub, :scrub!,
:setbyte, :size, :slice, :slice!, :split, :squeeze, :squeeze!, :start_with?, :strip,
:strip!, :sub, :sub!, :succ, :succ!, :sum, :swapcase, :swapcase!, :to_c, :to_f, :to_i,
:to_r, :to_str, :to_sym, :tr, :tr!, :tr_s, :tr_s!, :unpack, :upcase, :upcase!, :upto,
:valid_encoding?]
```

Well, ```"bob"``` has lots of methods. Now that we have done a little exploring, let's revisit our definition of a Ruby object. It's data bundled up with methods that can act on that data. In the case of our string, the data is the characters b, o, and b. That's not a lot of data, but we know that, by virtue of being of the class String, our string, ```"bob"``` has a boatload of methods that work on that data, so, our string actually has a good bit going on.

What is a class? Well, a class is sort of like a blue-print for an object. It gives a starting point for what kind of data an object can contain and what methods that object will have. Let's jump to some real world objects for a second.

I like cars, so let's use those. There is a mechanical design in existence that is the basis for the 2009 Subaru WRX. Every 2009 Subaru WRX starts off life with the same basic design, but then can get its own additional data that can make it unique. When they rolled off the assembly line, John's 2009 Subaru WRX and Kim's 2009 Subaru WRX were very similar. They both had an attribute, 0-60 acceleration, with a value of 4.8 seconds. They had identical weight and height and length.

They also had some differences. Kim paid more for upgraded rims and hers is blue. John's is black. When me make a specific object of a class, we call it an instance of that class. So John and Kim each have their own instance of a Subaru WRX. We could call that the class of the car, Subaru WRX.

It's important to think of instances of a class as being as diverse and separate as John and Kim's cars. Between you and me, John is a terrible driver and it took almost no time before he drove his hot little hatchback into a tree. It was repaired, but the important thing of note is that this had no effect on Kim's car or any other instance of Subaru WRX.

The cool thing about all this is that we can make up our own classes. We can decide what it should be called, what kinds of data it should hold, how it should hold it, and what methods should be able to act on that data.

Let's say we wanted to make a class in code to represent a WRX.

```ruby
class SubaruWRX
end
```
That was pretty easy. It looks like defining a method except that we use the keyword ```class``` instead of ```def```. Also, note that the name of the class Starts with a capitol letter and it not underscore (or "snake") case. You'll notice that some developers differ on what to do when you have a bunch of letters together in a name. WRX, or Wrx would both work. The important thing is that the name starts with a uppercase letter and that it contains both upper and lower case letters.

So what do we do with this new class? It doesn't really do much as it is, but we can make instances. We'll store them in variables to keep track of them.

```
2.1.0 :022 > johns_scooby = SubaruWRX.new
 => #<SubaruWRX:0x0000010483efa0>
2.1.0 :023 > kims_scooby = SubaruWRX.new
 => #<SubaruWRX:0x00000103861290>
2.1.0 :024 > johns_scooby == kims_scooby
 => false
 2.1.0 :025 > johns_scooby.local_methods
 => []
 ```
 We have two subarus and they are not equal to each other. But without any extra data and no methods specific to subarus(they DO have all the methods that a basic Ruby object has), they aren't a lot of fun.

 Let's try making them a litte more interesting.

```
class SubaruWRX
  def initialize(color)
    @color = color
  end
end
 ```

 What the what? What's this initialize thing? When we call ```.new``` on an object, the ```.initialize``` method is called as part of the process. Any parameters defined on initialize will need to be passed in when ```.new``` is called. Like so:

 ```
 2.1.0 :031 > kims_scooby = SubaruWRX.new("blue")
 => #<SubaruWRX:0x00000101080870 @color="blue">
 ```

 Ok... so what's with the ```@```? We've seen variables before and this is just a special variation called an instance variable. The ```@``` in front of the name means that the value of that variable, ```@color``` is available anywhere inside of the initialized object. We'll see how to access it in just a few.

 So, there we are with a color assigned, but what about it? Irb was nice enough to show us our object when we made it and we can see that the @color has a value. Now, our object has data. Sadly, we have no methods that do anything with that data. Right now, we would like to just be able to get the color from the instance. So we will add an "instance method" to our class definition.


```
class SubaruWRX
  def initialize(color)
    @color = color
  end

  def color
    @color
  end
end
```
Let's try this out:

```
2.1.0 :041 > kims_scooby = SubaruWRX.new("blue")
 => #<SubaruWRX:0x00000101079d18 @color="blue">
2.1.0 :042 > kims_scooby.color
 => "blue"
 ```

 Nice. What if we want to change that color?

 ```
 class SubaruWRX
  def initialize(color)
    @color = color
  end

  def color
    @color
  end

  def color=(new_color)
    @color = new_color
  end
end
```
And therefore:

```
2.1.0 :056 > johns_scooby = SubaruWRX.new("black")
 => #<SubaruWRX:0x00000104044d18 @color="black">
2.1.0 :057 > johns_scooby.color
 => "black"
2.1.0 :058 > johns_scooby.color = "orange"
 => "orange"
2.1.0 :059 > johns_scooby.color
 => "orange"
 ```

 What we just wrote are methods that are often called a "getter"—it gets the value of an instance variable—and a "setter"—it sets the value of that instance variable.

Ruby recognizes that these kinds of methods are written a LOT and so it gives us a shorthand to make them. Getters are called attr_reader (attr being short for attribute) and setters are called attr_writer.

 ```
 class SubaruWRX
  attr_reader :color
  attr_write :color

  def initialize(color)
    @color = color
  end
end
```

This class definition does the same thing as it did before. If you are making a reader and a writer for the same variable, there is an even shorter way of doing it, attr_accessor.

 ```
 class SubaruWRX
  attr_accessor :color

  def initialize(color)
    @color = color
  end
end
```

Awesome. That is super easy, but it is important to note that you shouldn't just willy-nilly make accessors for all of your instance variables. Many times, you don't want anyone, probably someone working in the same codebase later, to be able to change data. Here is an example of when that might be the case.

```
class Die
  attr_reader :sides

  def initialize(number_of_sides)
    @number_of_sides = number_of_sides
  end

  def roll
    rand(1..@number_of_sides) # returns random number bewteen 1 and the number of sides
  end
end
```

It seems perfectly acceptable that we should be able to know the number of sides a die has by asking it, but we should never be able to change that number. Notice the method ```.roll```. It utilized data from the instance variable ```@number_of_sides```. So the Die class holds data, the number of sides, and has a method that acts on that data. This is a basic object the we defined and instantiated.

Ok... there is a slightly more advanced topic that I would prefer to not get in to too much, but if I don't, you'll see it somewhere else and be confused. Up through all of this, everything we have done has involved instance methods... methods that can only be called on an instance of a class. It is possible to also define class methods. They can only be called on the class itself. Normally, this means that there will be no additional data wrapped up in that class (this isn't a strict rule, but I don't want to encourage the practice) and the classes are used for a basic utility. The way you would define a class with class methods looks like this:

```
class Thing
  def self.something
    puts "I am the side effect of a class method"
  end
end
```

```
2.1.0 :091 > Thing.something
I am the side effect of a class method
 => nil
 ```

 As a beginner, (really, as a developer) I urge you to not spend too much time getting into this. It seems like it could be more elegant, but a little googling will find you quite a few convincing arguments to avoid class methods.

 Cheers,

 Jeffrey

[jeffrey matthias]: http://twitter.com/idlehands
[irb]: http://rubybeginnerstrack.github.io/2014/03/09/customizing-your-irb.html