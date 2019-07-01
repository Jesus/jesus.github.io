---
layout: post
title:  I just made Ruby++
date:   2019-07-01 16:36:00 -0400
categories: programming ruby
---

I wanted to write a loop in Ruby, to show all numbers from 0 to 10:

```ruby
i = 0

while i < 10
  puts i
  i++
end
```

The above seems reasonable for any non-Rubyist, but when you run it you get:
`syntax error, unexpected end`. Of course, because **Ruby lacks the [increment
operator][increment-operator]** (`i++`) which exists
in pretty much any other language.

Anyone would advice me to rewrite the loop like
this:

```ruby
10.times do |i|
  puts i
end
```

And that'd be the right thing I guess. But what fun is it?

A proper hacker wouldn't settle for the right thing, and I want to be a
proper hacker.
So let's modify Ruby's interpreter to support my crappy loop!

## How deep do we have to dig?

Let's explore how this could be achieved. A first naive attempt could look
like this:

```ruby
class Integer
  def ++
    self = self + 1
  end
end
```

But quickly we realize we can't define a method like that. Meta-programming
to the rescue:

```ruby
class Integer
  define_method '++' do
    self = self + 1
  end
end
```

However that doesn't work either, for several reasons.

First, we'll have a bit of
trouble invoking that method because of its name. But most importantly Ruby
won't allow us to modify self, this'll fail
with: `Can't change the value of self`.

How's that? Can't an object modify itself?

It sort of can, but really it can't. An object can modify any of its instance
variables, sometimes a string can modify itself.

But it turns out that numbers aren't even stored like other
objects in Ruby, but they're immediate values. That is variables storing
numbers don't hold a reference to a generic object but they hold the value
directly.
The same happens for other object types such as symbols, for example.

The only way we can change the value of a numeric variable is by reassigning
it.

## As deep as the parser

Eventually I found the solution by looking at the similar `+=` operator.

I tried to find out how that was being implemented in Matz's Ruby Interpreter
(a.k.a. MRI)
and it turns out that internally it's just converting `i += 1` to the same
AST as `i = i + 1`. That's great, we can do the same for `i++`!

Knowing that, we *just* have to update the parser so the following 3
statements are converted to the same AST:


```ruby
i = 1 + 1
i += 1
i++
```

<img src="/assets/images/increment-ast.png" class="inline-text medium-image" />

The [patch on MRI][increment-patch] boils down to
 1. Adding support so the new operators can be tokenized, `++` will be
    tokenized as `tINCR` and `--` will be tokenized as `tDECR`.
 2. Adding parser rules so an expression like `var_lhs tINCR` is reduced to the
    same as `var_lhs tOP_ASGN arg_rhs` when `tOP_ASGN arg_rhs` are `+ 1`.

I would never have come up with this patch if it wasn't because I had read
the book [Ruby Under a Microscope][ruam], which is an excellent resource
to start diving into MRI's source code.

In particular, the first chapter where it explains how the program is
tokenized and then parsed was very relevant for this task.

## The result

Now it's possible to run our previous code!

<img src="/assets/images/increment-operator.png" class="inline-text large-image" />

And because it's just the same as invoking `+= 1` you can do stuff like:

```ruby
d = Date.new(32)
d++
```

<img src="/assets/images/increment-dates.png" class="inline-text large-image" />

[ruam]: http://patshaughnessy.net/ruby-under-a-microscope
[mri]: https://www.ruby-lang.org/en/about/
[increment-operator]: https://en.wikipedia.org/wiki/Increment_and_decrement_operators
[increment-patch]: https://github.com/ruby/ruby/compare/trunk...Jesus:6ae534830d403d7423cea4565fa60fee043eac30
