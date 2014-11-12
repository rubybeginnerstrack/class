---
layout: post
title:  Getting started with test driving your code
date:   2014-07-07
---

by [Jeffrey Matthias][jeffrey matthias]

Ah... to tackle one of the most complicated subjects in a beginners track. Let's start with a brief discussion on using other people's work vs. rolling your own. As coders, we like to think that if we had unlimited time, we could solve all coding problems and solve them well. But, the fact is that we don't have unlimited time and when challenged with something new, we must decide whether we are going to write our own implementation or to use someone else's open source work. There are many factors that must be considered, but the most important is how closely the existing work matches your needs and how much work it will take to modify it.

This is is why I do not plan to teach you how to test drive your code. While at JumpstartLab, [Katrina Owen][kytrinyx] wrote some [awesome tools][jumpstart_tut] to get you started. Instead, I am going to go over some concepts of test driving code that if I had understood better when I started learning, would have made it **much** easier for me to learn. After this post, I highly recommend you tackle the JumpstartLab intro.

Don't expect to fully get everything I talk about. But do your best to remember the basic area of knowledge as you go through and refer back as needed. At the end of the day, it will all click only after practice.

-----------------------------------------
**Concept 1 - side effects and return objects**

In March, I wrote about naming your methods after what they return or their side effect, depending on the purpose of the method. (As a reminder, it is always good to keep your method to doing just one thing if you can.) Return objects (in Ruby, just about everything is an object, so we will use "return objects" to refer to whatever is returned from a method) and side effects are the key focus of testing your code, so we will go into a refresher from the March lesson.

A return object is whatever is returned from a method call. If you recall, IRB gives us a little help with this by indicating what is returned from a method with ```=>```. Like so:

```ruby
2.1.0 :002 > def greeting
2.1.0 :003?>   "hello"
2.1.0 :004?>   end
 => :greeting
2.1.0 :005 > greeting
 => "hello"
```

Since I'm using Ruby 2.1, when I defined my method, Ruby returned ```:greeting``` the method name. Then, when I called the method (line 5), ```"hello"``` was returned. Let's look at an example where something a little more complex is returned.

```ruby
2.1.0 :024 > require 'ostruct'
 => true
2.1.0 :025 >
2.1.0 :026 >   def weather_report
2.1.0 :027?>     report = OpenStruct.new
2.1.0 :028?>     report.date = "07/07/14"
2.1.0 :029?>     report.temp = 32
2.1.0 :030?>     report.temp_scale = "celsius"
2.1.0 :031?>     report
2.1.0 :032?>   end
 => :weather_report
2.1.0 :033 > weather_report
 => #<OpenStruct date="07/07/14", temp=32, temp_scale="celsius">
```

So, there we have a more complex return object. Whether it is a simple string, boolean, or another instance of an object, when we call certain methods, we expect an object to come back.

By contrast, let's look at the method ```puts```.

```ruby
2.1.0 :034 > puts "hello"
hello
 => nil
 ```
We see that ```hello``` is output to the screen, but the what is returned from ```puts``` is ```nil```. Given that it always returns ```nil```, we can assume that the method name, *puts*, refers to the side effect of the method and not the return object.

When we test our code, we focus on the return objects from our code and on the side effects. That's it. We don't write tests for anything else.

-----------------------------------------
**Concept 2 - interface, not implementation**

Understanding this is the key to not hating test driving your code. In fact, when it 'clicks,' you'll even start to really like it.

As referenced above, we focus on the return objects and the side effects, in other words, the messages coming OUT of your code. The other part of this is paying attention to the messages going IN to your code. When we look at the defined set of messages going in and out of a class of code, we are looking at the 'interface' of the class. It's the definition of how other classes interact with your class.

Let's look at a sample class and talk about what is interface and what is not.

```ruby
class Engine
  attr_reader :redline_rpm

  def initialize(number_of_cylinders, displacement, redline_rpm, transmission)
    @number_of_cylinders = number_of_cylinders
    @redline_rpm = redline_rpm
    @throttle_position = 0
    @transmission = transmission
  end

  def set_throttle_position(amount_open)
    @throttle_position = amount_open
    send_rpm_to_transmission
  end

  def rpm
    @redline_rpm * @throttle_position / 100
  end

  private

  def send_rpm_to_transmission
    @transmission.set_input_rpm(rpm)
  end
  
end
```

It's a very basic model of a car engine expressed in code. The first thing to notice is that even though we initialize the engine with 4 pieces of information, only one of them has a reader method. That means that only ```:redline_rpm``` is part of the publicly available interface. ```:initialize``` will require those 4 items to instantiate an engine, but the number of cylinders and the throttle position aren't publicly available.

So what is publicly available? Well, ```.new``` is a class method for making a new instance, but that is inhereted from Object and so we don't typically test it since Object, well, just works. We can check that ```:redline_rpm``` returns what we instantiated it with, but that really is testing the return value of that method.

The public methods on an instance of the Engine class are ```:redline_rpm, :set_throttle_position, :rpm```. These are the methods that need to have tests.

```:rpm``` Is pretty straight forward. You create an engine. By default, the rpm should be zero. Then set the throttle and check the ```:rpm``` to make sure it is what you expect it to be. The only catch is that it requires the ```:set_throttle_position``` method to work.

So let's look at the ```:set_throttle_position``` method. One side effect is that it sets an instance variable, which is testable through ```:rpm```, but it ALSO calls a private method. So do we need to test that the private method gets called? ***NO!***

Ultimately, we need to know that a message gets sent out to the transmission that we instantiated the engine with. That is one of the side effects of the ```:set_throttle_position``` method. We don't care how it happens so lond as it does. So whether we refactored the method to look like: 

```ruby
  def set_throttle_position(amount_open)
    @throttle_position = amount_open
    @transmission.set_input_rpm(rpm)
  end
```

or leave it alone, the side effects of calling the ```:set_throttle_position``` method are 1) set the throttle position and 2) pass the rpm information along to the transmission. We use the word private above the ```:send_rpm_to_transmission``` method to keep it out of the publicly available methods. This means that the method doesn't need to be tested. We only test our public interface, not our implementation. So, we call a public method and check that the side effects happen, but not HOW they happen.

This concept is often referred to as a black box. You can see what's going on on the outside, but have no idea what's happening on the inside.

This allows our tests to make sure that our class behaves the way it needs to without locking us into any specific implementation.

----------------------------------------------

**Concept 3 - stage your data, kick off your code, and then assert!**

Test can be broken down into three parts. I'm going to use a test from one of the JumpstartLab exercises to illustrate.

```ruby
def test_when_cranky_it_will_not_shoot_a_bow
  centaur = Centaur.new("George","Palomino")
  3.times { centaur.shoot }
  assert_equal "NO!", centaur.shoot
end
```

The first part is getting everything set up so that the conditions around your code will create the right situation in which your code needs to perform a specific way. In our example, this is pretty basic. ```centaur = Centaur.new("George","Palomino")``` We have ```centaur``` and it is ready for our test.

The next phase is the kick off. In this case, it's ```3.times { centaur.shoot }```. This is the code that needs to be run in order to make sure our class behaves the way we expect.

And then there are the assertions. Well, in this case, the assertion. ```assert_equal "NO!", centaur.shoot``` An assertion is a check to see if the side effect or return object from your class is what you were expecting.

It is pretty straight forward and I would advocate that just about every good ruby test under the sun can be broken into those three parts... but. You knew there'd be a catch, right? When you are looking for side effects instead of return objects, most testing frameworks will have you place your assertions *before* your kick off. That's it, though. The entire catch. Not too bad.

----------------------------------------------

**Conclusion**

I almost got away with writing a whole post about testing without showing any tests. That was on purpose. Different test frameworks look different and these concepts are true no matter which one you use. So, go practice. Really, go run through the [exercises][jumpstart_tut]. When you're done with that basic one, keep going with [these][jumpstart_github]. Keep a vague understanding of all this in mind when you do. Try to use your experience with those exercises to give everything above some context. Write me if you have questions. jeffrey@matthias.org.

[jeffrey matthias]: http://twitter.com/idlehands
[kytrinyx]: https://twitter.com/kytrinyx
[jumpstart_tut]: http://tutorials.jumpstartlab.com/topics/testing/intro-to-tdd.html
[jumpstart_github]: https://github.com/JumpstartLab/ruby-exercises/tree/master/mythical-creatures
