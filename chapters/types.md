## Types

<!-- Idea -
  1. Clear up lingering ideas of "type";
  2. Introduce type as a judgement system (?) and explain type-checking,
  canonical values, and non-canonical expressions.
-->

In C, "type" is about how to interpret bits.  `unsigned short` and
`signed short` both have the same number of bits, but (assuming we are using
a compiler where `short` is 16 bits) `0xFFFF` represents either 65,535 or -1,
depending on the memory location's type.

In a dynamically-typed langugage like Scheme, "type" is a quality of a value
rather than a location.  We might query the type of a value with an expression
like `(boolean? x)`, which indicates whether `x` is either the true value or
the false value.

In object-oriented languages like Smalltalk, Python, and Ruby, which methods
can be called on the value and how those methods behave is (mostly) part of
the type; "type" and "class" are tightly related, if not synonymous.

In ML-based languages like Haskell and Idris, type is a judgement about a
name.  Sometimes we tell the compiler and sometimes the compiler figures it
out.

<!-- yadda yadda want it to mean something? -->

C has back doors to reinterpret the type, so we call it "weakly typed."
Weakness would hurt us in Idris, since one of our goals is to prove things
about our program.  So Idris is "strongly typed."
