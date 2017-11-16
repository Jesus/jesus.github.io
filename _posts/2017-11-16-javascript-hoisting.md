---
layout: post
title:  Javascript hoisting
date:   2017-11-16 20:19:29 +0100
categories: javascript programming
---
Computers often surprise me. Going down the road of understanding all the
small details of programming languages, today I'm writing about
*javascript hoisting*.

Let's start by trying to guess what the output of this JS snippet would be:

```javascript
a = "hello";
b = "crocodile";

function greet() {
  console.log(a + " " + b);

  if (false) {
    function b() {};
  }
}

greet();
```

Indeed, this will print `hello undefined` on screen. But I expected to see
`hello crocodile`!

Why is `b` *undefined* if we've defined its value as `"crocodile"`?

It turns out that the name `b` inside the function scope is declared as a
function. It doesn't seem to matter that the function is declared after the
call to `console.log()` or the fact that it's inside a conditional statement
that will never be executed.

The same happens with variable declarations:

```javascript
a = "hello";
b = "crocodile";

function greet() {
  console.log(a + " " + b);

  if (false) {
    var b = "alligator";
  }
}

greet(); // => hello undefined
```

In this case, `b` is a local variable inside the function scope so the global
`b` isn't visible from inside the function. The result is the same.

## Why?

A popular way to explain this is by saying that function and variable
declarations are always *hoisted* to the top of the function.

From [MDN web docs][mdn]:
  *Because variable declarations (and declarations in general) are processed
  before any code is executed, declaring a variable anywhere in the code is
  equivalent to declaring it at the top.*

And my preferred way to explain it is by saying that variable and function
declarations are processed at compile time. The javascript engine will use your
declarations to build the set of available names on each scope.

Note that only the declaration is processed first, not the definition. That's
why the program prints `undefined` instead of `alligator`.

So, the following code would be equivalent:

```javascript
a = "hello";
b = "crocodile";

function greet() {
  var b;
  console.log(a + " " + b);

  if (false) {
    b = "alligator";
  }
}

greet(); // => hello undefined
```

## So what?

Since I discovered this, I always put my declarations at the top of the
scope, to avoid any potential confusion.

If you're very curious, there's so much more to learn about declarations and
scoping ([let][let], [const][const], [closures][closures], etc.), but I wanted
to be concise. I hope you found it brief and useful.

[mdn]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var#var_hoisting
[let]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let
[const]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const
[closures]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures
