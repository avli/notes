.. _regexes:

+++++++
Regexes
+++++++

The following example:

.. code-block:: perl
   :linenos:

   my $s = "foo";
   $s =~ s/.*/!/g;
   print "$s\n";

prints ``!!``. Shouldn't it be just ``!``?

Let's apply the regex from the example step by step to see why it
matches twice. ``|`` shows our current position in the string.

1. At the beginning we are at position 0 - ``|foo``.
2. Since by default ``.`` matches any symbol except the newline, it
   subsequently matches the ``f``, ``fo``, and ``foo`` substrings.
3. ``*`` is a greedy quantifier. It means it consumes as many symbols as
   possible. So it picks the longest match which is ``foo``. Now we are at
   position 3 – ``foo|``. Are we done? Not quite.
4. The ``/g`` modifier asks a regex to match within a string as many times as
   possible. Right now we are at position 3. It means that we can try to
   match starting this position to the end of the line. It is the empty string.
   ``.*`` definitely matches it. That's why two matches are observed.
