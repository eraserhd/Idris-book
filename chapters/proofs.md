## Proofs

The Curry Howard correspondence is the relationship between computer programs
and mathematical proofs. Specifically:

1. *Types* can be interpreted as *logical claims*.
2. *Values* can be interpreted as *proofs of logical claims*.
3. *Evaluating code* can be seen as *making a proof simpler*.

This is tricky to think about, much like the optical illusion of a dancer,
where, if you concentrate really hard, you can reverse the direction you see
them spinning.

### Programs *are* Proofs

Let's take the Idris program:

```idris
total thinger : Nat
thinger = 1 + 2
```

(Ignore `total` for a moment.)

Its interpretation as a functional program is,

> "let’s define a name `thinger` which represents a `Nat` (a natural number,
> or integer \( \geq 0 \));

> further, let’s define `thinger` to be equivalent to the expression `1+2`."

We often say that `thinger` "has type" `Nat`.

Its logic interpretation is,

> “let’s propose that there is a natural number of some kind;

> further, let’s offer the expression `1+2` as a witness--a kind of
> proof--that this claim is true.”

A note on logic: In classical logic we can sometimes prove things without an
explicit witness, but we must abandon this ability to make our correspondence
work.  This restricted logic is called *constructivist* or *intuitionistic*.
Classically we can prove the statement, "Either England has a king, or it does
not," without knowing which part is true – or, in fact, anything about
England.  A constructivist proof of this statement requires we either present
a proof of England having a king, or a proof of England not having a king.

Idris rejects incorrect proofs (witnesses) primarily through _type checking_
and _totality checking_.  (Idris also performs other necessary checks that
won’t be covered here.)

Above, we claimed that `thinger` produces a `Nat`.  Idris looks up `+` and
sees that it has multiple versions for different kinds of numbers, but only
one can produce a `Nat`, and this one needs two different `Nat`s as input. It
then checks whether it can interpret `1` and `2` as `Nat`, which it can.  By
assigning types to every sub-expression and verifying that the types connect
correctly, Idris ensures the result of the expression will be a proof of the
claim.  This is type checking.

But not all correctly-typed programs work!  Some loop forever, going nowhere.
Some produce a forever-larger data structure and never stop to return
anything--in practice, crashing when memory runs out.  Some crash because they
fail to account for a particular case.  If any of these things could happen,
we can't trust our witness.  The keyword `total` tells Idris to ensure the
computation will always succeed. It could take eons to calculate, which is
fine--its value as a proof does not come from actually running it, but from
knowing we could always get an answer from it if we wanted.

### Evaluation

In the program above, we (or the compiler) could replace `1 + 2` with `3`,
making the proof simpler.  This is what we mean when we say that the
evaluation of programs corresponds to the simplification of proofs.

### A More Logical Example

Above, we showed how to interpret `Nat` as a claim.  In practice, when we are
intentionally using Curry-Howard to prove things, we'll use types which were
designed for the purpose.

Given two arguments, `=` produces a type that can only be witnessed if the
two arguments are equivalent.  Therefore, we can prove that `3 = 3`:

```idris
total threeEqualsItself : 3 = 3
threeEqualsItself = Refl
```

`Refl` means "reflection"; it is the value that witnesses that any value is
equivalent to itself.  Since `3=3` is a reflection, the proof is satisfied.

We can pass two arbitrary things to `=`, e.g. `True = 42`, but this type has
no values we can present.  It's valid, we just say that it's *uninhabited*.
Here's what happens if we try:

```idris
total badEquivalance : True = 42
badEquivalence = Refl
```

```
(input):1:50:When checking right hand side of badEquivalence with expected type
        True = 42

Type mismatch between
        x = x (Type of Refl)
and
        True = 42 (Expected type)
 
Specifically:
        Type mismatch between
                Bool
        and
                Integer
```

### Proving Programs

We now have the foundation to prove properties of programs.  Since programs
are proofs and proofs are programs, nothing stops us from putting code in our
proofs--or even making code the object of our proofs.

For example, when writing a function `parse` to turn some text into a value,
we can prove a relationship between our `parse` and `show`, the standard
library's function for turning a value into text:

```idris
total showAndParseAreSymmetric : (v : MyValueType) -> parse (show v) = v
```

This means, “turning any value of `MyValueType` into text and back yields a
value equivalent to the original.”

A proof of this property would rule out all sorts of bugs: precision problems
representing floating-point numbers, cases missed or coded incorrectly, or
ambiguous textual representations (distinct values having the same textual
representation).  A proof ensures that any value could be written and read
back without unexpectedly changing.

We get much more confidence from proving than from unit testing, since unit
testing requires us to have explicitly thought of all the cases.  We get a lot
more confidence from proving than from generative testing, since generative
testing over a large state space can fail to find super-rare corner cases that
proving forces us to consider.

It requires learning and more effort, but the result is confidence you can’t
get any other way.
