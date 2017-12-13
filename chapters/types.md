# Types

<!-- Idea -
  1. Clear up lingering ideas of "type";
  2. Introduce type as a judgement system (?) and explain type-checking,
  canonical values, and non-canonical expressions.
-->

<!--
  TODO: Per discussion with Max: Non-programming types are that we have
  preexisting objects and we organize them into types, but in computers, we
  have a more constructive notion.
-->

In C, "type" is how the compiler interprets memory.  A 16-bit variable with
all bits "on" might represent 65,535 or -1, depending on whether the variable
is `unsigned` or `signed`.  We often tell a C compiler to treat a variable as
if it had a different type (called _casting_).  Because we can do this, and
because we are expected to do so as a matter of course, we call C "weakly
typed."  It is useful to think of C-style types as annotations which help the
compiler understand how to arrange and access memory.

In Ruby or Scheme, "type" is a quality of a value instead of a variable.  A
single variable `x` might contain a string here and a number later.  We ask
about type at runtime with expressions like `(string? x)`.  We call this
"dynamic typing" to distinguish it from C's "static typing."  We generally
can't inspect how a value is arranged in memory or reinterpret it without
using built-in operations which know how to do so safely.

In Haskell, type is a judgement about a name.  Sometimes we tell the compiler
and sometimes the compiler figures it out.

Idris is statically typed, strongly typed, and types are judgments applied to
names.

In Idris, types are not so much an implementation detail, as in C, but a
foundational piece of the logical system that allows us to *prove* programs.

We assert a type for a name with `:`, like so:

```idris
myName : Int
```

In Idris and type theory, this is how we say `myName` has type `Int`.

<!-- mathematical types! -->
<!-- what does Luo say about types? -->

## Construction/destruction

<!-- Construction/destruction -->

## Canonical and Non-Canonical Values

<!-- canonical and "non-terminal" values (ugh, what are they called?) -->
<!-- Inductive types -->

Idris has a system of typing rules that it uses to ensure a program's
coherence.

The rules are usually written something like this:  If I have an
expression of type `x : a `a` and a function of type `a -> b` (read, "takes
an `a` and produces a `b`), and they are



<!-- Nat! S/Z/literals -->
