======
Sparse
======

Sparse is a tool to do compile-time checks on C code. Sparse can create
a parse tree of C code, similar to a GCC frontend. Using this parse
tree, it is possible to create additional compile-time checks of source
code, in addition to what regular C provides.

For example, a system call can be passed a userspace pointer. In regular
C, this will simply be a ``void *``. However, the kernel cannot directly
dereference this pointer. There is no syntax to denote this restriction
in regular C, and if a kernel developer makes a mistake, s/he will only
discover it at runtime, probably in the form of a kernel crash. Sparse
allows a developer to encode this restriction by annotating the pointer
as ``void __user *``, where ``__user`` denotes a separate address space.

There are currently three address spaces recognized by the kernel:

- User address space, annotated with ``__user``
- Kernel address space, no annotation
- I/O memory address space, annotated with ``__iomem``

These are defined in ``include/linux/compiler.h``

More information about sparse can be found at
http://www.linuxjournal.com/article.php?sid=7272 and
http://kerneltrap.org/node/view/3848. The sparse mailing list is at
http://vger.kernel.org/vger-lists.html#linux-sparse

The sparse source code is kept in `BitKeeper <http://www.bkbits.net/>`__
at bk://kernel.bkbits.net/torvalds/sparse, snapshots can be found at
http://www.codemonkey.org.uk/projects/bitkeeper/sparse/
