Thread Local Storage
====================

Recent versions of the GNU toolchain support a new method of managing
thread local storage (TLS). TLS has been implemented for parisc, the
binutils, and gcc changes are merged upstream with FSF. Not all changes
have been merged upstream with glibc.

Based on discussions with other developers, the recommended steps for
enabling TLS for parisc are:

#. Add the non-dynamic relocations into binutils.
#. Generate asm by hand for the relocations in a. to test that binutils is working.
#. Have binutils generate dynamic relocations.
#. Write relocation handling in ld.so (Don't build glibc with TLS enabled yet, just check for relocation handling).
#. Write more test asm by hand, this time with dynamic relocations.
#. Write the gcc code to handle ``__thread`` data. Places to look ``legitimize_address``
#. Rebuild glibc with TLS enabled.
#. Run TLS tests.

Changes are needed in the kernel, gcc, binutils and glibc to support
TLS:

- The pa backend in gcc needs to generate code for TLS variables (marked
  with the ``__thread`` keyword)

- gas needs to generate the correct relocations for TLS code sequences

- ld needs to correctly relocate TLS code sequences, and perform
  link-time optimization on TLS sequences

- TLS and NPTL support needs to be enabled in glibc

- The kernel needs to support some additional interfaces for NPTL

A design document for the relocations needed to support TLS is available
at http://parisc-linux.org/documentation/tls.html

gcc
---

Code sequences to support the various thread models are described below:

General Dynamic TLS Model
~~~~~~~~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1

   - 

      - Code sequence
      - Relocation
      - Symbol
   - 

      - ``addil LT'x-$tls_gdidx$, gp``
      - R_PARISC_TLS_GD21L
      - x
   - 

      - ``ldo RT'x-$tls_gdidx$(%r1), %arg0``
      - R_PARISC_TLS_GD14R
      - x
   - 

      - ``b __tls_get_addr``
      - R_PARISC_TLS_GDCALL
      - 
   - 

      - ``nop``
      - 
      - 
   - 

      - 
      - **Outstanding Relocations**
      - 
   - 

      - GOT[n]
      - R_PARISC_TLS_DTPMOD32
      - x
   - 

      - GOT[n+1]
      - R_PARISC_TLS_DTPOFF32
      - x

This sequence can be used to access thread-local variables anywhere, and
so this is the default code sequence that gcc can generate.

Local Dynamic TLS Model
~~~~~~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1

   - 

      - Code sequence
      - Relocation
      - Symbol
   - 

      - ``addil LT'x1-$tls_ldidx$, gp``
      - R_PARISC_TLS_LDM21L
      - x1
   - 

      - ``b __tls_get_addr``
      - R_PARISC_LDM_CALL
      - 
   - 

      - ``ldo RT'x1-$tls_ldidx$(%r1), %arg0``
      - R_PARISC_TLS_LDM14R
      - x1
   - 

      - ``addil LR'x1-$tls_dtpoff$, %ret0``
      - R_PARISC_TLS_LDO21L
      - x1
   - 

      - ``ldo RR'x1-$tls_dtpoff$(%r1), %tmp1``
      - R_PARISC_TLS_LDO14R
      - x1
   - 

      - ``addil LR'x2-$tls_dtpoff$, %ret0``
      - R_PARISC_TLS_LDO21L
      - x2
   - 

      - ``ldo RR'x2-$tls_dtpoff$(%r1), %tmp2``
      - R_PARISC_TLS_LDO14R
      - x2
   - 

      - 
      - **Outstanding Relocations**
      - 
   - 

      - GOT[n]
      - R_PARISC_TLS_DTPMOD32
      - 
   - 

      - GOT[n+1]
      - 
      - 

This sequence can be used when the compiler knows that the variable to
be accessed is "protected" -- for example, if it is a static variable,
or it is hidden/protected.

Initial Exec TLS Model
~~~~~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1

   - 

      - Code sequence
      - Relocation
      - Symbol
   - 

      - ``mfctl cr27, %t1``
      - 
      - 
   - 

      - ``addil LT'x-$tls_ieoff$, %gp``
      - R_PARISC_TLS_IE21L
      - x
   - 

      - ``ldw RT'x-$tls_ieoff$(%r1), %t2``
      - R_PARISC_TLS_IE14R
      - x
   - 

      - ``add %t1, %t2, %t3``
      - 
      - 
   - 

      - 
      - **Outstanding Relocations**
      - 
   - 

      - GOT[n]
      - R_PARISC_TLS_TPREL32
      - x

This sequence is used when we know that the thread-local variable being
referenced is in one of the initial modules being loaded at startup
(e.g. in the main executable or in one of the linked shlibs)

Local Exec TLS Model
~~~~~~~~~~~~~~~~~~~~

.. list-table::
   :header-rows: 1

   - 

      - Code sequence
      - Relocation
      - Symbol
   - 

      - ``mfctl cr27, %t1``
      - 
      - 
   - 

      - ``addil LR'x-$tls_leoff$, %t1``
      - R_PARISC_TLS_LE21L
      - x
   - 

      - ``ldo RR'x-$tls_leoff$(%r1), %t2``
      - R_PARISC_TLS_LE14R
      - x

This sequence can be used only if we know that the thread-local variable
being referenced is in the main executable, and so its TLS offset can be
computed at link time.

There have been some discussions on the optimal code sequences to use to
generate the call to ``__tls_get_addr``. On other archs,
``__tls_get_addr`` is implemented as a function supplied by glibc.
Currently, we are looking into whether it is possible to implement this
inside of libgcc.a instead, possibly as a millicode function. This has
the advantage of avoiding extra relocations for the call, as well as
saving some register spills/restores around a "normal" function call.

Dave Anglin suggested these sequences for calling ``__tls_get_addr``::

    1) Short PIC

            {bl|b,l} __tls_get_addr,%r2
            nop

    2a) Long non-PIC PA 1.x

            ldil L'__tls_get_addr,%r1
            ble R'__tls_get_addr(%sr4,%r1)
            copy %r31,%r2

    2b) Long non-PIC PA 2.0

            ldil L'__tls_get_addr,%r1
            be,l R'__tls_get_addr(%sr4,%r1),%sr0,%r31
            copy %r31,%r2

    3a) Long PIC PA 1.x

            {bl|b,l} .+8,%r1
            addil L'__tls_get_addr-$PIC_pcrel$0+4,%r1
            ldo R'%0-$PIC_pcrel$0+8(%r1),%r1
            ble 0(%sr4,%r1)
            copy %r31,%r2

    3b) Long PIC PA 2.0

            {bl|b,l} .+8,%r1
            addil L'__tls_get_addr-$PIC_pcrel$0+4,%r1
            ldo R'%0-$PIC_pcrel$0+8(%r1),%r1
            bve,l (%r1),%r2
            nop

The lengths of the call sequences are 8, 12 and 20 bytes. 2a and 2b are
the same (just a change in instruction syntax). 3b saves one instruction
if the delay slot can be utilized. I've used %r2 as the link register as
this provides 22-bit branch offsets on PA 2.0. Here's some alternative
sequences if we made ``__tls_get_addr`` a millicode routine and used r31
as the link register. Some of these are shorter if the delay slot can be
used.

::

    1) Short PIC

            {bl|b,l} __tls_get_addr,%r31
            nop

    2a) Long non-PIC PA 1.x

            ldil L'__tls_get_addr,%r1
            ble R'__tls_get_addr(%sr4,%r1)
            nop

    2b) Long non-PIC PA 2.0

            ldil L'__tls_get_addr,%r1
            be,l R'__tls_get_addr(%sr4,%r1),%sr0,%r31
            nop

    3a) Long PIC PA 1.x

            {bl|b,l} .+8,%r1
            addil L'__tls_get_addr-$PIC_pcrel$0+4,%r1
            ldo R'%0-$PIC_pcrel$0+8(%r1),%r1
            ble 0(%sr4,%r1)
            nop

    3b) Long PIC PA 2.0

            {bl|b,l} .+8,%r1
            addil L'__tls_get_addr-$PIC_pcrel$0+4,%r1
            ldo R'%0-$PIC_pcrel$0+8(%r1),%r1
            be,l 0(%sr4,%r1),%sr0,%r31
            nop

gas
---

gas needs to understand these constructs::

    $tls_gdidx$: Generate R_PARISC_TLS_GD21L/R_PARISC_TLS_GD14R relocations
    $tls_ldidx$: Generate R_PARISC_TLS_LD21L/R_PARISC_TLS_LD14R relocations
    $tls_dtpoff$: Generate R_PARISC_TLS_LDO21L/R_PARISC_TLS_LDO14R relocations
    $tls_ieoff$: Generate R_PARISC_TLS_IE21L/R_PARISC_TLS_IE14R relocations
    $tls_leoff$: Generate R_PARISC_TLS_LE21L/R_PARISC_TLS_LE14R relocations

ld
--

Additional entries need to be created in the GOT:

R_PARISC_TLS_GD21L/14R
  Allocate two contiguous entries in the GOT to hold a TLS index
  structure for passing to ``__tls_get_addr``. At runtime, the ti_module
  field (R_PARISC_TLS_DTPMOD32) and ti_offset (R_PARISC_TLS_DTPOFF32)
  fields are filled in to point to the correct module/offset.

R_PARISC_TLS_LDM21L/14R
  Similar to the above, but the ti_module parameter is always 0.

R_PARISC_TLS_IE21L/14R
  Allocate an entry in the GOT to hold the variable offset from the
  static TLS area.

Relocation processing:

R_PARISC_TLS_GD21L/14R, R_PARISC_LDM21L/14R
  Fill in the offset to the created GOT entry in the instruction.

R_PARISC_TLS_LDO21L/14R
  Calculate the offset of the variable relative to the TLS block it is
  contained in and fill in the offset in the instruction.

R_PARISC_TLS_IE21L/14R
  Fill in offset to GOT entry which contains the variable offset from
  the TLS block

R_PARISC_TLS_LE21L/14R
  Fill in offset to GOT entry which contains the variable offset from
  the static TLS block

R_PARISC_TLS_GDCALL/R_PARISC_TLS_LDMCALL
  Tag the function calls to ``__tls_get_addr`` so they can removed by
  the linker during TLS optimizations.

glibc
-----

We use the TLS_DTV_AT_TP layout::

      /* Memory layout is:
         [ TLS_PRE_TCB_SIZE ] [ TLS_TCB_SIZE ] [ TLS blocks ]
                              ^ This should be returned.  */

where ``TLS_PRE_TCB_SIZE`` is ``sizeof(_pthread_descr_struct)`` or
``sizeof(struct pthread)``, and ``TLS_TCB_SIZE`` is
``sizeof(tcbhead_t)`` (defined below).

Implement ``__tls_get_addr``; initially, with no linker optimizations,
we can use this (similar to alpha, ia64, etc)

.. code-block:: cpp

    void *
    __tls_get_addr(tls_index *ti)
    {
      dtv_t *dtv = THREAD_DTV();
      return (char *)dtv[1].pointer + ti->ti_offset;
    }

where THREAD_DTV() is defined as:

.. code-block:: cpp

    typedef union dtv {
      size_t counter;
      void *pointer;
    } dtv_t;

    typedef struct {
      dtv_t *dtv;
      void *private;
    } tcbhead_t;

    #define THREAD_DTV() \
      (((tcbhead_t *)__thread_self)->dtv)

With this layout, since the thread pointer points to the middle of the
allocated thread structure, the thread structure
(``_pthread_descr_struct`` or ``struct pthread``) is available as:

.. code-block:: cpp

    static inline struct _pthread_descr_struct * __get_cr27(void)
    {
            long cr27;
            asm("mfctl %%cr27, %0" : "=r" (cr27) : );
            return (struct _pthread_descr_struct *) cr27;
    }
    #  define THREAD_SELF (__get_cr27() - 1)

or, for NPTL

.. code-block:: cpp

    static inline struct pthread * __get_cr27(void)
    {
            long cr27;
            asm("mfctl %%cr27, %0" : "=r" (cr27) : );
            return (struct _pthread_descr_struct *) cr27;
    }
    # define THREAD_SELF (__get_cr27() - 1)

kernel
------

References
----------

Implementations from other archs:

- SH TLS for binutils: http://sources.redhat.com/ml/binutils/2002-10/msg00199.html
- PPC TLS for binutils: http://sources.redhat.com/ml/binutils/2003-02/msg00254.html
- PPC64 TLS for binutils: http://sources.redhat.com/ml/binutils/2003-02/msg00031.html
- x86-64 TLS for binutils: http://sources.redhat.com/ml/binutils/2002-09/msg00574.html
- alpha TLS for binutils: http://sources.redhat.com/ml/binutils/2002-05/msg00710.html
- sparc TLS for binutils: http://sources.redhat.com/ml/binutils/2003-01/msg00296.html

- PPC TLS for glibc: http://sources.redhat.com/ml/libc-alpha/2003-02/msg00241.html
- IA64 TLS for glibc: http://sources.redhat.com/ml/libc-hacker/2003-01/msg00083.html

- alpha TLS for gcc: http://gcc.gnu.org/ml/gcc-patches/2002-05/msg02639.html
- SH TLS for gcc: http://gcc.gnu.org/ml/gcc-patches/2003-02/msg01392.html
- PPC TLS for gcc: http://gcc.gnu.org/ml/gcc-patches/2003-05/msg00774.html
- sparc TLS for gcc: http://gcc.gnu.org/ml/gcc-patches/2003-09/msg00456.html
- IA64 TLS for gcc: http://gcc.gnu.org/ml/gcc-patches/2002-05/msg02038.html
