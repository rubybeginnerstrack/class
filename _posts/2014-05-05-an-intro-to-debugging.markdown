---
layout: post
title:  An intro to debugging
date:   2014-05-05
---
by [Jeffrey Matthias][jeffrey matthias]

***This is a work in progress. Sign up above to be notified when it is complete.***

Debugging is a skill that you will use for the rest of your coding days. It is the systematic hunt and removal of defects that make your code behave in a way other than intended.

Before we look at having to self-direct debugging, let's briefly look at reading Runtime errors. Ruby is pretty helpful when an error is raised. It provides a clear read out of what happened and what code was executing when things went wrong. This is called a stack trace. Here's an example:

```ruby
from_waiter_with_options.rb:28:in `>': comparison of String with :burrito failed (ArgumentError)
  from from_waiter_with_options.rb:28:in `take_order'
  from from_waiter_with_options.rb:11:in `order'
  from from_waiter_with_options.rb:69:in `block in <main>'
  from from_waiter_with_options.rb:63:in `times'
  from from_waiter_with_options.rb:63:in `<main>'
```

Let's break that down. The first line says the name of the file and the line where things broke, ```from_waiter_with_options.rb:28:``` and then what actually when wrong, including the type of the error, ```in `>': comparison of String with :burrito failed (ArgumentError)```. This immediately points me to line 28 in the file called from_waiter_with_options.rb. And I know that there's something wrong about one of the arguments provided to the '>' method.

Sometimes the error isn't so obvious, or it's in code that can be called from multiple places. When that happens, you can tell the context of when it is called by looking at the next line of code: ```from from_waiter_with_options.rb:28:in `take_order'```. Now I know that the line that is breaking is in the method 'take_order'. If I look at the next line, ```from from_waiter_with_options.rb:11:in `order'```, I can see from where that method was called.

Reading your stack trace can help you solve most of your issues. But what do you do when your code never breaks, but the behavior isn't what you expected?

DEBUG!

I'll go over some code that was written for this post. I will explain how it should work, and then I will show the output when I run it. It won't work quite right. I'll then cover two styles of debugging: with 'puts' statements and with a debugger gem.

Let's look at that code. It's from a lesson I did a few months back on the [law of demeter][law of demeter].

```ruby
require 'ostruct'

class Diner
  def initialize(waiter, menu)
    @waiter = waiter
    @menu = menu
    @meal = nil
  end

  def order
    @meal = @waiter.take_order(meal_choice)
  end

  private

  def meal_choice
    @menu.sample # random pick from the menu
  end
end

class Waiter
  def initialize(chef, oven)
    @chef = chef
    @oven = oven
  end

  def take_order(meal_choice)
    if meal_choice > :burrito
      meal = @oven.cook(meal_choice)
    else
      meal = @chef.receive_order(meal_choice)
    end
    return meal
  end
end

class Microwave
  def cook(name)
    return "unevenly heated #{name}"
  end
end

class Chef

  def receive_order(menu_choice)
    create_dish(menu_choice)
  end

  private

  def create_dish(dish_name)
    recipe = load_recipe(dish_name)
    prepared_meal = recipe.prepare
    return prepared_meal
  end

  def load_recipe(name)
    # a shortcut until this can be better defined
    OpenStruct.new(prepare: "hot #{name}")
  end
end
```

Ok. There's a bunch of code. To explore what's supposed to be going on, let's look at some 'driver code' that lives at the bottom of the file.

```ruby
30.times do
  menu = ["chicken noodle soup", "spaghetti", "burrito"]
  chef = Chef.new
  microwave = Microwave.new
  waiter = Waiter.new(chef, microwave)
  bob = Diner.new(waiter, menu)
  puts bob.order
end
```

We're running this 30 times to make sure we don't see any off behavior (some day, test driving our code will prevent having to take steps like this, but we aren't there yet).





[jeffrey matthias]: http://twitter.com/idlehands
[law of demeter]: http://en.wikipedia.org/wiki/Law_of_Demeter