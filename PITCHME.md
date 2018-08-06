## Bound for Greatness

#### by J Haigh
#### @DebugSteven

---

#### What are Rank N Types?

Note:
We want to write functions with higher-rank types. All that means is that we want to write functions that take polymorphic functions as arguments.

The motivation behind Rank N types is that we have these parametrically polymorphic functions & after we apply them once to a value, the type variable in that function is now bound to the type of that value. We'll look at some examples, but the issue here is that if you want to use a parametrically polymorphic function on multiple values of different types inside a function you'll get an error without this extension for code that *should* work. The Rank N types extension gives us the `forall` keywork & it gives us the ability to defer the binding of type variables to the function call, rather than the at type signature. The `N` in Rank N types means that we can nest these `forall` & added functions contexts.

---

#### Bound by Default
- Concrete Types
- Constrained Polymorphism
- Parametric Polymorphism

Note:
In Haskell we have 3 different types of type variables. We have concrete type variables, which is what all functions reduce down to when they are fully evaluated, every value has a concrete type. They are the types that start with capital letters. For example, we have `String`, `Integer`, `Bool` & other types that you can create yourself. 

We also have constrained polymorphic and parametric polymorphic type variables. Constrained polymorphic type variables have 1 or more typeclasses associated with it. The concrete type that the type variable will be bound to is reduced to only the types that have instances for all the typeclasses that constrain the type variable.

---

#### Constrained Polymorphism

`(Num a, Ord a) => a`

could be 

`Word`, `Integer`, `Int`, `Float`, or `Double` 

Note: 
because those are the only types that have instances of both the `Num` and the `Ord` typeclass. You can confirm for yourself by asking ghci using `:info Num` and `:info Ord`.

---

#### Parametric Polymorphism

```haskell
id :: a -> a
```

Note:
Parametric polymorphic, also known as fully polymorphic, type variables can be any bound to any concrete type & are represented by lowercase letters like `a`. Parametricity means that the expected behavior of a function should be uniform across all types.

The types of our functions tell us what kinds of operators are legal to perform. If we have a concrete type, we can find out which functions take that type as an argument. For typeclasses, we can look to see what functions it defines & which types have instances of those typeclasses. Having more constrained types allows us write our own code from a larger set of functions. 

Parametrically polymorphic types can be any types & functions written for parametrically polymorphic types have to have uniform behavior across all the types that a type variable can be bound to. There are no functions that are attached to parametrically polymorphic type variables. This limits what we can do in our functions with those type variables. For example, the `id` function has the type `a -> a`. The only thing that function can do & work uniformly for all types is return the `a` that we passed to `id`. We don’t have any information about the type to do anything else. The idea that something is applicable for all types is called universal quantification.

---

#### ForAll Extension

+++

```haskell
length :: [a] -> Int
length = ...
```

Note:
Haskell actually has rank 1 types implicitly.
Check out the `length` function.
`a` here can be any type so we can rewrite this.

+++

```haskell
{-# LANGUAGE ExplicitForAll #-}

length :: forall a . [a] -> Int
length = ...
```

Note:
The explicit forall extension only gives us the `forall` keyword.
It doesn't work on anything more than rank 1 types so it doesn't really
do anything for us other than letting us explicit about our `forall`.

---

#### Once It's Bound, It's Bound

The example of trying to id a tuple of 2 different types 
You can rewrite the function to work with different types, but there has to be a better way.


---

#### Ranks

| Type Signature                              | Rank   |
| :-----------------------------------------: | :----: |
| `Int -> Int`                                | Rank 0 | 
| `forall a. a -> a`                          | Rank 1 |
| `Int -> (forall a. a -> a)`                 | Rank 1 |
| `(forall a. a -> a) -> Int`                 | Rank 2 |
| `((forall a. a->a) -> Int) -> Bool -> Bool` | Rank 3 |

---

#### Rank 2 Types

Note:
Haskell has a language extension for Rank 2 types that is deprecated.
GHC use to make a distinction between Rank 2 types and Rank N types.
Rank 2 is the highest rank where full bidirectional type inference is 
decidable. GHC never implemented the Rank 2 type inference algorithm though,
so Rank2Types is indistinguishable from RankNTypes.

---

#### Rank N Types

---

#### Examples of RankNTypes
