---
layout: post
title:  Basic Data Types
date:   2014-10-15

---

###Data types - Ruby
*By Tom Hopkins* 

This post has companion slides which can be found [here](http://githop.github.io/slides_ruby_newby/).

Amongst the earliest things we learn in our academic careers are the alphabet and counting. Do you recall first learning the distinction between letters and numbers? If our kindergarten selves were a computer program, we’d accept 2 data types: numbers and letters. (possibly play-doh as a mutable structure, but perhaps another time) 

A data type is fundamentally a way to store information. Data types, in addition to a store of information, also have set parameters for what kind of information can be stored for a given type.

For instance: 

If my kindergarten teacher were to ask me to count to 10, perhaps they would take exception if I were to begin reciting my ABCs… Even if I were a particularly smart cookie and begun spelling out the numbers (starting at zero, of course), I’d still be providing the wrong information for my teacher. The teacher asked for numbers, yet I wanted to show off my grammar skills.. 

if you read the sentence: 

“Two plus two.”

You probably think to yourself, “Four, of course!”. That is because as humans, we are very loosely typed. We realize that “Two,”, “two”, and “four” are words that represent certain numbers. Even more, numbers have a way of being added together in a manner which yields a new number. In cases like above, we typically give the answer back as a word as well.

All of this is old news of course. But when learning new things, it’s easy to forget that everything is as foreign now as it was when you were learning arithmetic for the first time. 

As far as Ruby is concerned, it would need a lot more information to accomplish the above task. Ruby can handle the word “two” and number 2, however they are not letters or numbers, they are both in fact objects, one of ```class String``` and one of ```class Integer```*. Addition, as well as other math operations in Ruby, are indeed methods available to objects and can even be used with dot notation: 

```
2.+ 2 #=> 4 
```
*technically 2.class yields Fixnum ; for our purposes integers are fine

We know that “two” stands for 2 and “plus” stands for addition, however Ruby needs to have “two” converted from a string to an integer to have access to the correct ```“+”``` method. Fortunately, most data types can be converted into another with a conversion method. The one we would use in this case, would be ```:to_i```, so:

```
“two”.to_i #=> 0
```
Wait, that’s not right! This is because “two” and “2” are both strings in Ruby. What defines a string is wrapping text or numbers (or both, plus many more) in either single or double quotes.

```
“this is a string”
‘and so is this ;)’ 
```

Unfortunately, Ruby alone cannot convert the string “two” into any integer other than 0. Ruby would need: 

```
“2”.to_i #=> 2
```
Now we can use the ```“+”``` method and run 

```
“2”.to_i + “2”.to_i #=> 4
```

Notice the returned 4 sans quotes, a key tell that it is an integer. 

Suppose we define a simple method called ```sum()```, which takes 2 inputs and and uses the ```“+”``` operator to add them together and returns the result. 

```
sum(“two”, “two”) #=> “twotwo”
```

If we do “two” + “two” in a Ruby interpreter we would get “twotwo”. That’s because when you give a ruby two “words” (read: strings) to add, it’s going to concatenate them, rather than add them. The ```“+”``` method available to the string class invokes concatenation, not addition. As far as strings are concerned, the plus ```“+”```, or addition operator, when used with strings is akin to using a spreadsheet to merge two cells of text; such as first and last name. 

Perhaps we should redefine ```sum(a,b)``` as ```concatenate(a,b)```.

What if we try: 
```
concatenate(2,2) # => 4
```

Wonderful, it seems our concatenation method is now adding. 

Ruby is known as a dynamically typed language. This means that we can play somewhat loose with our data types. For better or worse, a Ruby programmer doesn’t need to bother declaring a data type for each variable. A benefit of this is that our ```sum()``` or ```concatenate()``` method works on many data types out of the box, we just have to be conscious of what we’re expecting it to do for us. 

Certain programming languages have different rules on how you can use data types. Perhaps the code will only execute if each variable type is declared ahead of time, or certain methods expect a particular input. Ruby is not this way, due to it being dynamically typed. Otherwise, when defining sum, it may have looked something like: 

```
def sum(int a, int b) 
```

This enforces that sum will only run if we input integers. In other words, it would not return “twotwo” if we fed it “two”, “two”. It would not run at all. 

Ruby isn’t as loosely typed as JavaScript, for contrast. If we called 

```
sum(“2”, 2) #=>  TypeError: String can't be coerced into Fixnum
```
we’d get the error above, but in JS: 

```
sum(“2”, 2) #=> “22” 
```

Many objects have duplicate method names, however they do not all function the same. If we try:

``` 
“one” + 4 #=> TypeError: no implicit conversion of Fixnum into String
```

Ruby will try to concatenate “one” and 4, however strings and integers cannot be concatenated in this manner, likewise: 

```
4 + “one” #=> TypeError: String can't be coerced into Fixnum
```
Would attempt to add an integer to a string, which also cannot happen in Ruby. 

Ruby programmers need to be aware of data types when programming. Since everything is an object, it’s possible different objects have different expectations when being combined. Two integers would give us addition, two strings would give us concatenation. Mixing types requires one party to be converted for a successful operation. 

##Palindromes

Palindromes are words or phrases which read the same forwards as reversed. Racecar is a palindrome, for instance. We can write a simple ruby program which detects if a word is a palindrome. The class String has several methods available which are useful for this end. One method, is particularly useful: ```:reverse```

```
“Racecar”.reverse #=> "racecaR" 
```

There is only one problem: 

```
“Racecar” == “racecaR” #=> false
```

The last problem is the casing doesn’t seem to match. To remedy this, we’ll use another String method: ```:downcase.``` 
```
“Racecar”.downcase.reverse == “Racecar”.downcase  #=> true
```

This way, our data is somewhat normalized; if we input strings, we’ll always be comparing them in lowercase. So far, this is what our program looks like: 

```
def palindrome(str)
  str.downcase.reverse == str.downcase ? true : false 
end

palindrome(“Radar”) #=> true 
```

This works great, but we can always improve. This only works on single word palindromes, how about phrases? Simply reversing the string in this case will no longer work, as phrases tend to have punctuation and palindromic phrases are allowed to have separate letter spacing as well. For example “A Toyota” is considered a palindrome, despite that

```
 “A Toyota”.downcase.reverse == “A Toyota”.downcase #=> false 
```

It seems that if we’re only considering letters, then all we’ll have to do is remove punctuation and spaces, then we can reverse what is left to check against our input. We’ll also have to make sure that when we do our comparison, the input is also devoid of spaces and punctuation.

The ```:scan``` method is just what we need in this case. We can pass in regular expression to match for a particular pattern in our text. In this case, we’ll match any word character. 

```
“A Toyota”.downcase.reverse.scan(/\w/) == “A Toyota”.downcase.scan(/\w/) #=> true
```

Before refactoring our palindrome method to include these new features, why not add them to the String class itself? After all, everything in Ruby is an object and we’ll be able to use our new method in a sugary way: 

```
class String
  def palindrome?
    self.downcase.reverse.scan(/\w/) == self.downcase.scan(/\w/) ? true : false 
  end
end
```

Now we can do something like this: 

	“racecar”.palindrome?                  #=> true
	“A Toyota”.palindrome?                 #=> true
	“No lemons, no melon.”.palindrome?     #=> true
	“He won a Toyota now, eh?”.palindrome? #=> true


Pretty neat, right? 

##Numbers

Numbers in Ruby can be of two sorts: integers and floats. Fortunately, the integer definition from in math will work here. Integers are any whole number positive or negative. Floats, or floating-point, are numbers with decimals. 

```
42.class   #=> Fixnum
42.0.class #=> Float
```

While floats and integers are different types, they can be used together in calculations. When floats and integers are used together, the result will always be a float. 

```
2 + 2.0 #=> 4.0
2.0 + 2 #=> 4.0 
```

In terms of number format, Ruby does not use commas to separate places. If you do, some odd things may occur:

```
ten_thousand = 10000  #=> 10000 
ten_thousand = 10,000 #=> [10, 0]
ten_thousand = 10_000 #=> 10000
```

In the second case, the comma cases Ruby to assign ten_thousand as an array with 2 values: 10 and 0. If you need to space out your letters, underscores will do fine. 

As Ruby programmers we have access to more than just addition. Out of the box, Ruby comes with the following math operations: 

Addition      | Subtraction    | Multiplication         | Division     | Modulus     | Exponents 
 :-----------:| :------------: | :--------------------: | :----------: | :---------: | :------------:
+             | -              |  *                     | /            |   %         | **
2 + 1         | 2 - 1          |  2 * 1                 | 2 / 1        | 2 % 1       | 2 ** 1

Modulus tends to be the least familiar to those new to programming. Modulus works as follows: 
divide the first term by the second term; return what is left over. 

```
15 % 4 #=> 3 
```

4 goes into 15 three times, with three left over. 4 * 3 = 12 + 3 = 15 
One common use case of modulus is for determining if a number is even or odd. Even numbers are divisible by 2 with no remainder, so:

```
n % 2 == 0 #=> true  n is even
n % 2 == 0 #=> false n is odd
n.even?    #=> false 
```

One thing to be mindful of, is that if your calculation uses integers, the result will be an integer. This is something to be aware of, especially when dividing. Integer division will always round the answer down: 

```
15 / 4   #=> 3
15 / 4.0 #=> 3.75
```

This could be the intended behavior, perhaps I have 15 minutes available to iron laundered shirts and it takes me 4 minutes to iron a shirt. How many shirts can I iron in this time? 3 shirts. It’s safe to assume ironing a shirt ¾ of the way is not considered job well done. 

Just like strings, integers can be converted as well: 

```
3.to_s #=> “3”
3.to_f #=> 3.0 
```


##FizzBuzz

Fizzbuzz is a riddle that goes as follows: For a given range of numbers, say 1 - 20, replace numbers divisible by 3 with the word “fizz”, replace numbers divisible by 5 with the word “buzz”. Lastly, replace numbers divisible by both 3 and 5 with “fizzbuzz”.

With palindromes, the reverse method was key to our solution. For Fizzbuzz, we’ll be using modulus to do most of the heavy lifting. We used modulus to check if a number was even by seeing if it was a multiple of two. 

If we review our requirements, we should be able to use

```
n % 3 == 0 #=> divides by 3
n % 5 == 0 #=> divides by 5 
```

to determine when we need to sub in “fizz” or “buzz”.

The next thing is to run this check on each number 1 through 20. We need a range of integers, and one easy way to accomplish this is with the ```.upto(n)``` integer method.

```
1.upto(3) do |n|
	puts n 
end
puts "Enough is enough, turning car around, road trip over"

#=> 1
    2
    3
    Enough is enough, turning car around, road trip over
``` 
This code issues the classic parental road trip warning. Rather than just printing the value of the current iteration, we need to be running our fizz and buzz checks inside the block.

We could solve this without adding methods to the ```class Integer```, however, having a method such as ```:divides_by(n)``` would go a long way to streamlining our code. Let’s implement the following:

```
class Integer
  def divides_by?(n)
    self % n == 0 ? true : false 
  end
end
```

Now we can do something like: 

```
9.divides_by(3) #=> true
```

Now we won’t have long expressions in our code. WIth our new ```:divides_by()``` Integer method, ```:upto(n)```, and a wicked conditional, we have everything we need to solve Fizzbuzz. 

```
1.upto(20) do |n|
  if n.divides_by(5) && n.divides_by(3)
    puts 'fizz buzz'
  elsif n.divides_by(5)
    puts 'buzz'
  elsif n.divides_by(3)
    puts 'fizz'
  else
    puts n
  end
end
```

A pretty straightforward solution. It returns: 

```
1
2
fizz
4
buzz
fizz
7
8
fizz
buzz
11
fizz
13
14
fizz buzz
16
17
fizz
19
buzz
#=> true
```




