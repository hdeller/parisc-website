PA-RISC Space Registers and How to use them
===========================================

Instructions
------------

There are two types of instruction which reference memory; those with
2-bit '``s``' fields and those with 3-bit '``s``' fields.

Instructions with a 3-bit ``s`` field are straightforward. The value of
the ``s`` field directly specifies which of the 8 space registers to
use.

Instructions with a 2-bit ``s`` field have two cases. If ``s`` is 1-3,
we use the corresponding space register (``sr1-sr3``). If the value in
the ``s`` field is 0, we do not use space register 0. Instead, we use
one of ``sr4-7`` as determined by the top two bits of the **base
register**.

In Linux
--------

In Linux, ``sr4-7`` are always set to the same value. When in kernel
space, they are 0. In user space, they have the value for the task we're
currently executing.

What we were trying to do in ``pacache.S`` was use an instruction (flush
instruction cache) as if it were a 2-bit-s instruction when it's
actually a 3-bit-s instruction. This led to the processor using ``sr0``
instead of one of ``sr4-7``. The assembler now errors on this case,
causing us to find a bug.

Just to make things harder, there is a 2-bit-s version of flush
instruction cache, but it's only available on pa2.0 processors, so we
don't want to use it. Instead we use ``sr4`` (since ``sr4-7`` all have
the same value in Linux).
