---
layout: post
title:  Private & protected methods
date:   2017-11-23 10:06:00 +0100
categories: ruby programming
---

I've been using private and protected methods in Ruby for years, I always knew
that these scope definitions weren't exactly as you may expect if you come from
other languages.

In other languages (C++, Java, etc.):

  * A **private** method can only be accessed by instances of the class where
    it's declared;
  * While **protected** methods can be accessed by instances of the class or
    any of its descendants.

In Ruby, as defined in the [Ruby Programming Book][ruby-programming]:

  * A **private** method can't be called with an explicit receiver.
  * And **protected** methods can be invoked only by objects of the defining
    class and its subclasses.

Let's create a playground to show a few examples:

```ruby
class Dog
  protected def protected_bark
    puts "Woof woof woof!"
  end

  private def private_bark
    puts "Woof!"
  end
end

class Samoyedo < Dog; end

pluto = Dog.new
goofy = Dog.new
laika = Samoyedo.new
```

## Protected methods

Protected methods can be accessed from other objects of the same class where
the method was defined:

```ruby
pluto.instance_eval do
  goofy.protected_bark
  # => Prints "Woof woof woof!"
end
```

And also any object of a descendant class:

```ruby
laika.instance_eval do
  goofy.protected_bark
  # => Prints "Woof woof woof!"
end
```

But you can't call it from anywhere else:

```ruby
goofy.protected_bark
# => protected method `protected_bark' called for #<Dog:...> (NoMethodError)
```

## Private methods

It's very striking to see that the definition of private only states that
the method can't be called with an explicit receiver. It doesn't say anything
about the context where the method is accessible.

Let's see how to call a private method without an explicit receiver:

```ruby
pluto.instance_eval do
  private_bark
  # => Prints "Woof!"
end
```

Now, with an explicit receiver:

```ruby
pluto.instance_eval do
  self.private_bark
  # => private method `private_bark' called for #<Dog:...> (NoMethodError)
end
```

Interestingly, forcing the absence of receiver implies that the
method can't be called anywhere else but the same scope.

Unlike other languages, we can access a private method of an ancestor class.
As we can call a private method just as long as we don't make explicit the
receiver, just call the method without a receiver:

```ruby
laika.instance_eval do
  private_bark
  # => Prints "Woof!"
end
```

## Difference with other OO languages

It's often said that *private* in Ruby is like *protected* in other languages,
and it's similar because:

  * It's accessible from methods in the same class.
  * It's accessible from methods in any descendant class.

But differs because it's not accessible to other objects of the same class:

```ruby
pluto.instance_eval do
  goofy.private_bark
  # => private method `private_bark' called for #<Dog:...> (NoMethodError)
end
```

[ruby-programming]: http://ruby-doc.com/docs/ProgrammingRuby/html/tut_classes.html#S4
