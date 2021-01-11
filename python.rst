.. highlight:: python
    :linenothreshold: 2
.. default-domain: py

.. _python:

++++++
Python
++++++

Absolute and Relative Imports
=============================

Say, we have the following project layout:

.. highlight:: none

::

    .
    ├── foo
    │ ├── __init__.py
    │ ├── mod1.py
    │ └── mod2.py
    └── main.py

.. highlight:: python

``mod1.py`` contains:

::

    import mod2

``main.py`` contains:

::

    from foo import mod1

If you try to do

.. code-block:: text

   $ python3 main.py

from the project root, the ``ModuleNotFoundError: No module named 'mod2'``
exception will be raised.

That's because in Python 3 imports are **absolute**. Or, as `PEP 328`_
states, modules are searched in ``sys.path`` only.

The current directory is added to ``sys.path`` automatically, thus the
``foo`` package is reachable from ``main.py``. However, as soon as Python
tries to import ``mod2`` from inside ``mod1`` it fails because there is no
such module in the project root which is the current directory.

The fact that the current directory is added to ``sys.path`` automatically, can
be a source of subtle bugs. Imagine, for example, that you have developed the
``foo`` package separately. Things like ``python3 mod1.py`` worked perfectly
when you run them from inside ``foo``. Now you try to use the ``foo`` package
in another project and, yes, ``ModuleNotFoundError``.

There are several ways to fix the issue:

1. Fully qualify the import statement inside ``mod1.py``:

   ::

      import foo.mod2

   or to

   ::

       from foo import mod2

   Both work because ``foo`` is in ``sys.path``.

2. Use the relative import in ``mod1.py``:

   ::

      from . import mod2

3. Explicitly add the ``foo`` directory to ``PYTHONPATH`` before run:

   .. code-block:: text

      $ PYTHONPATH=foo python3 main.py

.. warning:: For a relative import it's tempting to do ``import .mod2`` but it's
   an illegal syntax!

In Python 2 imports, are relative by default, so the ``python2 main.py``
command finishes successfully. Again, this is because in Python 2 the
``import mod2`` statement equals to ``from . import mod2``. The Python 3
behavior in Python 2 can be enabled with the ``from __future__ import
absolute_import`` statement at the beginning of a module.

Summing up, the absolute import approach makes it virtually possible to forget
about everything but ``sys.path`` when dealing with imports. So, when you see
something like

::

  import some_module

you know exactly that ``some_module`` should be in ``sys.path``.

.. _PEP 328: https://www.python.org/dev/peps/pep-0328/

Threading
=========

.. _dummy-threads:

Dummy Threads
-------------

.. currentmodule:: threading

Dummy threads, which are instances of the :class:`threading._DummyThread`
class, are used for representing threads not created by the means of the
:mod:`threading` module.

If you call the :func:`~current_thread` in such thread, it will
return a dummy thread object. After the call, the thread will be added to the
internal dictionary of active threads and functions such as
:func:`~threading.enumerate` and :func:`~active_count` will become aware of it.

A short example to illustrate said above:

::

    import threading
    import time
    from _thread import start_new_thread


    def func():
        threading.current_thread()
        return 42


    if __name__ == '__main__':
        start_new_thread(func, ())
        time.sleep(0.5)  # give the dummy thread some time to finish
        print(threading.active_count())
        print(threading.enumerate())

The example prints the following:

.. highlight:: none

::

   2
   [<_MainThread(MainThread, started 4545691072)>, <_DummyThread(Dummy-1, started daemon 123145384022016)>]

Note, that we had to add :func:`time.sleep` to give the dummy thread some
time so that it finished before :func:`~active_count`
and :func:`~threading.enumerate` calls.  This shows another important
property of dummy threads -- they lack many facilities thread created with the
:mod:`threading` module have, e.g. we can't join them.
