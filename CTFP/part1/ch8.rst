=============
Functoriality
=============

Notes
=====

A **bifunctor** is a functor of two arguments that maps every pair of objects,
one from :math:`C` and one from :math:`D`, to an object in `E`.

.. image:: img/ch8-1.png
     :alt: A bifunctor from c in C & d in D to Fcd in E
     :align: center

This is just a mapping of a cartesian product :math:`C \times D \rightarrow E`.

However, bifunctors must map morphisms too. Morphisms in a cartesian product
of categories are pairs of morphisms, :math:`(f, g)`. We can compose these
pairs:

.. math::

   (f, g) \circ (f', g') = (f \circ f', g \circ g')

This composition is also associative and has an identity :math:`(id, id)`.
Categories where join functorality fails are *premonoidal*.

.. image:: img/ch8-2.png
      :alt: A bifunctor that maps the morphisms in C and D.
      :align: center

Products and coproducts, if they're defined for every pair of objects in a
category, are bifunctors. In Haskell, pairs ``(a, b)`` and ``Either a b`` are
both instances of ``Bifunctor``.

One of the requirements for a monoidal category is that the binary operator
associated with it is a bifunctor.

Sum and product types are functorial, as bifunctors. The basic building blocks
of algebraic data types are too: the ``Const ()`` functor (like ``Nothing`` in
``Maybe``) and the ``Identity`` functor (like ``Just a`` in ``Maybe``).
Everything else in algebraic data structures is composed from those 2 primitives
using products and sums.

With this in mind, we can define the ``Maybe`` up to isomorphism as a composition
of the bifunctor ``Either`` with the 2 functors ``Const ()`` and ``Identity``:

.. code-block::

   data Maybe a = Either (Const () a) (Identity a)

Consider 3 categories: :math:`C`, :math:`C^{op}` and :math:`D`, with a functor
between :math:`C^{op}` and :math:`D`:

.. math::

   F :: C^{op} \rightarrow D

This functor maps :math:`f^{op} :: a \rightarrow b` in :math:`C` to
:math:`Ff^{op} :: Fa \rightarrow Fb` in D.

Alongside this, we can define a mapping :math:`G`, which is not a functor,
from :math:`C` to :math:`D`. It maps objects, but reverses the morphisms it
maps. It takes :math:`f :: b \rightarrow a` in :math:`C`, maps it to its
opposite in :math:`C^{op} f^{op} :: a \rightarrow b`, and then uses the functor
:math:`F` to get :math:`Ff^{op} :: Fa \rightarrow Fb`.

Since :math:`Fa` and :math:`Ga` are the same, this can be described as:

.. math::

   Gf :: (b \rightarrow a) \rightarrow (Ga \rightarrow Gb)

A mapping of categories that *inverts* the morphism direction like this is a
**contravariant functor**.

Regular functors are **covariant functors**. A contravariant functor is a
covariant functor from the opposite category.

.. image:: img/ch8-3.png
      :alt: A covariant functor in Cop acting as a contravariant functor in C
      :align: center

``(->)`` is contravariant in its first argument and covariant in the second.
If the target category is **Set**, this is a **profunctor**. Contravariant
functors are equivalent to covariant functors in the opposite set, so a
profunctor is defined as:

.. math::

      C^{op} \times D \rightarrow Set

And looks like:

.. image:: img/ch8-4.png
   :alt: a profunctor targetting Set from C and D

The mapping that takes a pair of objects :math:`(a, b)` and assigns the pair
to the set of morphisms between them, :math:`Hom_{C}(a, b)`, is a functor from
:math:`C^{op} \times C \rightarrow Set`.

Challenges
==========

8.1
---

**Show that the data type:**

.. code-block:: haskell

   data Pair a b = Pair a b

**is a bifunctor. For additional credit implement all three methods of Bifunctor
and use equational reasoning to show that these definitions are compatible
with the default implementations whenever they can be applied.**

We can turn ``Pair`` into a functor by fixing the first argument type:

.. code-block:: haskell

   instance Functor (Pair a) where
      fmap f (Pair a b) = Pair a (f b)

We can prove this preserves identity through equational reasoning like in the
last chapter:

.. code-block:: haskell

     fmap id (Pair a b)
   = { def of fmap }
     Pair a (id b)
   = { def of id }
     Pair a b
   = { def of id }
     id (Pair a b)

Next, the composition preservation law:

.. code-block:: haskell

     fmap (g . f) (Pair a b)
   = { def of fmap }
     Pair a (g(f b))
   = { def of fmap }
     fmap g (Pair a (f b))
   = { def of fmap }
     fmap g (fmap f (Pair a b))
   = { composition }
     (fmap g . fmap f) (Pair a b)

So, ``Pair`` acts as a functor in the second argument type. We can also implement
``Pair`` as a functor by fixing the ``b`` type of ``Pair a b``, implementing
``fmap`` as ``fmap f (Pair a b) = Pair (f a) b`` and proving the identity and
composition laws the same way.

Implemented as a ``Bifunctor``:

.. code-block:: haskell

   instance Bifunctor Pair where
      bimap f g (Pair a b) = Pair (f a) (g b)
      first f (Pair a b)   = Pair (f a) b
      second g (Pair a b)  = Pair a (f b)

8.2
---

**Show the isomorphism between the standard definition of Maybe and this
desugaring:**

.. code-block:: haskell

   type Maybe' a = Either (Const () a) (Identity a)

**By defining two mappings between the two implementations.**

The default implementation of ``Maybe`` is:

.. code-block:: haskell

   type Maybe a = Nothing | Just a

And we can define the mappings between the two types as:

.. code-block:: haskell

   import Data.Functor.Const
   import Data.Functor.Identity

   type Maybe' a = Either (Const () a) (Identity a)

   a2b :: Maybe a -> Maybe' a
   a2b Nothing = Left (Const ())
   a2b (Just x) = Right (Identity x)

   b2a :: Maybe' a -> Maybe a
   b2a (Left (Const ())) = Nothing
   b2a (Right (Identity x)) = Just x

