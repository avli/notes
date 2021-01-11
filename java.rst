.. highlight:: java
   :linenothreshold: 2

.. _java:

++++
Java
++++

Generics
========

.. _unchecked-casts:

Unchecked Casts
---------------

Say, we have a list of strings:

::

   List<String> ls = Arrays.asList("One", "Two", "Three");

Since ``List<Object>`` is not a superclass of ``List<String>`` in Java, it
is illegal to do things like this:

::

   List<Object> lo = ls; // compile-time error!

At the same time this is legal:

::

   List<Object> lo = (List<Object>)(List<?>)ls; // uchecked cast, but compiles

Why it is so? Well... Since ``List<?>`` is a supertype of all parametrized
``List`` types it is allowed to downcast ``List<String>`` to it. It is also
allowed to upcast an instance of ``List<?>`` to any of its subtypes, but
this is a potentially dangerous procedure and the compiler yields a warning.

Wildcards vs Raw Types
----------------------

What's the difference between ``Collection<?>`` and ``Collection``? After
type erasure both of them will be just collection of objects (**not**
``Collection<Object>``!) But the compiler considers ``Collection<?>`` to be a
collection of objects of the *same type*, while ``Collection`` can contains
objects of different types. This means that the first option is a bit more
type-safe.

Another difference is that ``Collection<?>`` :ref:`is a supertype
<unchecked-casts>`
for
all
parametrized ``Collection`` types.

Exploring Generics with ``-XD-printflat``
-----------------------------------------

There is a neat hidden option `-XD-printflat` of the `javac` compiler, which
allows you to generate the interim representation of parametrized classes.
Applying this option shows you how you generic clases look after the type
erasure. For example

::

   public class Box<T> {
    private T myObj;

    public Box(T obj) {
        myObj = obj;
    }

    public T get() {
        return myObj;
    }
   }

been compiled with the command:

.. code-block:: text

   $ javac -XD-printflat -d output_dir Box.java

generates ``output_dir/Box.java`` file with the following content:

::

   public class Box {
    private Object myObj;

    public Box(Object obj) {
        super();
        myObj = obj;
    }

    public Object get() {
        return myObj;
    }
   }

That's super useful when you need to understand how generics work or
debugging some type erasure related issue.