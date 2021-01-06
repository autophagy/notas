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


Challenges
==========

6.1
---

**Show to isomorphism between Maybe a and Either () a.**

.. code-block:: haskell

   maybeToEither :: Maybe a -> Either () a
   maybeToEither (Just a) = Right a
   maybeToEither Nothing = Left ()

   eitherToMaybe :: Either () a  -> Maybe a
   eitherToMaybe (Left ()) = Nothing
   eitherToMaybe (Right a) = Just a

These functions are inverses of each other.

6.2
---

**Here's a a sum type defined in Haskell:**

.. code-block:: haskell

   data Shape = Circle Float
              | Rect Float Float

**When we want to define a function like area that acts on a Shape, we do it by
pattern matching on the two constructors:**

.. code-block:: haskell

   area :: Shape -> Float
   area (Circle r) = pi * r * r
   area (Rect d h) = d * h

**Implement Shape in C++ or Java as an interface and create two classes: Circle
and Rect. Implement area as a virtual function.**

.. code-block:: java

    interface Shape {
        public float area();
    }

    class Circle implements Shape {
        float r;

        public Circle(float r) {
          this.r = r;
        }

        public float area() {
            return (float) Math.PI * r * r;
        }
    }

    class Rect implements Shape {
        float d;
        float h;

        public Rect(float d, float h) {
            this.d = d;
            this.h = h;
        }

        public float area() {
            return d * h;
        }
    }

6.3
---

**Continuing with the previous example: We can easily add a new function circ
that calculates the circumference of a Shape. We can do it without touching the
definition of Shape:**

.. code-block:: haskell

    circ :: Shape -> Float
    circ (Circle r) = 2.0 * pi * r
    circ (Rect d h) = 2.0 * (d + h)

**Add circ to your C++ or Java implementation. What parts of the original code
did you have to touch?**

First, we have to add the function to the ``Shape`` interface:

.. code-block:: java

    interface Shape {
        public float area();
        public float circ();
    }

And then add the implementation of ``circ`` to each of the classes that
implement the ``Shape`` interface:

.. code-block:: java

    class Circle implements Shape {
        ...

        public float circ() {
            return 2.0F * (float) Math.PI * r;
        }
    }

    class Rect implements Shape {
        ...

        public float circ() {
            return 2.0F * (d + h);
        }
    }

6.4
---

**Continuing further: Add a new shape, Square, to Shape and make all the
necessary updates. What code did you have to touch in Haskell vs. C++ or Java?
(Even if you’re not a Haskell programmer, the modifications should be pretty
obvious.)**

For Java, one would just need to add a new class that implements ``Shape``:

.. code-block:: java

   class Square implements Shape {
      float w;

      public Square(float w) {
         this.w = w;
      }

      public float area() {
         return w * w;
      }

      public float circ() {
         return 4.0F * w;
      }
   }

In Haskell, however, one would need to update the ``Shape`` type, as well
as add a new pattern to ``area`` and ``circ``:

.. code-block:: haskell

   data Shape = Circle Float
              | Rect Float Float
              | Square Float

   area :: Shape -> Float
   ...
   area (Square w) = w * w

   circ :: Shape -> Float
   ...
   circ (Square w) = 4.0 * w

6.5
---

**Show that a + a = 2 * a holds for types (up to isomorphism). Remember that 2
corresponds to Bool, according to our translation table.**

Translated into types, :math:`a + a = 2 * a` would be ``Either a a -> (Bool, a)``.
If they hold for types up to isomorphism, we should be able to define a pair
of inverse functions:

.. code-block::

   sumToProduct :: Either a a -> (Bool, a)
   sumToProduct (Left a)  = (False, a)
   sumToProduct (Right a) = (True, a)

   productToSum :: (Bool, a) -> Either a a
   productToSum (True, a)  = Right a
   productToSum (False, a) = Left a
