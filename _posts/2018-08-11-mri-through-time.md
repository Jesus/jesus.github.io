---
layout: post
title:  Ruby through time
date:   2018-08-11 09:16:00 +0000
categories: programming
---

When Yukihiro Matsumoto created Ruby in 1993, performance wasn't one of the
highest priorities. He had one main goal in mind:

> For me, the purpose of life is, at least partly, to have joy. Programmers
> often feel joy when they can concentrate on the creative side of programming,
> so Ruby is designed to make programmers happy.
>
> <cite>-- [Yukihiro Matsumoto][matz], creator of Ruby</cite>

So we want to have a language that is cool for humans and now we also want it
to run fast. But you can't have it all! Or can you?

Looks like Ruby 2.6 will be the first version to compile your code (at least
parts of it) all the way down to machine code and the core team aims to make
Ruby 3.0 three times faster than 2.0. So it's becoming very competitive, but
this didn't happen overnight, let's explore the main breakthroughs along the
way.

### First approach, AST evaluation

When Matz originally wrote the first interpreter, it would parse the code,
build its [Abstract Syntax Tree][ast] and just evaluate it. This is the most
basic approach and implies a significant overhead to traverse the tree while
the program is being executed.

Invoking the interpreter involved these steps:

 1. Tokenization.
 2. Parsing, to build an AST.
 3. Evaluation of the AST.

### YARV

Ruby 1.9 came with YARV, Yet Another Ruby Virtual machine, by
[Koichi Sasada][koichi]. Which was a great step forward for a series of
reasons.

This would be the new approach:

  1. Tokenization.
  2. Parsing.
  3. Compile to YARV instructions.
  4. Run the VM.

So more steps than the former interpreter, but it ends up being more efficient
than traversing an AST because:

  * The instructions are now in a format that the VM can quickly access
    sequentially, faster than a tree.
  * It's possible to add optimizations at compile time.

### The JIT

Finally, we're now at the doorstep of having a JIT.

Last year [Vladimir Makarov][vnmakarov] added what he called a Method JIT, or
MJIT.
What this adds is a way to compile a ruby method to machine code, it relies
on an external compiler such as GCC or Clang to achieve so.

The problem with Makarov's approach was that it changed the YARV's instruction
set to produce its intermediate representation that would then produce C code
ready for the C compiler that would then produce a loadable binary. The fact
that it's changing the instruction set implies that this is a huge changeset.

[Takashi Kokubun][kokubun] then took much of the work on MJIT to build the
YARV-MJIT, which applies the same basic idea as the MJIT but preserves
YARV's instruction set, making it more feasible for the Ruby project to
assimilate the update. In fact, his branch was merged in February and the
JIT can now be enabled if you're using one of the Ruby 2.6 preview releases.

This has already got very good results in [benchmarks][benchmarks] and the
great news is that there's still a lot of room for improvement. So it's
looking very promising.

I think it's safe to say that this'll ship in Ruby's next Christmas release.

## Why so hard?

It's interesting to see how performance has become more important only after
time, when there was a huge demand for it. If Matz had considered it when he
was designing the language he wouldn't have taken some decisions that made
the language quite unique.

So basically, it's hard to have better performance because we need to
preserve compatibility with a design that prioritized developers instead of
computers.

[matz]: https://es.wikipedia.org/wiki/Yukihiro_Matsumoto
[ast]: https://en.wikipedia.org/wiki/Abstract_syntax_tree
[vnmakarov]: https://github.com/vnmakarov
[koichi]: http://www.atdot.net/~ko1/
[kokubun]: https://github.com/k0kubun
[benchmarks]: https://github.com/benchmark-driver/mjit-benchmarks
[playing-with-mjit]: https://www.johnhawthorn.com/2018/02/playing-with-ruby-jit-mjit/
