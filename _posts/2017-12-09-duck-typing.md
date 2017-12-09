---
layout: post
title:  Duck typing
date:   2017-12-09 09:57:00 +0100
categories: ruby programming
---

<!-- Normally I try to write about something that I find surprising or confusing, or
maybe I want do dig deeper in some topic and put my findings in a blog post.
Today, I'll make an exception because *duck typing* is a very popular concept
amongst Ruby programmers so it's not new to me. I think it's also very popular
in Python. -->

<!-- # So, what's *duck typing*? -->

> When I see a bird that walks like a duck and swims like a duck and quacks
> like a duck, I call that bird a duck.
>
> <cite>-- [James Whitcomb Riley][james-whitcomb-riley]</cite>

![Ruber duck](/assets/images/duck.jpg)

Let's start by defining what it is. I would say it's the ability of a language
to allow you to **care about how an object behaves rather than what an object
is**. In other words, the emphasis is on the methods that the object implements
rather than its type.

For example, to manipulate a number in statically typed languages you
always have to keep in mind that the object you're handling is a number so you
can do number-things with it.

Now, with duck typing you don't care about the type of your object but what
the object can do. You don't care if something is a number but if it can
behave like a number.

It's very similar to [interfaces][interface], with the difference that
you don't need to define them explicitly. It's one of my favourite parts of
this language, though you have to use it with care, the lack of explicitness
comes at a cost.

We, as software developers, should try hard to create good abstractions.
I'll describe an example to show how duck typing may help to implement these
abstractions.

## Example, IO read/write

Imagine you need to read from a resource to do something with its contents
(maybe compression, encryption, etc.). You could implement a method that takes
in a file descriptor and reads from it.

In our example we'll make it very simple and just print on screen whatever we
read from the file descriptor:

```ruby
def read_and_print(readable_thing)
  puts readable_thing.read
end

read_and_print(File.open("jibiri.txt"))
# => shows the contents of the file on screen
```

Later on, you realize that you also want to do the same for something you
receive from a network connection. In the wonderland of Ruby, you don't have
to modify your existing method:

```ruby
require 'socket'

connection = TCPSocket.open("www.google.com", 80)
connection.write("GET /\n")
read_and_print(connection)
# HTTP/1.0 302 Found
# Cache-Control: private
# [...]
```

The key point here is that `read_and_print` doesn't care about the type of its
parameter, all it wants is it to have the method `read`. In fact, we can even
create another object that implements `read` and pass it as the parameter:

```ruby
class SomeSortOfStream
  def read
    "You've been tricked!"
  end
end

read_and_print(SomeSortOfStream.new)
# => "You've been tricked!"
```

<!-- ## Adapter pattern

The ability to do this is often convenient when you want to implement some
sort of [adapter pattern][adapter-pattern].

Imagine you have an application that processes payments, so you wrote your
class that connects to the payment gateway, etc.

Later on, you decide to support another type of payment that requires a totally
different wrapper. -->

## Alternatives

Without duck typing, similar behavior can be implemented using inheritance or
interfaces. But...

  * Inheritance may not always be applicable, it's not a good idea if the type
    of the objects have a very different nature.
  * Interfaces need to be defined explicitly. Take for instance our previous
    example, if your original method took a `File` object when you implemented
    it you'd have to update its definition so it uses the *readable* interface
    and then update both `File` and `TCPSocket` to implement the interface.

## Beware! not a type system

Despite I like the name, I reckon it may lead to confusion as it looks like
it's another type system such as dynamic typing or static typing.

You shouldn't think it's a type system but a feature inherent to the ability
of the language to defer the binding of a method until runtime.

Perhaps *duck pattern* or *implicit duck casting* would have been a better
choice. What do you think? Had you heard about the term before? Please write
your comments!

[james-whitcomb-riley]: https://en.wikipedia.org/wiki/James_Whitcomb_Riley
[adapter-pattern]: https://en.wikipedia.org/wiki/Adapter_pattern
[interface]: https://en.wikipedia.org/wiki/Interface_(computing)
