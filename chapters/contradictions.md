# Contradictions

## `impossible` Cases

In Idris, we can write:

```idris
total funnyMath : (3 = 4) -> (4 = 7)
funnyMath Refl impossible
```

The type signature can be read, "From any proof of `3 = 4`, I can produce a
proof that `4 = 7`."  Alternately, "`3 = 4` implies `4 = 7`."

We are able to write `impossible` instead of returning a value since Idris
knows we can't actually receive `Refl` here.

Idris only learns about what's possible when matching constructors, though,
so if we don't spell out "`Refl`", Idris complains:

```idris
total funnyMath : (3 = 4) -> (4 = 7)
funnyMath _ impossible
```

Produces:
```
funnyMath _ is a valid case.
```

We can see why in the REPL:
```idris
Idris> :printdef (=)
data (=) : A -> B -> Type where
  Refl : x = x
```

Since `x` appears more than once, matching `Refl` requires Idris to *unify*
the different expressions for `x`.  Since `3` and `4` are constructed
differently, the case is impossible.  Notice there's nothing magical about
`=` here!

However, unification doesn't find every impossible case:

```idris
total unificationUnhelpful : x + 7 = x + 5
unificationUnhelpful Refl impossible
```

Produces:
```
nope Refl is a valid case
```

## `Void` and `void`

A type is called "uninhabited" when it contains no values.  `3 = 4` is
uninhabited.  `Void` is the simplest uninhabited type:

```idris
data Void : Type where
```

We can't create any `Void` values because we have no constructors.  We can
still use it in interesting ways: `void` (with a lowercase "v") is a function
from `Void` to *any* type, so we can use an impossible value to fill *any*
hole.

```idris
total explosion : Void -> 6 = 42
explosion v = void v
```

Notice we can't use `impossible` here since it would require matching a
constructor of `Void`--but the effect is the same.

This is an example of "the principle of explosion," often phrased, "from
contradiction, anything follows."  In general, it is possible to prove any
claim given contradictory premises.

It is useful to think of values of `Void` as "falsity" or "contradiction."


## `Not`

`Not (3 = 4)` proposes that `3 = 4` does not hold.  `Not` is a function that
operates on types, which we can inspect in the REPL:

```idris
Idris> :printdef Not
Not : Type -> Type
Not a = a -> Void
```

Since `Void` has no values, the only way to construct a total function from
`a` to `Void` is by demonstrating `a` has no values.  A nice way to think of
it is, "From a proof of `a`, we can show contradiction."

<!-- below is bad, we need to clarify -->

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

Many uninhabited types come up frequently: `True = False`, `NonEmpty []`,
`IsJust Nothing`.  Idris gives us the `Uninhabited` interface so that we
don't have to worry about how to extract `Void` values from many types:

```idris
interface Uninhabited t where
  total uninhabited : t -> Void
```

We can implement it for an empty type:

```idris
implementation Uninhabited (True = False) where
  uninhabited Refl impossible
```

If we have an implementation of `Uninhabited`, `absurd` becomes interesting:

```idris
Idris> :printdef absurd
absurd : Uninhabited t => t -> a
absurd h = void (uninhabited h)
```

Now we can solve cases by calling out impossible values from empty types:

```idris
total slime : True = False -> 42
slime p = absurd p
```
