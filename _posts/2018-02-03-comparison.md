---
layout: post
title:  ???
date:   2018-02-04 09:26:00 +0000
categories: ruby
---

Let's explore the different ways we can compare objects in Ruby. We'll
investigate the implementation of the following methods:

  * Equality, or `==`.
  * Case Equality, or `===`.
  * `eql?`
  * `equal?`

For conciseness, I've decided to leave out the following ones, maybe will be
covered in a different post:

  * Pattern Match, or `=~` and its opposite: `!~`.
  * `instance_of?`, `kind_of?` & `is_a?`.
  * The spaceship operator, or `<=>`.

## The equality operator

So let's start with the most common one. The double equal `==` is how you
normally check that two objects have the same value.

The [official Ruby documentation][object-rdoc] says that it's the class's responsibility to
determine if other object is equal or not, example:

```ruby
class Car
  attr_accessor :brand, :plate_number

  def initialize(brand, plate_number)
    self.brand = brand
    self.plate_number = plate_number
  end

  def ==(other)
    self.plate_number == other.plate_number
  end
end

Car.new("Ford", "ADL 4681") == Car.new("Volkswagen", "ADL 4681")
# => true
Car.new("Ford", "ADL 4681") == Car.new("Ford", "2039 BKX")
# => false
```

If you leave this method undefined, you'll inherit the default
implementation builtin with Ruby. Let's find it!

## Diving into Ruby internals

We're exploring Ruby's implementation of `==`, `===`, `equal?` & `eql?`.
In `object.c`, we can find:

```c
rb_define_method(rb_cBasicObject, "==", rb_obj_equal, 1);
rb_define_method(rb_cBasicObject, "equal?", rb_obj_equal, 1);
```

Furthermore, a few lines below we can also find this:

```c
rb_define_method(rb_mKernel, "eql?", rb_obj_equal, 1);
```

Remember the basic class inheritance chain:

```
+--------+    +--------+    +-------------+
| Object |--->| Kernel |--->| BasicObject |
+--------+    +--------+    +-------------+
```

So the three methods `==`, `equal?` & `eql?` map to the same C function:
`rb_obj_equal`.

What about the triple equal? that one is mapped to `rb_equal` instead:

```c
rb_define_method(rb_mKernel, "===", rb_equal, 1);
```

But looking at its definition, we find out it might end up calling
`rb_obj_equal` too:

```c
VALUE
rb_equal(VALUE obj1, VALUE obj2)
{
    VALUE result;

    if (obj1 == obj2) return Qtrue;
    result = rb_funcall(obj1, id_eq, 1, obj2);
    if (RTEST(result)) return Qtrue;
    return Qfalse;
}
```

That function, translated into English means:

  * If `obj1 == obj2` (in its C meaning), then return true.
  * Otherwise, rely on the object's implementation of `==`. Ie. return the
    result of `obj1.==(obj2)`.


By default, the implementation of `==` is bound to `rb_obj_equal` so `===` will
end up invoking `rb_obj_equal` in many cases.

Let's now see where this all seems to be converging to, the definition of
`rb_obj_equal`:

```c
VALUE
rb_obj_equal(VALUE obj1, VALUE obj2)
{
    if (obj1 == obj2) return Qtrue;
    return Qfalse;
}
```

So it boils down to that simple value comparison at C level.

So the question at this point would be: Why do we need four different method
names pointing at the same C function?

---




Both `==` and `equal?` are mapped to the same method, part of the `Object`
class:


Also, `eql?` is mapped to the same method, but it's appended as part of
`Kernel` rather than `Object`:

```c
rb_define_method(rb_mKernel, "eql?", rb_obj_equal, 1);
```

Alright, so all `==`, `equal?` & `eql?` are mapped to `rb_obj_equal`. Let's
see its definition:


So its result boils down to that comparison at C level. This has a

```c
rb_define_method(rb_cNumeric, "eql?", num_eql, 1);
```

Let's remember the inheritance chanin:

```ruby
class Cactus; end
Cactus.ancestors
# => [Cactus, Object, Kernel, BasicObject]
```

And this the function's definition:


### Edge cases

This is the triple equal implementation:


If both objects are the same, it's evaluated as true. Otherwise,


`Numeric`'s implementation of `eql?` delegates its result to `rb_equal`.


[mri]: https://en.wikipedia.org/wiki/Ruby_MRI
[object-rdoc]: https://ruby-doc.org/core-2.5.0/Object.html#method-i-eql-3F
