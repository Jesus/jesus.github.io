---
layout: post
title:  The symbol to proc operator, or not?
date:   2017-11-09 20:29:29 +0100
categories: ruby programming
---
Some time ago I used to think that the `&` unary operator in Ruby could be
used to create a `Proc` object. It turns out I was slightly wrong.

I think I'm not alone here as apparently the `&:` operator even gets the name
of [symbol to proc
operator](https://github.com/JuanitoFatas/what-do-you-call-this-in-ruby). The
name is misleading in several ways:

1. It's not really an operator, `&:` is a combination of the `&` operator
   followed by a symbol definition. `&:upcase` is exactly the same as
   `&(:upcase)`.
2. You can't use it to create a `Proc` object, this is explained this with
   detail below.

## Calling a method with a block (proc to block)

To be precise, the operator does create a `Proc` object, but it's just
because Ruby needs to do this internally as part of the process to build the
block which will be passed to the method being invoked. As this proc
is internal, we can't access it from our Ruby code.

What the operator really does is to pass its only operand as a block to the
method we're invoking. It converts something to a block.

Take the following snippet for example:

```ruby
%w(a b c).map(&:upcase)
# => ["A", "B", "C"]
```

When this is executed, Ruby will pass whatever follows the `&` operator as
the block for the method. But in this case we have a `Symbol` object.

It's pretty obvious how to convert a `Proc` into a block. Both things are
almost the same.

It's not so obvious how to convert a `Symbol` to a block. Here's where
`:to_proc` comes in. When the object given to the operator isn't a
*proc*, `:to_proc` will be invoked on the parameter to produce a *proc* that
will later be converted to a block.

So, the snippet can be rewritten as the following equivalent:

```ruby
%w(a b c).map(&(:upcase.to_proc))
# => ["A", "B", "C"]
```

Notice that we haven't used the operator to build the *proc*, but to build the
block.

## Receiving a block as a proc (block to proc)

Just for completeness, I'll mention that the same operator can be used in a
different context to convert a block to a proc.

In the following example, the operator is used in the params of a method
definition:

```ruby
def repeat_twice(&what)
  what.call
  what.call
end

repeat_twice do
  puts rand(10)
end
```

The code will show a couple of random numbers on screen, but what's most
important here is that the block defined with a `do ... end` becomes a proc
object named `what` inside the method.

When the `repeat_twice` is invoked, we're sending a block which will be
converted to a proc and added to the table of local variables when the method
is executed.

In this case we did create a proc, but we didn't use any `Symbol` at all.

## So what?

In summary, you can't use the *symbol to proc* operator to create a proc from
a symbol.

This looks like a subtle thing, but hopefully I've helped
someone to avoid the confusion I had.

I guess *block operator* is a better name, because what it really does is to
convert procs to blocks and vice-versa.

<!-- What do you think? -->

<!--
Original idea:

Shorthand for to_proc, or not?

Ruby's `&` may not call `to_proc` nor create a `Proc` object.

1. Explain how to use the & operator to pass a block to a function.
2. Show that to_proc isn't being called when you pass in a Proc.
3. Show that no proc is being passed in to the method, a Block is being
   sent. Show that the Block can be converted to a proc.
4. Show that you can create a proc by calling to_proc on a Symbol, but
   it's a particular kind of proc, it can't be disassembled!

-->
