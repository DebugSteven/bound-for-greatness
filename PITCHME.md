## Bound for Greatness

#### by J Haigh
#### @DebugSteven

---

#### What are Rank N Types?
Note:
This is where we're going to define it & motivate why we would want to use them as well as why we at least learning about them is useful.

---

#### Bound by Default
- Concrete Types
- Constrained Polymorphism
- Parametric Polymorphism

Note:
In Haskell we have 3 different types of type variables. We have concrete type variables, which is what all functions reduce down to when they are fully evaluated, every value has a concrete type. They are the types that start with capital letters. For example, we have `String`, `Integer`, `Bool` & other types that you can create yourself. 

We also have constrained polymorphic and parametric polymorphic type variables. Constrained polymorphic type variables have 1 or more typeclasses associated with it. The concrete type that the type variable will be bound to is reduced to only the types that have instances for all the typeclasses that constrain the type variable.

do an example

---

#### Constrained Polymorphism

`(Num a, Ord a) => a`

could be 

`Word`, `Integer`, `Int`, `Float`, or `Double` 

Note: 
because those are the only types that have instances of both the `Num` and the `Ord` typeclass. You can confirm for yourself by asking ghci using `:info Num` and `:info Ord`.

---

#### Parametric Polymorphism

Note:
Parametric polymorphic, also known as fully polymorphic, type variables can be any bound to any concrete type & are represented by lowercase letters like `a`. Parametricity means that the expected behavior of a function should be uniform across all types.

The types of our functions tell us what kinds of operators are legal to perform. If we have a concrete type, we can find out which functions take that type as an argument. For typeclasses, we can look to see what functions it defines and which types have instances of those typeclasses. Having more constrained types allows us write our own code from a larger set of functions. 

Parametrically polymorphic types can be any types & functions written for parametrically polymorphic types have to have uniform behavior across all the types that a type variable can be bound to. There are no functions that are attached to parametrically polymorphic type variables. This limits what we can do in our functions with those type variables. For example, the `id` function has the type `a -> a`. The only thing that function can do & work uniformly for all types is return the `a` that we passed to `id`. We don’t have any information about the type to do anything else. The idea that something is applicable for all types is called universal quantification.

---

#### ForAll Extension

+++

```haskell
length :: Foldable t => t a -> Int
length = ...
```

+++

```haskell
{-# LANGUAGE ExplicitForAll #-}

length :: forall a . a -> Int
length = ...
```

---

#### Rank 2 Types

---

#### Rank N Types

---

#### Examples of RankNTypes
