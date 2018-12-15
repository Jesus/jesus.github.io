---
layout: post
title:  Crackle Pop!
date:   2018-12-15 09:48:00 +0000
categories: programming
---

I was recently challenged to write CracklePop in any language of my choice,
here're the program specifications:

*Write a program that prints out the numbers 1 to 100. If the
number is divisible by 3, print Crackle instead of the number. If it's
divisible by 5, print Pop. If it's divisible by both 3 and 5, print
CracklePop.*

In an attempt to be original, I decided to implement it in two languages, but
in one single file!

Let me explain the two tricks I used to achieve this.

### Trick 1, back slash followed by new line character

At the beginning of the program, we'll need a couple of lines that set up
what's required so the program works in C. These lines need to be ignored when
running on Javascript.

That's done with a back-slash at the end of the line, which
is only evaluated by the C preprocessor, and will remove the new line
character. Converting this:

```c
/* This is CracklePop in C & Javascript *\
/#include <stdio.h>
```

Into this:

```c
/* This is CracklePop in C & Javascript */#include <stdio.h>
```

This trick is used twice to run a couple of setup lines in C and another
setup which is exclusive for Javascript.

That preprocessor feature is normally used for multi-line macros. Here's the
related [GCC documentation][gcc-initial-processing].

### Trick 2, language detection at run time

After our setup lines, the code is the same for both languages. So it must be
valid syntax in both, but we can introduce control flow based on the current
language. That's needed in the call to `printf`, sorted out like this:

```
'R' == 82 ? printf("%i\n", i) : printf(i + '\n');
```

The expression `'R' == 82` has different meaning on each language:

  * In Javascript, `'R'` is a string so it's obviously not equal to `82`.
    Therefore this is falsey.
  * In C, `'R'` is a `char`. Specifically, it's number 82 in the ASCII
    table, so this has a truthy result.

## Ba Dum Tsss

{% highlight javascript linenos %}
/* This is CracklePop in C & Javascript *\
/#include <stdio.h>
typedef int function; typedef int var;
//*///////////////////////////////////////////////////////////////////////////\
printf = function(a) { process.stdout.write(a) };
/*         Tested with LLVM v9.1.0 (clang), GCC v4.6.3 & Node v8.9.4         */

function cracklePop(a, b) {
  for (var i = a; i <= b; i++)
    if (i % 3 == 0 && i % 5 == 0) {
      printf("CracklePop\n");
    } else if (i % 3 == 0) {
      printf("Crackle\n");
    } else if (i % 5 == 0) {
      printf("Pop\n");
    } else {
      'R' == 82 ? printf("%i\n", i) : printf(i + '\n');
    }
}

function main() {
  cracklePop(1, 100);
} main();
{% endhighlight %}

Now that we've got so far writing a completely useless program with no real
application, let's not stop here. Out there, there's a whole world of
challenges like this.

If you enjoyed this fiddle, you should check out Yusuke Endoh's
[quine relay][quine-relay], and his talk
[Esoteric, Obfuscated, Artistic Programming in Ruby][mames-talk].

I'll finally wrap up this post leaving a challenge: **Would it be
possible to add a third language?**

[gcc-initial-processing]: https://gcc.gnu.org/onlinedocs/gcc-3.3.3/cpp/Initial-processing.html
[quine-relay]: https://github.com/mame/quine-relay
[mames-talk]: https://www.youtube.com/watch?v=6K7EmeptEHo
