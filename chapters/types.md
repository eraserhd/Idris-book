# Types

<!-- Idea -
  1. Clear up lingering ideas of "type";
  2. Introduce type as a judgement system (?) and explain type-checking,
  canonical values, and non-canonical expressions.
-->

## Types of Types

In C, "type" is how the compiler interprets memory.  A 16-bit variable with
all bits "on" might represent 65,535 or -1, depending on whether the variable
has the type `unsigned short` or `signed short`[^c-short].

In Ruby or Scheme, "type" is a quality of a value rather than a variable.  A
variable `x` might be a string here and a numeric value later.  We ask about
the type at runtime with expressions like `(string? x)`.  We call this
"dynamic typing" to distinguish it from C's "static typing."

In C++ or Ruby, which methods can be called on the value and how those methods
behave is mostly part of the type; "type" and "class" are tightly related, if
not synonymous.

In Haskell, type is a judgement about a name.  Sometimes we tell the compiler
and sometimes the compiler figures it out.

C allows--and often requires--us to reinterpret a variable of one type as a
different type, so we call it "weakly typed."

## Idris Types

Idris is statically typed, strongly typed[^believe-me], and types are
judgements applied to names.

In Idris, types are not so much an implementation detail, as in C, but a
foundational piece of the logical system that allows us to *prove* programs.

<!-- mathematical types! -->
<!-- what does Luo say about types? -->

## Type Checking

Idris has a system of typing rules that it uses to ensure a program's
coherence.

The rules are usually written something like this (warning: Greek letters,
though I will fully explain):

<!-- diagram this out with arrows and scratch marks -->

\[\frac{
  \Gamma\vdash e_1 : \sigma\to\tau
  \quad
  \Gamma\vdash e_2 : \sigma
}{\Gamma\vdash e_1 e_2 : \tau}\mbox{(app)}
\]

<!-- "terminal" and "non-terminal" values (ugh, what are they called?) -->
<!-- Inductive types -->
<!-- Construction/destruction -->
<!-- Nat! S/Z/literals -->

[^c-short]: Assuming your C compiler has 16-bit shorts.
[^believe-me]: We *do* have `believe_me`, a way to escape the type system; however, it is scary and we aren't expected to use it as a matter of course.
