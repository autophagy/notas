===========================
Simple Algebraic Data Types
===========================

Notes
=====

The category of sets **Set** is a monoidal category - we can multiply its
objects. These are product types.

Product types in Haskell are n-tuples, like ``(a, b, c)``. The creation of a
product type can be viewed as a binary operation on types, where the
isomorphism:

.. math::

   ((a, b), c) \leftrightarrow (a, (b, c))

can look like the monoidal associative law:

.. math::

   ((a * b) * c) = (a * (b * c))

where the multiplication of type objects is their cartesian product.

A more general way of defining product types in Haskell is by using ``Pair``:

.. code-block::

   data Pair a b = Pair a b

   statement :: Pair String Int
   statement :: Pair "My favourite number is" 5

``()`` is the unit of the product, as ``(a, ())`` is isomorphic to ``a``:

.. code-block::

   toA :: (a, ()) -> a
   toA (x, _) = x

   fromA :: a -> ((), a)
   fromA x = ((), x)

Symbolically, :math:`a * 1 = a`.

The coproduct gives rise to sum types, such as ``Either a b``:

.. code-block::

   data Either a b = Left a | Right b

Where ``Either a b`` can have the value of ``a`` or ``b``, but never both. Like
pairs and tuples, sum types are also commutative and nesting order is irrelevant
up to isomorphism.

**Set** is also a monoidal category with respect to the coproduct, the sum type.
The initial object acts as the unit (``Void`` in this case). Adding ``Void``
to a sum type doesn't change its content, similar to zero in addition.
``Either a Void`` is isomorphic to ``a``, as there is no way to construct
a ``Right Void`` value. Symbolically, :math:`a + 0 = a`.

The product type of ``(a, Void)`` is isomorphic to ``Void``, as there is no
value to satisfy ``(a, Void)`` - the product type is **uninhabited**. Symbolically,
:math:`a * 0 = 0`.

The distributive property also holds:

.. math::

   a(b + c) = ab + ac

Translated into types, ``(a, Either b c)`` would correspond to ``Either (a, b) (a, c)``.

Two such intertwined monoids, product and sum types, form a **semiring**. We can't
define subtraction of types, so it does not form a **ring**.

Recursive types, such as ``List a``:

.. code-block::

   data List a = Nil | Cons a (List a)

form an equation. If we substitute ``List a`` for :math:`x` and ``Nil`` for
:math:`1` (as it can only have 1 value, it is essentially ``()``):

.. math::

   &x = 1 + ax

   &x = 1 + a(1 + ax) = 1 + a + aax

   &x = 1 + a(1 + a(1 + ax)) = 1 + a + aa + aaax

   &...

Resulting in an infinite list of products. A list can be empty (:math:`1`),
a singleton (:math:`a`), a pair (:math:`aa`), a tuple (:math:`aaa`), etc. Hence,
these are called **algebraic data types**.

A product type, ``(a, b)`` must have values for both ``a`` **and** ``b``. A sum
type, ``Either a b`` has a value for either ``a`` **or** ``b``. Logical and &
logical or also form a semiring. Mapped to types:

.. code-block::

   False  = Void
   True   = ()
   a || b = Either a b
   a && b = (a, b)
