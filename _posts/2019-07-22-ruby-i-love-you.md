---
layout: post
title:  Ruby, I love you but you're bringing me down
date:   2019-07-22 11:14:00 -0400
categories: programming ruby
---

Ruby is great, I find it super expressive and concise. But sometimes you find
corner cases which are very hard to explain. During an interview on the
philosophy of Ruby, Matz said:

> Sometimes people jot down pseudo-code on paper. If that pseudo-code runs
> directly on their computers, it's best, isn't it?
>
> <cite>-- [Yukihiro Matsumoto][matz], creator of Ruby</cite>

So clearly it's important for this language to be readable by humans, I guess
that's the reason why this language isn't as mathematically strict as many
others. It aims to be easily understood and it doesn't even have a standard
specification.

The lack of a spec sometimes makes it impossible to reason about whether
something is wrong or intended within the language. And I have one example of
this.

Take the following program:

```ruby
def some_method(x)
  x.class
end
a = false
b = 42
```

Sometimes you hit confusing features but you quickly find a good explanation:

```ruby
some_method(a || b)    # => Integer (ok, as expected)
some_method a || b     # => Integer (ok)
some_method((a || b))  # => Integer (ok)
some_method a or b     # => FalseClass (ok?)
```

This may be confusing but makes perfect sense. While you can use both the `or`
operator and the `||` they're not exactly the same. The `or` & `and` operators
have a much lower precedence, so the last call is equivalent to
`some_method(a) or b`. Here's another [blog post by Preston Lee][preston-lee]
on these operators if you're very interested. So far Ruby is clear enough.

On the following example, a good explanation isn't found that easily:

```ruby
some_method((a or b))  # => 42 (ok)
some_method (a or b)   # => 42 (ok)
some_method(a or b)    # SyntaxError (Hmm)
```

That's certainly confusing, there's no ambiguity in the last line so I would
expect its syntax to be as valid as the other two lines.

I really have no idea if this is a bug or expected behavior. It might
happen that this isn't documented or described anywhere and if that's the case
the only person who can answer that question is Matz.


`¯\_(ツ)_/¯`

## Postface and further read

The claim of this article, that **Ruby lacks a strict unambiguous
specification** could be argued. So for those who aren't aware of this topic,
find below a brief walkthrough with links to sources to catch up.

There's a [ISO Ruby spec][iso-ruby-spec], but it got deprecated pretty much
since it was released. This document is useless today.

Acording to JRuby's project leader [Charles Nutter][charles-nutter],
[Ruby's spec is the set of existing Ruby programs][charles-nutter-spec].
That's a very volatile definition in my opinion, but he's right.

Rubinius creator [Evan Phoenix][evan-phoenix] created RubySpec in 2006,
a project to test Ruby implementations with actual code but this
[never got adopted by the MRI][rubyspec].
Despite the project being dead now, the same idea was continued by
[The Ruby Spec Suite][ruby-spec-suite]. And that's the closest thing to a
spec we currently have.

[matz]: https://www.artima.com/intv/ruby.html
[ruby-story]: https://twobithistory.org/2017/11/19/the-ruby-story.html
[preston-lee]: https://www.prestonlee.com/2010/08/04/ruby-on-the-perl-origins-of-and-versus-and-and-or/
[iso-ruby-spec]: https://www.ipa.go.jp/files/000011432.pdf
[charles-nutter]: http://blog.headius.com/about/
[charles-nutter-spec]: https://twitter.com/headius/status/590536502049800192
[evan-phoenix]: https://github.com/evanphx
[rubyspec]: https://github.com/rubinius/rubinius-website-archive/blob/cf54187d421275eec7d2db0abd5d4c059755b577/_posts/2014-12-31-matz-s-ruby-developers-don-t-use-rubyspec.markdown
[ruby-spec-suite]: https://github.com/ruby/spec
