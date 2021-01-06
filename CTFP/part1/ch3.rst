==========================
Categories Great and Small
==========================

Notes
=====

To create a *free category* from any directed graph, add more arrows.
Specifically, add an identity arrow at each node. Then, for any 2 arrows where
the end of one coincides with the start of another, add their composition.
Remember to also include the composition of this new arrow with other arrows.

A thin category is one where there is *at most* one morphism from any object
:math:`a` to any object :math:`b`. The set of morphisms from :math:`a` to
:math:`b` in category :math:`C` is the **hom-set** :math:`Hom_{C}(a,b)`. Every
hom-set in a thin category is either empty or a singleton set. :math:`Hom_{C}(a,a)`
is a singleton set containing :math:`id_{a}`.

**Monoids** are sets with an associative binary operation and a value that
behaves as a unit with respect to that operation. For example, natural numbers
form a monoid under addition, where zero acts as the unit:

.. math::

    a + 0 = a
    0 + a = a

A monoid is a single object category with a set of morphisms that follow
the rules of composition. Elements of a hom-set can be seen as both compositional
morphisms and as points in a set. Composition of morphisms in :math:`M` are
monoidal products in the set :math:`M(m,m)`.


Challenges
==========

3.1
---

**Generate a free category from**:

1. **A graph with one node and no edges.**
2. **A graph with one node and one (directed) edge.**
3. **A graph with two nodes and a single arrow between them.**
4. **A graph with a single node and 26 arrows marked with the letters of
   the alphabet: a, b, c, ... z.**

1. Add an identity arrow from the node to itself.
2. Add the identity edge. Then add composition of the existing edge with itself.
   Then add the composition of the existing edge with this new one, going until
   infinity.
3. Add the identity edges from each node to itself.
4. Add the identity arrow, then add the arrows corresponding to the composition
   of each letter to any other letter.  Then add the composition of those, plus
   the existing edges, going until infinity.

3.2
---

**What kind of order is this?**

1. **A set of sets with the inclusion relation: A is included in B if every
   element of A is also an element of B.**
2. **C++ types with the following relation: T1 is a subtype of T2 if a pointer
   to T1 can be passed to a function that expects a pointer to T2 without
   triggering a compilation error.**

1. The objects of our category are sets, and the morphisms between the objects
   is the subset relationship ( :math:`\subset` ). The hom-set :math:`S(a,b)`
   will only ever have, at most, one morphism corresponding to :math:`a \subset b`,
   so it is at least a preorder. If :math:`a \subset b` and :math:`b \subset a`,
   then :math:`a = b`, so it is at least a partial order. It is not a total order,
   however, as there exists sets that are not subsets of each other (like ``[1, 2, 3]``
   and ``[4, 5, 6]``). Therefore, this is a **partial order**.

2. The objects of our category are types, and the morphisms between the objects
   is the subtype relationship. The hom-set :math:`T(T1,T2)` can only ever have
   at most one morphism corresponding to the subtype relationship, so it is at
   least a preorder. If ``T1`` is a subtype of ``T2``, and ``T2`` is a subtype
   of ``T1``, then they must be the same type, so it is at least a partial order.
   It isn't a total, as there exists types which are not subtypes of each other.
   So this is a **partial order**.

3.3
---

**Considering that Bool is a set of two values True and False, show that it
forms two (set-theoretical) monoids with respect to, respectively, operator
&& (AND) and || (OR).**

For ``&& (AND)``, the binary operator is associative:

.. math::

    a \land (b \land c) = (a \land b) \land c = a \land b \land c

The value that behaves like unit for ``&&`` is ``True``:

.. math::

    a \land True = a; True \land a = a

If ``a`` is true, then :math:`True \land True = True`. If false, then
:math:`False \land True = False`.

For ``|| (OR)``, the binary operator is associative:

.. math::

    a \lor (b \lor c) = (a \lor b) \lor c = a \lor b \lor c

The value that behaves like unit for ``||`` is ``False``:

.. math::

    a \lor False = a; False \lor a = a

If ``a`` is true, then :math:`True \lor False = True`. If false, then
:math:`False \lor False = False`.

3.4
---

**Represent the Bool monoid with the AND operator as a category. List the
morphisms and their rules of composition.**

Since it is a monoidal category, there is only one object - the ``Bool`` type.
The morphisms are:

- ``&& True``, which is the identity morphism.
- ``&& False``.

Composition of these morphisms follows the identity rule, where
:math:`id \circ f = f`. The only valid composition resolves to ``&& False``.

3.5
---

**Represent addition modulo 3 as a monoid category**.

There are only 3 possible morphisms in the hom-set of this monoidal category,
corresponding to:

.. code-block:: haskell

    (+ 0) `mod` 3
    (+ 1) `mod` 3
    (+ 2) `mod` 3

Similarly for addition, ``(+ 0) `mod` 3`` is the identity morphism.
