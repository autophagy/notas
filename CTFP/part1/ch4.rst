==================
Kleisli Categories
==================

Notes
=====

A Kleisli category is a category based on a monad. It has, as its objects,
types. The morphism between the type A to type B, for example, is a function
that goes from type A to an embellished type derived from B. Morphism composition
as well as identity morphisms are defined in a particular way for each Kleisli
category.

Challenges
==========

**A function that is not defined for all possible values of its argument is
called a partial function. It’s not really a function in the mathematical
sense, so it doesn’t fit the standard categorical mold. It can, however, be
represented by a function that returns an embellished type optional:**

.. code-block:: cpp

    template<class A> class optional {
        bool _isValid;
        A    _value;
    public:
        optional()    : _isValid(false) {}
        optional(A v) : _isValid(true), _value(v) {}
        bool isValid() const { return _isValid; }
        A value() const { return _value; }
    };

**As an example, here’s the implementation of the embellished function safe_root:**

.. code-block:: cpp

    optional<double> safe_root(double x) {
        if (x >= 0) return optional<double>{sqrt(x)};
        else return optional<double>{};
    }

4.1
---

**Construct the Kleisli category for partial functoins (define composition and
identity).**

Composition
...........

.. code-block:: cpp

   template<class A, class B, class C>
   function<optional<C>(A)> compose(function<optional<B>(A)> m1,
                                    function<optional<C>(B)> m2)
   {
      return [m1, m2](A x) {
         auto p1 = m1(x);
         if (p1.isValid()) {
            return m2(p1.value());
         } else {
            return <optional><C>{};
         }
      };
   }

Identity
........

.. code-block:: cpp

   template<class A> optional<A> identity(A x) {
      return optional<a>{x};
   }

4.2
---

**Implement the embellished function safe_reciprocal that returns a valid
reciprocal of its argument, if it’s different from zero.**

.. code-block:: cpp

   optional<double> safe_reciprocal(double x) {
      return (x == 0) ? optional<double>{} : optional<double>{1/x};
   }

4.3
---

**Compose safe_root and safe_reciprocal to implement safe_root_reciprocal that
calculates sqrt(1/x) whenever possible.**

.. code-block:: cpp

   optional<double> safe_root_reciprocal(double x) {
      return compose<double, double, double>(safe_reciprocal, safe_root)(x);
   }
