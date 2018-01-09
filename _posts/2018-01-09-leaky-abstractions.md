---
layout: post
title:  Leaky abstractions
date:   2018-01-09 19:03:00 +0000
categories: programming
---

It's said that an abstraction is **leaking** when part of the complexity it's
supposed to hide sneaks into the surface, exposing it in some way.

<!-- When we create an abstraction, we create an imaginary universe where the
rules are as we define. Then we provide a way to work with these rules. -->

# Rational numbers as an example

![Setun Computer](/assets/images/setun.jpg)

Even though all computer hardware works with a binary numeric
system (with some [rare exceptions][setun]) we're able to use arithmetic
with decimal numbers very easily with any programming language.
Aren't abstractions awesome?

However, you may find surprises like the following, in Ruby:

```ruby
0.1 + 0.3
# => 0.30000000000000004
```

But let's not blame Ruby just yet, let's try with Python:

```python
0.1 + 0.3
# => 0.30000000000000004
```

Javascript (Node.js):

```javascript
0.1 + 0.3
// => 0.30000000000000004
```

Etc.

This is a problem related to the way we store floating point numbers, using
the [IEEE-754 double precision format][ieee-754-double].
This is very well explained in [Python's documentation][python-floating-point],
and it's a good example of how an abstraction can leak.

The computer creates the illusion that we can use rational numbers, but
there're cases where it shows an unexpected behavior that is only understood
when you know the underlying details.

# How bad is this?

It's bad because it means we still need to understand what's beneath the
abstraction in order to use it. But still the abstraction may be useful.

> All non-trivial abstractions, to some degree, are leaky.
>
> <cite>-- [Joel Spolsky][joel-spolsky]</cite>

In a wonderful world abstractions wouldn't leak at all. But we live
in the world were Internet Explorer exists, not wonderland.

They always exist because in some cases we can't avoid the leak, but then we
have to make an extra effort to communicate the leaking detail to whoever may
be surprised by it, which could be another programmer or maybe the end-user.
This idea is well explained in this [article by Fragner Brack][fragner-brack].

For example, imagine a user of a banking system making a money transfer. The
UI to make the transfer doesn't specify the time it takes for the receiver
to see the incoming amount in their account. However the user made this
operation several times in the past so we've set his expectations to have
this completed in 1 hour.

One day, the user makes a transfer but this time the servers are really busy
because the operation was requested while the daily batch processing was
being carried out, so it doesn't get fully processed until 3 hours later.

By explicitly showing something like *"Most transactions take about 1 hour but
it may take up to 24 hours"* in the UI we're probably saving a few calls to
the support center. And more importantly, we're preserving the trust of the
user.

# A glimpse of hope

So all abstractions are leaking, but I think just the fact of being conscious
of this concept is going help in the future when we create or use an API, web
application, the file system in your OS, the networking protocols, etc.

Also it's going to lead us to think about how leaky our abstractions are, which
is a good exercise. For example, if you find yourself explaining too many
details of your internals, the universe may be telling you that your
abstraction doesn't fit there.

I think the most valuable contribution you can do to your software project as a programmer is create good abstractions,
at every level of the application -- internally and at the front-end.
Interestingly, this may end up changing the way other people on the company
perceives the business logic.

So, creating the right abstractions is definitely super important.

<!--
To create an abstraction what you do is generalize a problem and define it
in a way which makes it easier to handle by whoever is going to use the
abstraction. By doing this, we create higher level concepts that describe
something new, imaginary entities that hide the complexity of the detail below.

In general, the higher level concepts are better to tackle a specific problem,
they're not necessarily simpler, but they make our life easier.
-->

<!--
In Spain, as in many other countries I guess, taxes are an example of a very
poor abstraction, leaking thousands of gallons.
-->

[setun]: https://en.wikipedia.org/wiki/Setun
[python-floating-point]: https://docs.python.org/2/tutorial/floatingpoint.html
[ieee-754-double]: https://en.wikipedia.org/wiki/Double-precision_floating-point_format#IEEE_754_double-precision_binary_floating-point_format:_binary64
[joel-spolsky]: https://www.joelonsoftware.com/2002/11/11/the-law-of-leaky-abstractions/
[fragner-brack]: https://medium.com/@fagnerbrack/repairing-the-leaky-abstraction-e726baab91b5
