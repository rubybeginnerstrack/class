---
layout: post
title:  March - The Github Ruby style guide && extracting methods && method basics.
date:   2014-03-10
---
by [Jeffrey Matthias][jeffrey matthias]

First off, here is the [Github Ruby style guide][github style]. There is no single way to write Ruby code, but choosing a consistent style makes your code more readable. As time has gone on, the community has moved towards some consistency, but there is still plenty of room for opinion. Following this style guide as you learn will help you to write clear, readable code that will make others happier to give you code review and work with you.


Now, to the methods. First, I want to thanks JonathanTR for puting the code example together for this lesson. This wouldn't have happened without him.


Methods are reusable chunks of code. We give them a name and "call" the method to execute the code. (Technically, in Ruby, we **send** the message, but that is a distinction we will get into at another juncture.) Every method starts out being defined like this:

```ruby
def my_new_method
  # executable code goes here
end
```

The name of the method is the bit that goes after **def**. Then, to execute the code, we simply "call" the name of the method.

```ruby
my_new_method
```

Note that the syntax for the method name is snake case. That leads us to rule number **1: Name your methods with snake_case.** (Don't worry, I will relist these rules together at the end of the post.)

If we need to pass the information to the method, we do so by passing **arguments** to it.

```ruby
def greater_of_two(number1, number2)
  # sort from smallest to greatest and return the last one.
  return [number1, number2].sort.last 
end
```
Note that the method was named for what it returns AND that the variables storing the arguments are named for what they are. This leads us to rule **2: Name your method for what it returns, if that is the point of the method** and **3: Name your variables to store your arguments for what they contain.**

Wait... I just put a conditional on rule 2, didn't I? "if that is the point of the method" I said. Doesn't seem very commital. Well, it actually is. There are two things can can happen when you "call" a method. The method can do one, the other or both. The first is that it can return something. Our method above ```greater_of_two``` returns the larger number. 

A quick trip to irb can demonstrate this.

```ruby
irb(main):012:0> def greater_of_two(number1, number2)
irb(main):013:1>   # sort from smallest to greatest and return the last one.
irb(main):014:1*   return [number1, number2].sort.last
irb(main):015:1> end
=> nil
irb(main):016:0> greater_of_two(56,32)
=> 56
```

The ```=>``` in irb always indicates what is returned by a method. In this case, it's the largest number in the array. Note that ending the method returned a ```nil```. Until the method is actually executed, it doesn't return anything.

The second thing that can happen is called a side effect. It isn't what is returned, but something else that happens as a result of calling a method.

And example of a side effect comes from the method ```puts```. If we check it out in irb, we can see what I'm talking about.

```ruby
irb(main):006:0> puts "I'm a side effect."
I'm a side effect.
=> nil
```

We see a ```=>``` followed by a ```nil```, which is what was returned by the method puts, on the line before that, we see the *side effect*. The line "I'm a side effect." was output to the StandardIO, making is show up on the screen. As a result, let's modify rule number **2: Name your method for what it returns or for its side effect.**

"Oh," you say, "sure pal, but what if the method returns something AND has a side effect?" Well, that actually brings up rule **4: Limit your methods to doing only one thing.**

I'm sure that seems terribly ineffecient, but it's a **very** good practice. Ever play with legos? How about lego sets with specialized pieces? They're cool and all, but wow, they are usually good for only what they were designed for and not really usable for anything else. I'm talking about the horse piece. Good luck reusing that thing for part of a castle. (I am aware that the very first lego horses were built out of lego bricks, but that actually helps serve the point.) So, why write methods that are so custom with the things that they do that they can't be reused?

Code is cheap. We work in Ruby because it makes achieving good code organization and clarity very easy compared to other languages. Let's take advantage of those things.

For our example, let's start with some code that doesn't follow rule 4. It's a method that accepts an array of scores between 1 and 100 and returns the letter grade that maps to the average of those numbers.

```ruby
def average_letter_grade(grades_array)
  sum = 0
  # find the average
  grades_array.each do |grade|
    sum += grade
  end
  average = sum / grades_array.length

  #translate to a letter
  case average
  when 90..100
    return "A"
  when 80..89
    return "B"
  when 70..79
    return "C"
  when 60..69
    return "D"
  when 0..59
    return "F"
  else
    return "There is a wildly invalid number in there somewhere."
  end 
end
```

It would be called like this:

```ruby
irb(main):083:0> puts average_letter_grade([92, 95, 97, 84])
A
=> nil
```

It seems pretty basic, works, and even has comments explaining what's going on, so what am I complaining about? Well, this code can only ever do one thing. It is locked into taking an array of numbers and has to return a letter. I see components in there that could be reusable. So, let's start by identifying those components. First, it averages the grades. Then it translates the average grade to a letter.

Let's pull the averaging bit into it's own method.

```ruby
def average_grade(grades_array)
  sum = 0
  grades_array.each do |grade|
    sum += grade
  end
  sum / grades_array.length
end

def average_letter_grade(grades_array)
  average = average_grade(grades_array)

  case average
  when 90..100
    return "A"
  when 80..89
    return "B"
  when 70..79
    return "C"
  when 60..69
    return "D"
  when 0..59
    return "F"
  else
    return "There is a wildly invalid number in there somewhere."
  end 
end

# to call the method
puts average_letter_grade([92, 95, 97, 84])
```

Look at that. Now we have a method that can average numbers for us. Is our work done? Well, the second method is a little strange now. It still has to take an array of numbers and returns a letter. So, the code that translates a number to a letter is still locked up inside of a very purpose built piece of code. Let's extract that bit into a method as well.

```ruby 
def average_grade(grades_array)
  sum = 0
  grades_array.each do |grade|
    sum += grade
  end
  sum / grades_array.length
end

def letter_grade(numeric_grade)
  case numeric_grade
  when 90..100
    "A"
  when 80..89
    "B"
  when 70..79
    "C"
  when 60..69
    "D"
  when 0..59
    "F"
  else
    "There is a wildly invalid number in there somewhere."
  end
end
 
def average_letter_grade(grades_array)
  average = average_grade(grades_array)
  letter_grade(average)
end

# to call the method
puts average_letter_grade([92, 95, 97, 84])
```

There. I feel better now. I have two methods that can be used in many more ways. The first averages numbers, the second translates a number to a letter grade. Now, that second method could be used by itself to report the letter grade from a single score. It doesn't know or care about averages. Limiting your methods to a **single responsibility** will leave your code more flexible and less repetitive.

Our new wrapper method, ```average_letter_grade(grades_array)```, still has the original functionality but it doesn't trap any blocks of code in a way that they can't be reused.

You may have noticed one other change between the last two iterations. I removed the ```return``` statements from the code. This leads us to the last lesson for this section. Code blocks (methods, blocks, procs) in Ruby always return whatever is on the last line that is executed. With our case statement, if I give ```numeric_grade``` a value of 75, the condition ```when 70..79``` will evaluate to true and "C" will be the last code executed. As a result, it will be returned from the case statement. And since the case statement is the last thing in the method to be executed, it "C" will be returned from the method without me having to explicitly say ```return```.

If you're coming from another language, this might seem a little different. Typically rubyists only use ```return``` when they need to prevent any other code after that line from being executed. If the return object is what they want it to be, they leave the ```return``` out.

So, let's review our rules about methods.

1. Name your methods with snake_case.
2. Name your method for what it returns or for its side effect.
3. Name your variables to store your arguments for what they contain.
4. Limit your methods to doing only one thing.
5. Make sure your last executed code is what your method should return.

Cool. Now I just need to figure out how to cover all of the above in our 20 minute session.


[jeffrey matthias]: http://twitter.com/idlehands
[github style]: https://github.com/styleguide/ruby