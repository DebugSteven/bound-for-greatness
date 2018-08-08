## Bound for Greatness

#### by J Haigh
#### @DebugSteven

---

#### What are Rank N Types?

Note:
We want to write functions with higher-rank types. All that means is that we want to write functions that take polymorphic functions as arguments.

The motivation behind Rank N types is that we have these parametrically polymorphic functions & after we apply them once to a value, the type variable is now bound to the type of that value. We'll look at some examples, but the issue here is that if you want to use a parametrically polymorphic function on multiple values of different types we'll get an error without the RankNTypes extension. The RankNTypes extension gives us the `forall` keyword & it gives us the ability to defer the binding of type variables to the function call, rather than the at type signature. The `N` in Rank N types means that we can nest these `forall` & added function contexts.

Rank N types are cool because they give us a better understanding of type variables, but they also allow us to write functions that work & also get them to type check. RankNTytpes are the most elegant way to solve some problems. We see this most notably in the lens library & with runST.

---

#### Bound by Default
- Concrete Types
- Constrained Polymorphism
- Parametric Polymorphism

Note:
Before we jump into RankNTypes, we need understand polymorphism.
In Haskell we have 3 different types of types. We have concrete types, which is what all functions reduce down to when they are fully evaluated.We also have constrained polymorphic and parametric polymorphic type variables. 

---
#### Concrete Types

```haskell
intId :: Integer -> Integer
intId x = x

doubleId :: Double -> Double
doubleId x = x
```

Note:
Every value has a concrete type. Concrete types start with capital letters. For example, we have `String`, `Integer`, `Bool` & other types that you can create yourself. 
The variable `x` in `intId` has the type `Integer` & in the function `doubleId`, `x` has the type `Double`.

---

#### Constrained Polymorphism

`(Num a, Ord a) => a`

could be 

`Word`, `Integer`, `Int`, `Float`, or `Double` 

Note: 
Constrained polymorphic type variables have 1 or more typeclasses associated with it. The concrete type that the type variable will be bound to is reduced to only the set of types that have instances for all the typeclasses that constrain the type variable.
So in our example above, a can only be `Word`, `Integer`, `Int`, `Float`, or `Double` 
because those are the only types that have instances of both the `Num` and the `Ord` typeclass. You can confirm for yourself by asking ghci using `:info Num` and `:info Ord`.


---

#### Parametric Polymorphism

```haskell
id :: a -> a
id x = x
```

Note:
Parametric polymorphic, also known as fully polymorphic, type variables can be any bound to any concrete type & are represented by lowercase letters like `a`. Parametricity means that the expected behavior of a function should be uniform across all types.
The idea that type variable is applicable for all types is called universal quantification.

---

#### ForAll Extension

+++

```haskell
length :: [a] -> Int
length = ...
```

Note:
Haskell has Rank 1 types implicitly.
Check out the `length` function.
`a` here can be any type so we can rewrite this.

+++

```haskell
{-# LANGUAGE ExplicitForAll #-}

length :: forall a . [a] -> Int
length = ...
```

Note:
The explicit forall extension gives us the `forall` keyword.
It doesn't work on anything more than Rank 1 types so it doesn't really
do anything for us other than letting us explicit about our `forall`.

---

#### Ranks

<div style='font-size: 16px !important'>
| Type Signature                              | Rank   |
| ------------------------------------------- | :----: |
| `Int -> Int`                                | Rank 0 | 
| `forall a. a -> a`                          | Rank 1 |
| `Int -> (forall a. a -> a)`                 | Rank 1 |
| `(forall a. a -> a) -> Int`                 | Rank 2 |
| `((forall a. a->a) -> Int) -> Bool -> Bool` | Rank 3 |
</div>

Note:
`Int -> Int` is Rank 0 because there is no quantifier.
The other ranks are determined by how many levels the quantifier (the `forall` keyword) is nested in.
`Int -> (forall a. a -> a)` is rank 1 because `forall a. a -> a` is rank 1 & this example has the `forall` on the right side of the function arrow. The quantifier isn't nested. `(forall a. a -> a) -> Int` is rank 2 because the `forall` is nested 1 layer more because it's on the left side of the `-> Int` in necessary parentheses. This pattern continues.

---
#### Rank 2 Types

> Language users only have so much time to spend on learning a language and
on understanding compiler error messages. Compiler implementors have a finite
time budget to spend on implementing language features, or improving type error
messages. - ["Practical type inference for arbitrary-rank types"](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/putting.pdf)

Note:
Haskell has a language extension for Rank 2 types that is deprecated.
Rank 2 is the highest rank where full bidirectional type inference is 
decidable. GHC doesn't implement the Rank 2 type inference algorithm though,
so Rank2Types is indistinguishable in practice from RankNTypes.


> Why was it never implemented & why do we still have the extension then?


I didn't find a definite answer for this question, but my guess was, based on the quote below & talking with Kenny Foner & Gabriel Gonzalez,
that needing to understand the decidability of type inference for Rank N types & adding a type annotation
where it's necessary would result in strange errors for Haskell users. Rank2Types is a synonym for RankNTypes right now.
The extension was likely added & left in in case someone wanted to go back & add the algorithm for Rank 2 type inference.
Currently though, there is no type inference implemented for Haskell Rank N types.


---

#### Scope It Out

```haskell
id' :: a -> a
id' a = a

tup :: (a -> a) -> (Char, Int)
tup f = (f 'a', f 1)
```

+++

```
Prelude> :r
[1 of 1] Compiling Main             ( tuple.hs, interpreted )

tuple.hs:5:12: error:
    • Couldn't match expected type ‘a’ with actual type ‘Char’
      ‘a’ is a rigid type variable bound by
        the type signature for:
          tup :: forall a. (a -> a) -> (Char, Int)
        at tuple.hs:4:1-30
    • In the first argument of ‘f’, namely ‘'a'’
      In the expression: f 'a'
      In the expression: (f 'a', f 1)
    • Relevant bindings include
        f :: a -> a (bound at tuple.hs:5:5)
        tup :: (a -> a) -> (Char, Int) (bound at tuple.hs:5:1)
  |
5 | tup f = (f 'a', f 1)
  |            ^^^
Failed, no modules loaded.
```

Note:
A concrete type cannot equal a polymorphic type variable in the same scope.

---

#### We Could Have Different Functions I Guess...

```haskell
idChar :: Char -> Char
idChar c = c

idInt :: Int -> Int
idInt i = i

tup' :: (Char -> Char) -> (Int -> Int) -> (Char, Int)
tup' f1 f2 = (f1 'a', f2 1)
```

Note:
Yeah so this compiles. But we pass `id` in for both arguments.
That's redundant & unnecessary.

---

#### Defer Binding Instead

```haskell
{-#LANGUAGE RankNTypes #-}

tup :: (forall a. a -> a) -> (Char, Int)
tup f = (f 'a', f 1)
```

Note:
By adding the `forall` keyword, we tell Haskell that we want to defer
the binding of the type variable `a` to the function application level.

---
