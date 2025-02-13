.. _comparisons:

==========================
Comparisons to Other Tools
==========================

What makes PyOxidizer different from other Python packaging and distribution
tools? Read on to find out!

.. _compare_pyinstaller:

PyInstaller
===========

`PyInstaller <https://www.pyinstaller.org/>`_ - like ``PyOxidizer`` - can
produce a self-contained executable file containing your application.
However, at run-time, PyInstaller will extract Python source/bytecode
files to a temporary directory then import modules from the filesystem.
``PyOxidizer`` typically skips this step and loads modules directly from
memory using zero-copy. This makes ``PyOxidizer`` executables significantly
faster to start.

.. _compare_py2exe:

py2exe
======

`py2exe <http://www.py2exe.org/>`_ is a tool for converting Python scripts
into Windows programs, able to run without requiring an installation.

The goals of py2exe and ``PyOxidizer`` are conceptually very similar.

One major difference between the two is that py2exe works on just Windows
whereas ``PyOxidizer`` works on multiple platforms.

Another significant difference is that py2exe distributes a copy of Python and
your Python resources in a more traditional manner: as a ``pythonXY.dll``
and a ``library.zip`` file containing compiled Python bytecode. ``PyOxidizer``
is able to compile the Python interpreter and your Python resources directly
into the ``.exe`` so there can be as little as a single file providing
your application.

Also, the approach to packaging that py2exe and ``PyOxidizer`` take is
substantially different. py2exe embeds itself into ``setup.py`` as a
``distutils`` extension. ``PyOxidizer`` wants to exist at a higher level
and interact with the output of ``setup.py`` rather than get involved in the
convoluted mess of ``distutils`` internals. This enables ``PyOxidizer`` to
provide value beyond what ``setup.py``/``distutils`` can provide.

.. _compare_py2app:

py2app
======

`py2app <https://py2app.readthedocs.io/en/latest/>`_ is a setuptools
command which will allow you to make standalone application bundles
and plugins from Python scripts.

``py2app`` only works on macOS. This makes it like a macOS version of
``py2exe``. Most :ref:`comparisons to py2exe <compare_py2exe>` are
analogous for ``py2app``.

.. _compare_cx_freeze:

cx_Freeze
=========

`cx_Freeze <https://cx-freeze.readthedocs.io/en/latest/>`_ is a set of
scripts and modules for freezing Python scripts into executables.

The goals of ``cx_Freeze`` and ``PyOxidizer`` are conceptually very
similar.

Like other tools in the *produce executables* space, ``cx_Freeze`` packages
Python traditionally. On Windows, this entails shipping a ``pythonXY.dll``.
``cx_Freeze`` will also package dependent libraries found by binaries you
are shipping. This introduces portability problems, especially on Linux.

``PyOxidizer`` uses custom Python distributions that are built in such
a way that they are highly portable across machines. ``PyOxidizer`` can
also produce single file executables.

.. _compare_shiv:

Shiv
====

`Shiv <https://shiv.readthedocs.io/en/latest/>`_ is a packager for zip file
based Python applications. The Python interpreter has built-in support for
running self-contained Python applications that are distributed as zip files.

Shiv requires the target system to have a Python executable and for the target
to support shebangs in executable files. This is acceptable for controlled
\*NIX environments. It isn't acceptable for Windows (which doesn't support
shebangs) nor for environments where you can't guarantee an appropriate
Python executable is available.

Also, by distributing our own Python interpreter with the application,
PyOxidizer has stronger guarantees about the run-time environment. For
example, your application can aggressively target the latest Python version.
Another benefit of distributing your own Python interpreter is you can run a
Python interpreter with various optimizations, such as profile-guided
optimization (PGO) and link-time optimization (LTO). You can also easily
configure custom memory allocators or tweak memory allocators for optimal
performance.

.. _compare_pex:

PEX
===

`PEX <https://github.com/pantsbuild/pex>`_ is a packager for zip file based
Python applications. For purposes of comparison, PEX and Shiv have the
same properties. See :ref:`compare_shiv` for this comparison.

.. _compare_xar:

XAR
===

`XAR <https://github.com/facebookincubator/xar/>`_ requires the use of SquashFS.
SquashFS requires Linux.

``PyOxidizer`` is a target native executable and doesn't require any special
filesystems or other properties to run.

.. _compare_docker:

Docker / Running a Container
============================

It is increasingly popular to distribute applications as self-contained
container environments. e.g. Docker images. This distribution mechanism
is effective for Linux users.

``PyOxidizer`` will almost certainly produce a smaller distribution than
container-based applications. This is because many container-based applications
contain a lot of extra content that isn't needed by the processes within.

``PyOxidizer`` also doesn't require a container execution environment. Not
every user has the capability to run certain container formats. However,
nearly every user can run an executable.

At run time, ``PyOxidizer`` executes a native binary and doesn't have to go
through any additional execution layers. Contrast this with Docker, which
uses HTTP requests to create containers, set up temporary filesystems and
networks for the container, etc. Spawning a process in a new Docker
container can take hundreds of milliseconds or more. This overhead can be
prohibitive for low latency applications like CLI tools. This overhead
does not exist for ``PyOxidizer`` executables.

.. _compare_nuitka:

Nuitka
======

`Nuitka <http://nuitka.net/pages/overview.html>`_ can compile Python programs
to single executables. And the emphasis is on *compile*: Nuitka actually
converts Python to C and compiles that. Nuitka is effectively an alternate
Python interpreter.

Nuitka is a cool project and purports to produce significant speed-ups
compared to CPython!

Since Nuitka is effectively a new Python interpreter, there are risks to
running Python in this environment. Some code has dependencies on CPython
behaviors. There may be subtle bugs are lacking features from Nuitka.
However, Nuitka supposedly supports every Python construct, so many
applications should *just work*.

Given the performance benefits of Nuitka, it is a compelling alternative
to ``PyOxidizer``.

.. _compare_pyrun:

PyRun
=====

`PyRun <https://www.egenix.com/products/python/PyRun>`_ can produce single
file executables. The author isn't sure how it works. PyRun doesn't
appear to support modern Python versions. And it appears to require shared
libraries (like bzip2) on the target system. ``PyOxidizer`` supports
the latest Python and doesn't require shared libraries that aren't in
nearly every environment.

.. _compare_pynsist:

pynsist
=======

`pynsist <https://pynsist.readthedocs.io/en/latest/index.html>`_ is a
tool for building Windows installers for Python applications. pynsist
is very similar in spirit to PyOxidizer.

A major difference between the projects is that pynsist focuses on
solving the application distribution problem on Windows where ``PyOxidizer``
aims to solve larger problems around Python application distribution, such
as performance optimization (via loading Python modules from memory
instead of the filesystem).

``PyOxidizer`` has yet to invest significantly into making producing
distributable artifacts (such as Windows installers) simple, so pynsist
still has an advantage over ``PyOxidizer`` here.
