---
layout: post
title:  Javascript hoisting
date:   2017-11-16 20:19:29 +0100
categories: javascript programming
---
To keep going through the path of chasing misconceptions, today I've decided to
write about *javascript hoisting*.

Let's start by trying to guess what the output of this JS snippet would be:

```javascript
a = "hello";
b = "crocodile"

function greet() {
  console.log(a + " " + b);

  if (false) {
    function b() {};
  }
}

greet();
```

I was surprised that the output of that code was `hello undefined` rather than
`hello crocodile`.

Why is `b` *undefined* if we've defined its value as `"crocodile"`?

The answer is because the name `b` in the function scope is declared as a
function. It doesn't seem to matter that the function is declared after the
call to `log()` or the fact that it's inside a conditional statement that will
never be executed.

The same happens with variable declarations:

```javascript
a = "hello";
b = "crocodile"

function greet() {
  console.log(a + " " + b);

  if (false) {
    var b = "alligator";
  }
}

greet(); // => hello undefined
```

In this case, `b` is a local variable inside the function scope so the global
`b` just doesn't exist inside the function. The result is the same.

A popular way to explain this is by saying that function and variable
declarations are always *hoisted* to the top of the function.

From [MDN web docs][mdn]:
  *Because variable declarations (and declarations in general) are processed
  before any code is executed, declaring a variable anywhere in the code is
  equivalent to declaring it at the top.*

And my preferred way to explain it is by saying that declarations are processed
at compile time before anything in the function gets executed at all. But note
that the definition won't happen at compile time but during execution.

So, the
following code would be equivalent:

```javascript
a = "hello";
b = "crocodile"

function greet() {
  var b;
  console.log(a + " " + b);

  if (false) {
    b = "alligator";
  }
}

greet(); // => hello undefined
```

[mdn]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/var#var_hoisting
