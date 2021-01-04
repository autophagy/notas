=======================
Products and Coproducts
=======================

Notes
=====

An **initial object** in a category is an object that has 1 and only 1 morphism
going to any object in the category. Object a is more initial than b if
:math:`a \rightarrow b`.

In the category of sets, this is the ``Void`` Haskell type with ``absurd :: Void -> a``
as the universally polymorphic morphism.

A **terminal object** a category is an object that has 1 and only 1 morphism
going to it from any object in the category. Object a is more terminal than b
if :math:`a \leftarrow b`.

In the category of sets, this is a singleton set: ``()`` in Haskell, with
``unit :: a -> ()`` as the morphism.

For any category :math:`C`, we can define the opposite category :math:`C^{op}`
by reversing the morphisms. If :math:`f :: a \rightarrow b`, :math:`g :: b \rightarrow c`
with their composition :math:`g \circ f = h :: a \rightarrow c`, then we can
define :math:`f^{op} :: a \leftarrow b`, :math:`g^{op} :: b \leftarrow c` and
:math:`h^{op} :: a \leftarrow c`. The terminal object in :math:`C` is the initial
:math:`C^{op}`, and vice versa.

An **isomorphism** is a pair of morphisms such that :math:`f \circ g = id` and
:math:`g \circ f = id`.

Initial objects are unique up to unique isomorphisms. Take 2 initial objects,
:math:`i_1` and :math:`i_2`, with morphisms :math:`f :: i_1 \rightarrow i_2` and
:math:`g :: i_2 \rightarrow i_1`. :math:`g \circ f` is a morphism from
:math:`i_1 \rightarrow i_1`.
Since :math:`i_1` is initial, there can be only 1 morphism from it, so
:math:`g \circ f = id_{i_1}`. Similarly, :math:`f \circ g = id_{i_2}`. :math:`f`
and :math:`g` must be the inverse of each other. Therefore, **any 2 initial
objects are isomorphic**.

Products
--------

A product of two objects :math:`a` and :math:`b` is the object :math:`c` equipped
with two **projections** (:math:`p :: c \rightarrow a` and :math:`q :: c \rightarrow b`)
such that for any other object :math:`c'` equipped with two projections there is
a *unique morphism* :math:`m :: c' \rightarrow c` that factorizes those projections.

For example, consider the product between the types ``Int`` and ``Bool``. There
could be two candidates for the product of those two types, ``Int`` and ``(Int, Bool)``:

.. image:: img/ch5-1.png
    :alt: 2 candidates for the product of Int and Bool: Int and (Int, Bool)
    :align: center

In this case, :math:`p'` and :math:`q'` could be constructed in terms of
:math:`m`:

.. code-block:: haskell

   m :: Int -> (Int, Bool)
   m x = (x, True)

   p :: (Int, Bool) -> Int
   p (x, _) = x

   q :: (Int, Bool) -> Bool
   q (_, x) = x

   p' :: Int -> Int
   p' x = p (m x)     -- this would equal x

   q' :: Int -> Bool
   q' x = q (m x)     -- this would equal True


Coproducts
----------

A **coproduct** of two object :math:`a` and :math:`b` is the object :math:`c`
equipped with two **injections** (:math:`i :: a \rightarrow c` and
:math:`j :: b \rightarrow c`) such that for any other object :math:`c'` equipped
with two injections there is a *unique morphism* :math:`m :: c \rightarrow c'`
that factorizes those injections:

.. image:: img/ch5-2.png
    :alt: A coproduct of a and b
    :align: center

In the category of sets, the coproduct is the *disjoint union* of two sets.
In programming, a coproduct can be a tagged union of 2 types - cannonically
``Either`` in Haskell:

.. code-block:: haskell

   data Either a b = Left a | Right b


Asymmetries
-----------

The terminal object can be obtained from reversing the morphism directions
of an initial object. A coproduct can be obtained from reversing the morphisms
of a product. However, asymmetries exist in the category of sets. Take a
function from a singleton set:

.. code-block:: haskell

   f100 :: () -> Integer
   f100 () = 100

Functions are *total*, in that they are defined for every element of the
domain set. However, functions don't have to cover the whole codomain. ``f100``
is a total function in that is covers every value of ``()``, but only covers one
value of the codomain: ``100`` in the set of integers. Functions that have a
smaller domain than the codomain *embed* the domain in the codomain. Functions
that tightly fill their codomains, on the other hand, are **surjective** or **onto**.

Functions can also map many elements of the domain onto one element of the
codomain:

.. code-block:: haskell

   intToTrue:: Integer -> Bool
   intToTrue _ = True

In this sense, they *collapse* the domain into the codomain. Functions that
do not do this are **injective** or **one-to-one**.

Some functions are *both surjective and injective*. They are **bijections**,
and are properly invertable. An ismorphism in the category of sets is a bijection.

Challenges
==========

5.1
---

**Show that the terminal object is unique up to unique isomorphism.**

We can basically just reverse the proof for the initial object.

Take 2 terminal objects, :math:`t_1` and :math:`t_2`, with morphisms
:math:`f :: t_2 \rightarrow t_1` and :math:`g :: t_1 \rightarrow t_2`.
:math:`g \circ f` is a morphism from :math:`t_2 \rightarrow t_2`.
Since :math:`t_2` is terminal, there can be only 1 morphism to it from any object,
and objects in a category have the identity morphism already, so
:math:`g \circ f = id_{t_2}`. Similarly, :math:`f \circ g = id_{t_1}`. :math:`f`
and :math:`g` must be the inverse of each other. Therefore, any 2 terminal
objects are isomorphic. The terminal object is unique up to unique isomorphism.

5.2
---

**What is a product of two objects in a poset?**

To recap: a poset, or partially ordered set, is a category where a morphism
is a relation between objects: for example, the relation of being less than
or equal. It has:

- *Identity morphisms*: an object is less than or equal to itself.
- *Composition*: :math:`a \leq b \land b \leq c \rightarrow a \leq c`.
- *Partial order*: additional condition that :math:`a \leq b \land b \leq a \rightarrow a = b`.

However, a partial order is not a total/linear order, in which every object
has a morphism to every other.

If we have 2 objects, :math:`a` and :math:`b`, we seek a :math:`c` that is the
product of those objects, with morphisms :math:`p :: c \rightarrow a` and
:math:`q :: c \rightarrow b`.

Given that the morphisms in this posset are the relation of being less than
or equal, the morphisms from :math:`c` are :math:`p :: c <= a` and
:math:`q :: c <= b`.

The product :math:`c` must be a number that is less than or equal to a or b. It must also
be the *biggest* number less than or equal to a and b, as if were not, then the morphism
:math:`m :: c' <= c` would factorize :math:`p'` and :math:`q'`:

.. code-block:: haskell
   m :: c' <= c

   p :: c <= a
   q :: c <= b

   p' :: c' <= a
   q' :: c' <= b

   p' :: m.p -> c' <= c && c <= a == c' <= a
   q' :: m.p -> c' <= c && c <= b == c' <= b

So, for example, if :math:`a = 5` and :math:`b = 6`, then :math:`c \leq 5 \land c \leq 6`.
In this case, :math:`c = 5`, as :math:`5 \leq 5 \land 5 \leq 6`. If :math:`c' = 4`, then
:math:`m :: c' <= c`.

5.3
---

**What is a coproduct of two objects in a poset?**

A coproduct :math:`c` of :math:`a` and :math:`b` in the poset defined in 5.2
would have the morphisms :math:`i :: a \leq c` and :math:`j :: b \leq c`. It
should also be the object where :math:`m :: c \leq c'`. In the example poset, it
would be the *smallest* number less than or equal to both a and b.

If :math:`a = 5` and :math:`b = 6`, then the coproduct :math:`a \leq c \land b \leq c`.
This would be :math:`c = 6`. If :math:`c' = 7`, then :math:`m :: c <= c'`.

5.4
---

**Implement the equivalent of Haskell Either as a generic type in your favorite
language (other than Haskell).**

.. code-block:: python

    from typing import Generic, TypeVar

    T = TypeVar("T")
    U = TypeVar("U")


    class Either(Generic[T, U]):
        pass


    class Left(Either[T, U]):
        def __init__(self, value: T) -> None:
            self.value = value


    class Right(Either[T, U]):
        def __init__(self, value: U) -> None:
            self.value = value


    left = Left[str, int]("hello")
    right = Right[str, int](10)

    assert type(left.value) == str
    assert type(right.value) == int

    assert left.value == "hello"
    assert right.value == 10

5.5
---

**Show that Either is a “better” coproduct than int equipped with two
injections:**

.. code-block:: cpp

     int i(int n) { return n; }
     int j(bool b) { return b? 0: 1; }

**Hint: Define a function int m(Either const & e); that factorizes i and j.**

In this instance, ``m :: Either int bool -> int``

.. code-block:: cpp

   int m(Either const & e) {
      return (e.tag == Either::isLeft) ? e.left : e.right ? 0 : 1;
   }

