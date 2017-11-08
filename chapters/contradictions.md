## Contradictions

### `impossible` Cases

In Idris, we can write:

```idris
total funnyMath : (3 = 4) -> (4 = 7)
funnyMath Refl impossible
```

The type signature can be read, “From a proof that 3 is equivalent to 4, I can
produce a proof that 4 is equivalent to 7.”

We can write `impossible` instead of returning a value because Idris knows
we can't actually get a `Refl` value here.  It only learns about what’s
possible when matching constructors, so if we don't spell out the `Refl`,
Idris complains:

```idris
total funnyMath : (3 = 4) -> (4 = 7)
funnyMath _ impossible
```

Produces:
```
funnyMath _ is a valid case.
```

We can see why using `:printdef Refl`:
```idris
Refl : x = x
```

Since `x` appears more than once, matching `Refl` requires Idris to
to *unify* the different expressions for `x`.  Idris knows that `3` and `4`
are constructed differently so it decides the case impossible.

Notice how there's nothing magical about `=` here!

However, unification won’t find every impossible case.

<!-- an example Idris can't figure out -->

### `Void`

`Void` is the simplest impossible type:

```idris
data Void : Type where
```

Since `Void` has no constructors, we can never have a value of type `Void`.
We say it is uninhabited.

### `Not`

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
`a -> Void` is by showing `a` has no values.  We are saying, “if there
were a proof of `a`, it would mean something is very wrong.”

### `void`

The function `void` is of type `Void -> a` so can fill any impossible hole if
you have this (impossible) value. This works when places where Idris's unifier
cannot detect the contradiction. `Not` is defined as `Not x = x -> Void`, so
if you have a prfX and a prfNotX, the expression `void (prfNotX prfX)` can be
used to solve an impossible case:

```idris
foo : x -> Not x -> 6 = 42
foo p np = void (np p)
```

### `Uninhabited` and `absurd`

`Void` is not the only uninhabited type, just the simplest.  There are many
useful uninhabited types, for example `True = False` is uninhabited.  These
often come up in proofs, and we'll need a way to solve these impossible cases.

For this, Idris has the `Uninhabited` interface, defined like so:

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

