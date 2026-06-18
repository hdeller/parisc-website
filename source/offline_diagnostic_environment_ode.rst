====================================
Offline Diagnostic Environment (ODE)
====================================

Offline Diagnostic Environment (ODE) is a an environment to test HP
PARISC or IA64 hardware. It is usually distributed as ISO image, which
contains a LIF image with ISL + ODE binary + other data. To boot it in
qemu, it is sufficient to boot the ISO file.

HP provides some documentation about ODE at
https://support.hpe.com/connect/s/product?language=en_US&kmpmoid=4284216

Booting ODE
-----------

::

    qemu-system-hppa -boot d -machine [MACHINE] -cdrom HP_ODE_CDROM.iso

Make sure to use either B160L or C3700 as ``[MACHINE]`` parameter since
ODE will decide at runtime based on the found machine type which test
programs it will enable.

This will boot to ISL::

    HARD Booted.
    ISL Revision A.00.44  Mar 12, 2003 
        
       Cannot find an auto-execute file.  AUTOBOOT ABORTED.
     
    ISL>

Typing ode will start the diagnostic environment. It is possible to
start a specific test by adding it to ode::

    ODE L2DIAG

will start the PCXL2 CPU tests for the PA7100LC CPU in the B160L. You
will be asked for a password, in qemu it's usually 'quality', but can be
'poultry', 'saturn', 'dolphin' or 'country' as well.

L2DIAG
------

::

    ISL_CMD> l2diag
    ***************************************************************************
    ******                                                               ******
    ******                             L2DIAG                            ******
    ******                                                               ******
    ******       Copyright (C) 1995-2000 by Hewlett-Packard Company      ******
    ******                       All Rights Reserved                     ******
    ******                                                               ******
    ******   This program may only be used by HP support personnel and   ******
    ******   those customers with the appropriate Class license or       ******
    ******   Node license for systems specified by the license.  HP      ******
    ******   shall not be liable for any damages resulting from misuse   ******
    ******   or unauthorized use of this program.  This program          ******
    ******   remains the property of HP.                                 ******
    ******                                                               ******
    ******                         Version A.01.13                       ******
    ******                                                               ******
    ***************************************************************************
    Type DIAGINFO for test information.

    Enter password or a <CR> to exit:

    YOUR SELF-MAINTAINER/CHANNEL LICENSE EXPIRES IN  DAYS ON  12/31/9999
    Type HELP for command information.

    No other processors logged in.
    UNI-PROCESSOR MODE 

    L2DIAG>

diaginfo provides some useful information::

    L2DIAG> diaginfo

    L2DIAG is the PCXL2 ODE based diagnostic program.  It is intended
    to test the processor of the various PCXL2 based systems in the offline
    environment.  The program consists of 119 sections, 1/119,
    and are organized into the following groups:

       1.  CPU data path tests, Sections 1/6 (6 sections)
       2.  ICACHE tests, Sections 7/10 (4 sections)
       3.  DCACHE tests, Sections 11/17 (7 sections)
       4.  2nd Level Cache tests, Sections 18/21 (4 sections)
       5.  TLB tests, Sections 22/27 (6 sections)
       6.  CPU instruction tests, Sections 28/76 (49 sections)
       7.  CPU extended tests, Sections 77/88  (12 sections)
       8.  Floating point tests, Sections  89/119 (31 sections)

A test or range of test can be selected by 'section X or section X/Y'
where X is the starting test number, and Y the ending test number. If
only X is specified, only test X is run.

Useful ODE tools
----------------

L2DIAG: PCXL2 PA-7300LC diag
  useful for testing 32 bit CPU emulation in qemu (B160L machine)

UDIAG: PCX-U PA-8000 diag
  useful for testing 64 bit CPU emulation in qemu (C3700 machine)

WDIAG: PCX-W PA-8500 diag
  useful for testing 64 bit CPU emulation in qemu (C3700 machine)

Known issues/test failures in **WDIAG** in section 35/86
--------------------------------------------------------

::

    WDIAG overview:
    1.  CPU data path tests,      Section 1/6
    2.  BUS-INTERFACE tests,      Section 7/10
    3.  CACHE tests,              Section 11/25
    4.  TLB tests,                Section 26/34
    5.  CPU instruction tests,    Section 35/86
    6.  CPU extended tests,       Section 87/101
    7.  Floating point tests,     Section 102/134
    8.  Multiple processor tests, Section 140/150

.. list-table:: Known test failures in **WDIAG**
  :header-rows: 1

  - 

     - Section
     - Test
     - Comment
     - Notes
  - 

     - 1
     - cpu internal register tests
     - ::

         IN: 
         0x001a5660:  diag 281840
         0x001a5664:  nop
         0x001a5668:  diag 2008a6
         0x001a566c:  cmpb,<>,n r8,r6,0x1a585c
         should save r8 somewhere, then second diag restores that to r6 ???
     - 
  - 

     - 6
     - various unknown diag instructions
     - ::

         IN:
         0x001a68dc:  diag 4008bd
         0x001a68e0:  ldo 0(ret1),r24
         0x001a68e4:  depdi 1,53,1,ret1
         0x001a68e8:  diag 5d1840
         0x001a68ec:  diag 200ba0
         0x001a68f0:  ssm 0,r0
         ----------------
         IN:
         0x001a68f4:  diag 2008aa
         0x001a68f8:  bb,<,n r10,1a,0x1a6904
         ----------------
         IN:
         0x001a68fc:  depdi 0,63,11,r1
         0x001a6900:  b,l,n 0x1a690c,r0
         0x001a6904:  depdi 0,23,24,r1
         ----------------
         IN:
         0x001a690c:  cmpb,*<>,n r1,r5,0x1a6fe4
     - 
  - 

     - 63
     - PSW-B bit
     - Not emulated due to performance reasons
     - 
  - 

     - 65
     - dcor
     - not investigated yet
     - fixed by Richards patches
  - 

     - 66
     - shladd
     - not investigated yet
     - `fixed <https://lists.nongnu.org/archive/html/qemu-devel/2024-03/msg06047.html>`__
  - 

     - 71
     - PSW-X bit
     - Not emulated due to performance reasons
     - 
  - 

     - 72
     - ??
     - not investigated yet
     - 
  - 

     - 73
     - b,gate
     - not investigated yet
     - 
  - 

     - 74
     - ??
     - not investigated yet
     - 
  - 

     - 75
     - b,gate
     - not investigated yet
     - 
  - 

     - 77
     - ds
     - not investigated yet
     - 
  - 

     - 79-86
     - TLB?
     - hangs
     - 
  - 

     - 99
     - UNEXPECTED TRAP# 08 , IN SECTION 99, ADDR 282120
     - ::

         traps on instruction
         0x00282120:   00 40 40 e9
     - 
  - 

     - 102
     - ERROR 0030 IN SECTION 102
     - ::

         0x00283790:   b4 1a 00 cc   addi 66,r0,r26
         0x00283794:   b4 12 07 ff   addi -1,r0,r18
         0x00283798:   e8 40 40 00   blr r0,rp
         0x0028379c:   00 00 00 00   break 0,0
         INT  21553: break instruction trap @ 0000000000000000:000000000028379c
     - 
  - 

     - 103
     - ERROR 0052 IN SECTION 103
     - ???
     - 
  - 

     - 104
     - UNEXPECTED TRAP# 08 , IN SECTION 104, ADDR 284038
     - ::

         0x00284038:   30 85 86 06   frem,sgl fr4,fr5,fr6
         INT  21555: illegal instruction trap @ 0000000000000000:0000000000284038
         frem instruction not yet implemented in qemu ?
     - 
  - 

     - 105
     - 
     - ::

         0x00284b08:   b4 03 00 00   addi 0,r0,r3
         0x00284b0c:   b4 12 07 e5   addi -e,r0,r18
         0x00284b10:   39 cf 06 0e   fadd,sgl fr14,fr15,fr14
         0x00284b14:   24 f0 12 2e   fstw,ma fr14,8(r7)
         0x00284b18:   8a 43 20 06   cmpb,<>,n r3,r18,0x285b20
         expects fp helper handler to modify r3/r18
     - 

Known issues/test failures in **L2DIAG**
----------------------------------------

::

    L2DIAG overview:
    1.  CPU data path tests,   Section 1/6
    2.  ICACHE tests,          Section 7/10
    3.  DCACHE tests,          Section 11/17
    4.  2nd Level Cache tests  Section 18/21
    5.  TLB tests,             Section 22/27
    6.  CPU instruction tests, Section 28/76
    7.  CPU extended tests,    Section 77/88
    8.  Floating point tests,  Section 89/119

.. list-table:: Known CPU instruction test failures in **L2DIAG** in section 28/76
  :header-rows: 1

  - 

     - Section
     - Test
     - Comment
     - Notes
  - 

     - 6
     - mtctl r1,rctr
     - test CPU recovery counter (not implemented in qemu yet)
     - will probably not be fixed.
  - 

     - 36
     - probe,w (sr1,r11),r12,r5 does not return 0
     - ::

         IN:
         0x001c13c0:  addi 0,r0,r1
         0x001c13c4:  probe,w (sr1,r11),r12,r5
         0x001c13c8:  cmpb,<>,n r1,r5,0x1c1568
         fails
     - not fixed yet.
  - 

     - 40
     - depw,cond sar
     - fixed (0x001a07a0: add,tsv r13,r14,r15 ??)
     - fixed by Sven
  - 

     - 41
     - addi,cond
     - fixed
     - fixed by Sven
  - 

     - 45
     - sub,cond
     - fixed
     - fixed by Sven
  - 

     - 54
     - sub & subi,tsv,cond
     - fixed
     - fixed by Sven
  - 

     - 55
     - uaddcm,tc
     - ::

         IN: 
         0x001a2b2c:  uaddcm,tc,shc r13,r14,r15
         r13..r15: 55555555 55555555 00000000 should not trap.
     - `fixed by Richard <https://lists.nongnu.org/archive/html/qemu-devel/2024-03/msg05994.html>`__
  - 

     - 56
     - b,l vs. b,gate
     - ::

         0x001ba05c:  ldil L%4000,r18
         0x001ba060:  b,l 0x1ba068,r31
         0x001ba064:  b,gate 0x1ba06c,r0
         0x001ba068:  cmpb,<>,n r0,r18,0x1ba1c0
         IN: 
         0x001ba1c0:  addi 1,r0,ret0
     - NOT FIXED YET. checks that "b,gate" is not allowed in delay slot??
  - 

     - 58
     - uaddcm & dcor
     - dcor/uaddcm condition misbehaviour
     - `fixed by Richard <https://lists.nongnu.org/archive/html/qemu-devel/2024-03/msg05753.html>`__
  - 

     - 59
     - shladd,cond
     - 
     - fixed by Richard
  - 

     - 62
     - ERROR 0131 IN SECTION 062 ??
     - not investigated yet
     - 
  - 

     - 63
     - virt memory access / relied-upon-translation?
     - not investigated yet, maybe tdtlbp does not need to follow idtlba?
     - 
  - 

     - 64
     - rfi/be,l should not exec delay slot ???
     - ::

         IN: 
         0x001e0058:  rfi
         ---------------- 
         0x001e0060:  be,l 0(sr1,r21),sr0,r31
         ----------------
         IN: 
         0x001e0064:  addi 18,r0,r18
         ----------------
         IN: 
         0x001e2000:  nop
         0x001e2004:  cmpib,<> 0,r18,0x1e021c
         branches, but should not (r18 == 18, but should be 0)
     - 
  - 

     - 65
     - ERROR 0121 IN SECTION 065
     - not investigated yet
     - 
  - 

     - 66
     - ERROR 0003 IN SECTION 066
     - not investigated yet
     - 
  - 

     - 68
     - ERROR 0005 IN SECTION 068
     - not investigated yet
     - 
  - 

     - 73
     - ERROR 0010 IN SECTION 073
     - not investigated yet
     - 
  - 

     - 76
     - ERROR 0005 IN SECTION 076
     - not investigated yet
     - 
