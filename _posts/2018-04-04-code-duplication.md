---
layout: post
title:  Code duplication
date:   2018-04-04 18:16:00 +0000
categories: programming
---

There's a very common rule, specially amongst Ruby programmers, to avoid
repeating code. This is called [DRY, or Don't Repeat Yourself][dry].

![I will not repeat myself again](/assets/images/dry.jpg)

This is one of the first concepts you learn in programming. You also learn
about abstractions and how these are very helpful to avoid duplication.

But not so early in your learning path, you'll come accross an statement
similar to this:

> Duplication is cheaper than the wrong abstraction

Sandi Metz stands by that claim and has explained his point of view many times,
for example in her post on [the wrong abstraction][the-wrong-abstraction].

This has caused a
[lot][discussion-1]
[of][discussion-2]
[discussion][discussion-3]
and no clear conclusion.

I think part of the reason is because the discussion is too broad and we're
missing specific examples. I personally have failed to find one real example
where a wrong abstraction was solving duplication.

But I do find a good point behind her statement, which is that the
abstractions in your program are something more than simple code holders.
Abstractions have meaning, and the code they contain have the behavior of that
entity.

If two entities happen to behave the same way, it's very likely that a
programmer will create a single abstraction for both. But the point here is
that we should look at the nature of the two entities to conclude whether they
can be abstracted as a single thing or not. I.e. are they the same thing or
they just accidentally happen to have the same behavior?

And I do have some example to illustrate this. Here we go: Say we have an
online shop with products and users. Both entites will have a name, the
procedure to obtain a name in both cases is to pull the `name` column its
database table.

In this case we shouldn't have an abstraction for the `name` property. The
reasoning to refrain from that is that the name of a person has nothing to do
with the name of a product. And we may even may decide at some stage that the
name of a person isn't pulled from a column anymore but from a combination of
two columns, `first_name` and `last_name` for example.

But the main point is that we're accepting this code duplication because
despite having the same behavior, it has a different meaning in each context.

# 100% DRY?

I don't think programs can be totally *DRY*, some duplication will always be
around. And I'll finish this post leaving
a question rather than a conclusion, I may get back to it in a later post.

When are we allowed to have duplication in our code?

<!--
Us, programmers, strive for: efficiency & maintaniability.

Maintaniability is really efficiency too, it just takes into account the
developer time rather than the execution time.
-->

[dry]: https://en.wikipedia.org/wiki/Don%27t_repeat_yourself
[the-wrong-abstraction]: https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction
[discussion-1]: https://twitter.com/sandimetz/status/273110526706462721
[discussion-2]: https://www.reddit.com/r/programming/comments/5txp5t/duplication_is_far_cheaper_than_the_wrong/?st=jflf554y&sh=965f6b1b
[discussion-3]: https://news.ycombinator.com/item?id=12061453
