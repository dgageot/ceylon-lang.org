---
layout: tour
title: Tour of Ceylon&#58; Type aliases and type inference
tab: documentation
unique_id: docspage
author: Gavin King
---

# #{page.title}

This is the seventh step in the Tour of Ceylon. The 
[previous installment](../sequences) introduced various kinds of iterable
objects. Now it's time to explore Ceylon's type system in more detail. 

In this chapter, we're going to discuss _type aliases_ and _local type 
inference_, two features of the language that help reduce the verbosity
of statically typed code.


## Type aliases

It's often useful to provide a shorter or more semantic name to an existing 
class or interface type, especially if the class or interface is a 
parameterized type. For this, we use a *type alias*.

To define an alias for a class or interface, we use a fat arrow, for example:

<!-- try: -->
<!-- cat: 
    class Person() {
    }
-->
    interface People => Set<Person>;

A class alias must declare its formal parameters:

<!-- try: -->
<!-- check:none:ArrayList -->
    shared class People({Person*} people) => ArrayList<Person>(people);

If you need to create an alias for a union or intersection type you have to 
use the `alias` keyword:

    alias Num = Float|Integer;

A type alias may be parameterized, and have type constraints, which we'll
[learn about later](../generics/#generic_type_constraints):

    class Named<Value>(String name, Value val) 
            given Value satisfies Object
            => Entry<String,Value>(name,val);

Type aliases help us reduce verbosity, because instead of repeatedly writing
out the same generic type, for example `Set<Person>`, we can use a snappier
alias, such as `People`. But in some cases, Ceylon lets us omit the type
altogether. 


## Type inference

So far, we've always been explicitly specifying the type of every declaration. 
This generally makes code, especially example code, much easier to read and 
understand.

However, Ceylon does have the ability to infer the type of a local variable 
or the return type of a local method. Just place the keyword 
`value` (in the case of a local variable) or `function` (in the case of a 
local method) in place of the type declaration.

<!-- try-pre:
    Float pi = 3.14159;
    class Polar(Float angle, Float radius) {}

-->
<!-- cat-id: Point -->
<!-- cat-id: Polar -->
<!-- cat: Float pi = 3.14159; -->
<!-- cat: void m() { -->
    value polar = Polar(pi, 2.0);
    value operators = { "+", "-", "*", "/" };
    function add(Integer x, Integer y) => x+y;
<!-- cat: } -->

There are some restrictions applying to this feature. You can't use `value` 
or `function`:

* for declarations annotated `shared`,
* for declarations annotated `formal`,
* when the value is specified later in the block of statements, or
* to declare a parameter.

These restrictions mean that Ceylon's type inference rules are quite simple. 
Type inference is purely "right-to-left" and "top-to-bottom". The type of any 
expression is already known without needing to look to any types declared 
to the left of the `=` specifier, or further down the block of statements.

* The inferred type of a reference declared `value` is just the type of the 
  expression assigned to it using `=`.
* The inferred type of a getter declared `value` is just the union of the 
  returned expression types appearing in the getter's `return` statements
  (or `Nothing` if the getter has no `return` statement).
* The inferred type of a method declared `function` is just the union of the 
  returned expression types appearing in the method's `return` statements
  (or `Nothing` if the method has no `return` statement).


## Type inference for iterable constructor expressions

What about iterable constructor expressions expressions like this:

<!-- try-pre:
    abstract class Point() of Polar | Cartesian { }
    class Polar(radius, angle) extends Point() {
        shared Float radius;
        shared Float angle;
    }
    class Cartesian(x, y) extends Point() {
        shared Float x;
        shared Float y;
    }

-->
<!-- cat-id: Point -->
<!-- cat-id: Polar -->
<!-- cat: void m() { -->
    value coords  = { Polar(0.0, 0.0), Cartesian(1.0, 2.0) };
<!-- cat: } -->

What type is inferred for `coords`? You might answer: 

> `{X+}` where `X` is the common superclass or super-interface 
> of all the element types. 

But that can't be right, since there might be more than one common 
supertype.

The correct answer is that the inferred type is `{X*}` where `X` is the 
union of all the element expression types. In this case, the type is 
`{Polar|Cartesian*}`. Now, this works out nicely, because 
`Iterable<T>` is [covariant](../generics#covariance_and_contravariance) 
in `T`. So the following code is well-typed:

<!-- try-pre:
    abstract class Point() of Polar | Cartesian { }
    class Polar(radius, angle) extends Point() {
        shared Float radius;
        shared Float angle;
    }
    class Cartesian(x, y) extends Point() {
        shared Float x;
        shared Float y;
    }

-->
<!-- cat-id: Point -->
<!-- cat-id: Polar -->
<!-- cat: void m() { -->
    value coords =
            { Polar(0.0, 0.0), 
              Cartesian(1.0, 2.0) }; //type {Polar|Cartesian+}
    {Point*} coords = sequence;
<!-- cat: } -->

As is the following code:

<!-- try-post:
    print(numbers);
-->
<!-- cat: void m() { -->
    value nums = { 12.0, 1, -3 }; //type {Float|Integer+}
    {Number+} numbers = nums;
<!-- cat: } -->

What about iterables that produce `null`s? Well, do you 
[remember](../basics#dealing_with_objects_that_arent_there) the type of `null` 
was [`Null`](#{site.urls.apidoc_current}/class_Nothing.html)?

<!-- try-post:
    print(s else "null");
-->
<!-- cat: void m() { -->
    value sequence = { null, "Hello", "World" }; //type {String?+}
    String? s = strings.first;
<!-- cat: } -->

The declared type of the attribute `first` of `Iterable<Element>` is 
`Element?`. Here, we have an `Iterable<String?>` Substituting `String?` 
for `Element`, we get the type `String??`, that is, `Null|Null|String`, 
which is simply `Null|String`, written `String?`. Of course, the 
compiler can figure out that kind of thing for us, we could have simply 
written:

<!-- try-post:
    print(s else "null");
-->
<!-- cat: void m() { -->
    value sequence = { null, "Hello", "World" }; //type {String?+}
    value s = strings.first; //type String?
<!-- cat: } -->

The same thing works out for sequences:

<!-- try-post:
    print(s else "null");
-->
<!-- cat: void m() { -->
    value tuple = [null, "Hello", "World"]; //type [Null,String,String]
    String?[] strings = tuple;
    value s = strings[0]; //type String?
<!-- cat: } -->

It's interesting just how useful union types turn out to be. Even if you only 
rarely write code with explicit union type declarations, they're still there, 
under the covers, helping the compiler solve some hairy, otherwise-ambiguous, 
typing problems.

Note that what we've just seen is really just a special case of the algorithm
Ceylon uses for _generic type argument inference_, and all of the above works
just as well for [user-written generic types](../generics) as it does for 
`Iterable`. 


## Anonymous classes and type inference

Since an anonymous class doesn't have a name, Ceylon replaces anonymous classes 
with the intersection of their supertypes when performing type inference:

    interface Foo {}
    interface Bar {}
    object foobar satisfies Foo&Bar {}
    value fb = foobar; //inferred type Basic&Foo&Bar
    value fbs = { foobar, foobar }; //inferred type {Basic&Foo&Bar+}


## There's more...

Next we'll explore some more details of the type system, starting with
[union types, intersection types, enumerated types, and type switching](../types). 
Then, after that, we'll be ready to discuss [generic types](../generics). 
