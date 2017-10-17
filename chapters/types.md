## Types

<!-- Idea -
  1. Clear up lingering ideas of "type";
  2. Introduce type as a judgement system (?) and explain type-checking,
  canonical values, and non-canonical expressions.
-->

### Types of Types

In C, "type" is how the compiler interprets memory.  A 16-bit variable with
all bits "on" might represent 65,535 or -1, depending on whether the variable
has the type `unsigned short` or `signed short`[^c-short].

In Scheme, "type" is a quality of a value rather than a variable.  A variable
`x` might be a string here and a numeric value later.  We ask about the type
at runtime with expressions like `(string? x)`.  We call this "dynamic typing"
to distinguish it from C's "static typing."

In C++ or Ruby, which methods can be called on the value and how those methods
behave is mostly part of the type; "type" and "class" are tightly related, if
not synonymous.

In ML-based languages like Haskell and Idris, type is a judgement about a
name.  Sometimes we tell the compiler and sometimes the compiler figures it
out.

C allows us to reinterpret the type, so we call it "weakly typed."
Weakness would hurt us in Idris, since one of our goals is to prove things
about our program.  So Idris is "strongly typed."

<!-- Idris has a back door... mention here? -->

### Idris Types


### Type Checking

[^c-short]: Assuming your C compiler's `short`s are 16-bits.
