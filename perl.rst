.. highlight:: perl
    :linenothreshold: 2

.. _perl:

++++
Perl
++++

Sigils
======

The Perl sigils can be confusing sometimes. Consider the following example:

::

  my @arr = (1, 2, 3, 'foo');
  my $x = $arr[2];
  print "$x\n";

It's not hard to understand that the code above prints "3". What feels
strange to me is the line ``my $x = $arr[2]``. Perl lists can contain only
*scalar* values. So assigning an element extracted from a list to a scalar
(``$x`` in our case) makes perfect sense. But why it's ``$arr[2]`` and not
``@arr[2]``? It's an array we're working with in the first place, isn't it?
Well... Let's give it a try:

.. _ex:

::

  my @arr = (1, 2, 3, 'foo');
  my $x = @arr[2];  # <-- @ instead of $
  print "$x\n";

Perl complains that ``Scalar value @arr[2] better written as $arr[2] at foo.pl
line 2``, but in the end prints the expected "3".

The answer to why Perl complains and why ``@arr[2]`` gives the same result can
be found in Perldoc_. It turns out that ``@arr[2]`` actually means **slice**
of length 1. In other words, the ``@arr[2]`` approximately equals to
``($arr[2])``. Next, the slice is assigned to the scalar variable ``$x``.
Recall that an array in the scalar context means its length, f.e.

::

  my @xs = (1, 2, 3, 4, 5);
  my $z = @xs;
  print "$z\n";

prints "5" which is the length of the array ``@xs``.

You may expect that it's also true for slices, but it isn't. ``print "$x\n";``
in the :ref:`example <ex>` outputs "3", not "1" which is the length of the
``@arr[2]`` slice!

Let's get back to the original example and modify it a little:

::

  my @arr = (1, 2, 3, 'foo');
  my $x = @arr[2, 3];
  print "$x\n";

It prints "foo" - *not* the length of the slice as we have already seen. That's
because, according to Perldoc_:

    "Slices in scalar context return the last item of the slice."

And that is why ``$x`` equals ``3`` in the :ref:`second example <ex>` - it's
not the length but the last element of a single element slice.

Summing up:

1. To access the first list element, the ``$arr[0]`` syntax should be used.
2. The ``@arr[0]`` syntax is used for slices.
3. The two forms above assigned to a scalar give visually the same result,
   but they actually differ since the first returns the first element
   and the second returns the last element of a single element slice.
4. Arrays and slices meaning in the scalar context is different.

Now, what about hashes?

Here is an example of getting the value by the key:

::

  my %h = ('a' => 42, 'b' => 100);
  my $y = $h{'a'};
  print "$y\n";  # prints 42

Note the syntax ``$h{'a'}`` which resembles the one for arrays.

What will happen if we change the sigil ``$`` to ``%`` in ``$h{'a'}``?

::

  my %h = ('a' => 42, 'b' => 100);
  my $y = %h{'a'};  # <-- % instead of $
  print "$y\n";

Perl complains that ``%h{"a"} in scalar context better written as $h{"a"} at
foo.pl line 2.`` and prints "42".

Let's go straight to Perldoc_ and check what the ``$h{'a'}`` syntax means.
This thing is called "key/value hash slice". In the example above ``$h{'a'}``
returns a slice that contains the key (``'a'``) and the value (``42``). In
other words – ``('a', 42)``. This slice is assigned to the scalar
variable ``$y``. According to the rules of interpretation of slices in the
scalar context, "42" (the last element of the slice) is assigned to ``$y``.

.. _Perldoc: https://perldoc.perl.org/perldata#Slices

.. seealso:: `The Problem With Sigils <https://wiki.c2.com/?TheProblemWithSigils>`_
