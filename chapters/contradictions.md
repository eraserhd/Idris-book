# Contradictions

## `impossible` Cases

In Idris, we can write:

```idris
total funnyMath : (3 = 4) -> (4 = 7)
funnyMath Refl impossible
```

The type signature can be read, "From a proof that 3 is equivalent to 4, I can
always produce a proof that 4 is equivalent to 7."

We can write `impossible` instead of returning a value since Idris knows
we can't actually receive a `Refl` value here.  It only learns about what’s
possible when matching constructors, though, so if we don't spell out the
`Refl`, Idris complains:

```idris
total funnyMath : (3 = 4) -> (4 = 7)
funnyMath _ impossible
```

Produces:
```
funnyMath _ is a valid case.
```

We can see why using `:printdef (=)`:
```idris
data (=) : A -> B -> Type where
  Refl : x = x
```

Since `x` appears more than once, matching `Refl` requires Idris to *unify*
the different expressions for `x`.  Idris knows that `3` and `4` are
constructed differently so it decides the case impossible.  Notice there's
nothing magical about `=` here!

However, unification can't find every impossible case:

```idris
total nope : x + 7 = x + 5
nope Refl impossible
```

Produces:
```
nope Refl is a valid case
```

## `Void` and `void`

A type is said to be "uninhabited" when there are no values of that type.
The type `3 = 4` is uninhabited, but `Void` is the simplest uninhabited type:

```idris
data Void : Type where
```

Having no constructors, we can't create any `Void` values.  But we can still
use it in interesting ways.

`void` (with a lowercase "v") is a function from `Void` to *any* type, so can
fill any hole, given an impossible value.

```idris
total exFalsoQuodlibet : Void -> 6 = 42
exFalsoQuodlibet v = void v
```

Notice we can't use `impossible` here since it would require matching a
constructor of `Void`--but the effect is the same.

This is an example of "the principle of explosion," often phrased, "from
contradiction, anything follows."  In general, it is possible to prove any
claim given contradictory premises.

It is useful to think of values of `Void` as "falsity" or "contradiction."


## `Not`

Most kinds of logic have some kind of first-class negation.  Most programming
languages have `!`, so that `!false` is equivalent to true.  (Idris has this
for boolean values as well, though it’s called `not`.)

When working with propositions, Idris has `Not`.  The proposition
`Not (3 = 4)` claims that `3` is not equivalent to `4`.

There’s a little more to the picture, though – `Not` is not a type itself, but
a function that produces a type.  We can inspect its code with `:printdef Not`
in the REPL:

```idris
Not : Type -> Type
Not a = a -> Void
```

Since `Void` is uninhabited, the only way we can make a function of type
`a -> Void` is by demonstrating `a` has no values.  A nice way to think of
it is, "From a proof of `a`, we can show contradiction."

Since eliminating implication corresponds to function application, we have
a nice way to use any proof of `a` and `Not a` to produce a `Void`, which
allows us to dispatch the case with `void`.  We use this to fill a hole in a
total function when Idris's unifier cannot detect the contradiction on its
own:

```idris
total
oneLTENatNotZero : (x : Nat) -> Not (x = 0) -> 1 `LTE` x
oneLTENatNotZero Z     xNotZero = void (xNotZero Refl)
oneLTENatNotZero (S k) xNotZero = LTESucc LTEZero
```

In the first equation, we've matched `x` with `Z`, so for the scope of this
equation, Idris believes that `x` is the same as `0`.  This means that
`xNotZero` on this line is a proof that `0 = 0 -> Void`.  We can easily
provide a proof of `0 = 0` using `Refl`, so we write `(xNotZero Refl)` to
get a value of type `Void`.  We still need a proof of ``1 `LTE` 0``, though,
so we use `void` to get it.


## `Uninhabited` and `absurd`

There are many useful uninhabited types which come up frequently such as
`True = False`, `NonEmpty []`, and `IsJust Nothing`.  It would be nice if we
didn't have to worry about the different ways to extract `Void` values from
each of these types, and so Idris provides us with the `Uninhabited`
interface:

```idris
interface Uninhabited t where
  total uninhabited : t -> Void
```

We can implement it for an empty type:

```idris
implementation Uninhabited (True = False) where
  uninhabited Refl impossible
```

Once we have an implementation of `Uninhabited` for a type, we can use
`absurd` -- a shortcut for `void (uninhabited x)` to solve cases.

```idris
slime : True = False -> 42
slime p = absurd p
```

