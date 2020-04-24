.. highlight:: python
    :linenothreshold: 2
.. default-domain: py

.. _python:

++++++
Python
++++++

Threading
=========

.. _dummy-threads:

Dummy Threads
-------------

.. currentmodule:: threading

Dummy threads, which are instances of the :class:`_DummyThread`
class, are used for representing threads not created by the
:mod:`threading` module.

If you call the :func:`~current_thread` in such thread it will
return a dummy thread object. After the call, the thread will be added to the
internal dictionary of active threads and functions such as
:func:`~threading.enumerate` and :func:`~active_count` will
also become aware of it.

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
