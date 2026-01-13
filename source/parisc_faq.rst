PA-RISC LINUX FAQ
=================

What binaries will PA-RISC Linux support?
-----------------------------------------

We currently only support 32-bit Linux ELF executables. Eventually, we
hope to add support for 64-bit Linux ELF executables. HP-UX emulation
was once planned but has been dropped in the meantime. To run HP-UX
32-bit binaries we suggest to install HP-UX in a qemu VM.

Will parisc-linux work on my system?
------------------------------------

Most likely yes. See :doc:`Hardware support <hardware_support>` for
details.

Where can I get an Install CD?
------------------------------

Pre-built installation ISOs can be downloaded from Debian and Gentoo
download pages.

How can I switch the console to serial without a keyboard/monitor?
------------------------------------------------------------------

On most machines unplugging all PS/2-, HIL- and USB keyboards before
turning the machine on will trigger the firmware to revert to using
SERIAL_1. For some models of older workstations, you can either remove
the framebuffer card, or hold down TOC for ten seconds while booting up.

How do I change the palo/linux kernel boot parameters?
------------------------------------------------------

Occasionally, one needs to change the :doc:`kernel boot parameters
<_kernel_boot_parameters>` to boot a system. You can also refer to the
PALO section of our HOWTO. Two ways to modify kernel boot parameters via
palo:

- **Interactive**: During bootup press the escape-key to stop auto-boot.
  On older machines enter "``BO scsi.X.0 IPL``" and replace X with the
  SCSI ID of your boot-disc. Appending ``IPL`` directs palo to interact
  with the user and offer a menu to change the boot-parameters **for
  this boot only**. On newer systems, ``IPL`` isn't needed since the PDC
  will ask ``Interact with IPL (Y/N)?`` Just enter Y here and palo will
  let you change the parameters.

- **Auto-boot**: To change the palo/linux kernel parameters
  **permanently**, edit ``/etc/palo.conf`` and change the arguments to
  ``--commandline`` parameter. Most importantly **run palo once** to
  store the new kernel parameters on the boot disk. The new parameters
  will be effective for successive boots.

Can I use the latest kernel from Linus?
---------------------------------------

Yes. We merge between the PA-RISC Linux repository and Linus' tree
regularly.

Can I run a 64-bit kernel on my machine?
----------------------------------------

First ask yourself: Why do I need to run a 64-bit kernel? Since
parisc-linux does not yet support 64-bit userspace, there is no
advantage to be gained running a 64-bit kernel on a machine with less
than 3.5GB of memory. In fact, there are many disadvantages including
bigger data structures and slightly less performance on many common
workloads.

Only PA-RISC 2.0 CPU can run a 64-bit kernel (aka "wide mode"). Check
/proc/cpuinfo if you already have linux booted. Or check firmware output
at power up time. Systems running the CPU clock speed slower than 160Mhz
are PA 1.x and faster than 180Mhz are PA 2.0. Systems between 160Mhz and
180Mhz are either PA 1.1 (PCX-L2, eg C160L) or PA 2.0 (PCX-U, eg C160).

Platform firmware also determines the type of kernel which can be used:

- **64-bit Only** : A500, L/N-class, rpXXXX, Superdome servers, and all
  PA-8800 CPUs must run a 64-bit kernel.

- **Dual Mode** : B2000/C3000, similar J-class workstations and PA 2.0
  D/K/R-class system firmware can be called from either 32-bit or 64-bit
  mode.

- **32-bit Only** : Workstations up to and including the J2240/C360 and
  servers of D/R-class have 32-bit firmware.

- Linux reports in the **capabilities** field of **/proc/cpuinfo** a
  combination of **os32** and **os64** which means the machine is
  capable to boot the respective kernel.

How do I report a kernel problem to the PA-RISC Linux developers?
-----------------------------------------------------------------

Randolph Chung wrote a small :doc:`How to report a parisc-linux kernel
problem <how_to_report_a_parisc-linux_kernel_problem>` just for YOU!
Ok. It's not that small anymore. But not providing at least a few of the
details listed in the HOWTO will just lead to folks (a) asking for the
missing info and/or (b) folks ignoring you.

How do I get my system to boot from the network?
------------------------------------------------

A long time ago, in a galaxy far, far away....well, not really. Just a
long time ago Martin Petersen (with help from others) wrote this :doc:`PARISC
NFS-Root-HOWTO <_parisc_nfs-root-howto>` for a fledgling parisc-linux
port. The "official" :doc:`NFS-Root-Client-mini-HOWTO
<_nfs-root-client-mini-howto>` will probably serve you better. It's
also called the "diskless root" if you want to search for more info.
It's also worth mentioning the :doc:`PA-RISC/Linux Boot HOWTO
<_pa-risc_linux_boot_howto>`.

Can parisc boot from RAID1?
---------------------------

Yes, see the :doc:`PA-RISC RAID1 Boot HOW-TO <_pa-risc_raid1_boot_how-to>`.

How can I check if the PDC (firmware) revision is the latest?
-------------------------------------------------------------

Lots of boot and some performance problems can be attributed to firmware
"bugs". All machines print the PDC revision at boot up. Or once at "Main
Menu>" or "BOOT_ADMIN>" one can print the PDC revision with "in fv"
(c3000) or "in" (712) command.

Putting more intelligence in any software module will increase the odds
of having bugs. Since firmware updates (aka patches) are freely
available (below), the latest firmware is required when reporting
problems instead of attempting other workarounds. The official "front
door" for all patches (Firmware, HPUX, MPE, OpenVMS, Tru64, Linux, ...)
is the :doc:`IT Resource Center <_it_resource_center>`. But if you know what
you need, go directly to the :doc:`catalog of firmware patches
<_catalog_of_firmware_patches>` and then grab the correct firmware
update from the :doc:`HP FFS Patch Server <_hp_ffs_patch_server>`.

The :doc:`PDC Patch Catalog <_pdc_patch_catalog>` lists firmware revs for
systems which have upgradeable firmware. If it's not listed either you
don't need a patch (yet :^) or it's not upgradeable. Systems older than
712 or 715's (eg 735) are only upgradeable via chip replacement.

.. warning::

   It's possible to kill a system by either upgrading the wrong firmware
   or the upgrade doesn't finish properly for any reason. Don't upgrade
   unless you have evidence of a PDC problem. Text files on the HP patch
   depot describe what was fixed in each revision since the machine
   model was released.

The system boots up on the STI Console but no "login:" prompt appears, what do I do?
------------------------------------------------------------------------------------

Check the contents of /etc/inittab and make sure the following lines
exist and are uncommented.

::

   1:2345:respawn:/sbin/getty 38400 tty1
   2:23:respawn:/sbin/getty 38400 tty2 
   3:23:respawn:/sbin/getty 38400 tty3 
   4:23:respawn:/sbin/getty 38400 tty4 
   5:23:respawn:/sbin/getty 38400 tty5
   6:23:respawn:/sbin/getty 38400 tty6 

*Contributed by: Phillip Beal* <pdbeal@louisville.edu>

The system boots up but I get Cannot access the Hardware Clock via any known method. What do I do?
--------------------------------------------------------------------------------------------------

``Cannot access the Hardware Clock via any known method.`` What do I do? Create the special /dev/rtc device::

    mknod /dev/rtc c 10 135

Can I use ISA cards in the EISA slots?
--------------------------------------

Yes. In general it's possible to use use ISA cards in the EISA slots. We
already had some success with ISA NE2000 clones and some other cards,
but you need to tell the kernel via the **eisa_irq_edge** boot parameter
which ISA IRQs should be available for ISA devices. See the next FAQ
entry for details. Also note that busmastering ISA or EISA cards are not
yet supported.

My ISA card is recognized but the IRQ is wrong or not accepted. What can I do?
------------------------------------------------------------------------------

In order to get ISA IRQs working you'll need to tell the kernel which
IRQs should be set to edge triggered mode and thus be available for ISA
devices. Add the kernel boot parameter
``eisa_irq_edge=3,4,5,7,9,10,11,14,15`` to the palo command line and
replace the numbers in this example with the ISA IRQs you need for your
cards.

How do I send Magic SysRq over lanconsole?
------------------------------------------

First you have to start a telnet session on your lanconsole, login to
GSP, and type 'CO' at the GSP prompt to get the system console. (You
might need to type '^Ecf' to gain write access on it).

Then you have to *escape* from the telnet session, by typing ``^]`` on a
QWERTY keyboard (``^-AltGr-]`` or ``^$`` on some AZERTY layouts).

You will get a ``telnet>`` prompt. Type ``send break``, hit enter to
validate. The next key you will hit will be sent as the SysRq, e.g. if
you type 'h' it will display the available SysRq keys.

You will have to start all over again from the escape sequence for each
SysRq you will need to send.

On my system ksoftirqd uses 70-100 percent of the CPU time. What's wrong?
-------------------------------------------------------------------------

This is currently a bug/feature related to the HIL drivers. In the HIL
drivers we poll the HIL ports with a few timers simultanously and those
timers seem to increase the ksoftirqd load a lot. But although the load
seems to be very high, the machine will be fully functional at maximum
speed and it seems to us as if this is only a cosmetical problem which
is maybe caused by some bugs in the load calculation functions in the
Linux kernel.

**Update:** Guy Martin `posted a patch
<http://article.gmane.org/gmane.linux.ports.parisc/278/match=use+lower+nice+level+ksoftirqd+hil+enabled_posted_a_patch>`__
which solves this issue, but this patch is sadly not acceptable for
upstream inclusion.

When trying to log in to the machine it times out with "Login timed out"
------------------------------------------------------------------------

Change the value of LOGIN_TIMEOUT in /etc/login.defs.

What are "unaligned access" messages about?
-------------------------------------------

``Jan 8 23:52:33 hp kernel: emacs(17795): unaligned access to 0x001cdaf2 at ip=0x0008930b``

The message usually means the program in question is buggy and is not
meeting address alignment requirements. The unaligned access is trapped
and emulated by the kernel, so normally the message itself is simply
informational. Like most architectures, PA-RISC loads (and stores) to
half-words, words, doublewords have specific address alignment
requirements. The address accessed must be divisible by the number of
bytes being stored. E.G. word (32-bit) stores should access an address
divisible by 4. You can control the Kernel behaviour in different ways:

- The value in ``/proc/sys/kernel/unaligned-trap`` tells the kernel if
  the unaligned access should be emulated or not. When 1, the kernel
  will transparently fix the buggy program (by emulating the unaligned
  access). When 0, the kernel will segfault the programm, which may help
  if you want to run the program in a debugger to find the issue.

- You can start your program with the prctl program. ``prctl
  --unaligned=[silent|signal|always-signal|default] your_program_name``
  changes the behaviour for the started program and all it's child
  processes. Read the man documentation for prctl for more details.

What about XFree and FX graphic cards?
--------------------------------------

FX Graphic adapters are not (and will probably never be) supported as
framebuffer devices, and don't have specific XFree drivers. That is to
say these cards can only be used through STI Console. Hence, you can't
use XFree with any FX card. Anyway, there is a reverse engineering
attempt on :doc:`Visualize FX <visualize_fx>`.

Why do I get corrupted graphics with my Vis-EG/Graffiti/A4450A card?
--------------------------------------------------------------------

This may be because your card is running in double buffered mode. To
check this, type '``co``' and then '``mo``' at the PDC menu prompt. If
'Double buffered' appears as part of the 'Class', select another monitor
type - for instructions on how to do this, type '``help mo``'.

Can I use a non-HP PCI graphic cards with PA-RISC Linux?
--------------------------------------------------------

Short Answer: Voodoo2/ATI Rage XL provide limited functionality. Long
Answer: See the :doc:`Graphics howto <graphics_howto>`.

On my rp5470, rpXXXX machine, the Attention LED is flashing
-----------------------------------------------------------

The Attention LED is flashing because there was some problem somewhen in
the past. To turn it off, log in to the GSP console and read the error
log (command: SL, then choose 'E' for error log). Accessing this error
log is the only way to turn off the attention LED when it is flashing
and alerts have not been acknowledged at the alert display level. You do
not need to read all error messages. Accessing this log once is
sufficient to turn the LED off.

How can I interact with the Remote Management processors like GSP or BMC?
-------------------------------------------------------------------------

Most PA-RISC server machines have a GSP, which you can access via the ^B
key. For details see :doc:`Guardian Service Processor
<guardian_service_processor>`. Latest generation of machines (e.g. the
C8000 workstation and the rp3410/rp3440 server) does instead have a BMC,
which you can access via ESC-( keys. For details see :doc:`BMC <bmc>`.

Why do I get "ENTRY_INIT failed" booting the Install CD?
--------------------------------------------------------

The full error message from the IODC (firmware) looks like::

    ENTRY_INIT failed, status = -3: Cannot complete call without error 

There are three likely causes for the ``ENTRY_INIT`` error and different
machines may return different status numbers depending on the reason
type of IO device:

#. non-bootable CD drive (broken or incompatible)
#. The CD drive block size (usually a jumper on the back) is not set to 512 byte blocks.
#. The CD has bad blocks where boot data is expected (or is upside down in tray :^)

The :doc:`How to create Debian unstable iso images
<how_to_create_debian_unstable_iso_images>` covers many of the issues
below. The following steps should help the rule out common failures:

#. Check the integrity of the CD image file used to burn the CD. The
   output of ``md5sum your_cd_img.iso`` should match the value found in
   ``md5sum.txt`` from the original site.

#. Use ``sea ipl`` from the PDC menu prompt to search for bootable media
   (hard disk or CD). Interrupt "autoboot" to get a PDC (aka Boot
   Console Handler) command prompt. CD, disk, or tape drives with
   bootable media installed will be listed in ``sea`` output. ``sea`` is
   much faster if one specifies the HW Path (eg ``sea SCSI.3.0 ipl``).
   Listen for the CD to spin up (a clue the CD is not broken) as ``sea``
   tries to read the boot sectors off the CD drive. If a CD drive then
   shows up in the list, next step is to verify the CD does not have bad
   blocks in the vmlinuz or initrd data.

#. Test the ability to boot by trying a HP-UX install disk then run
   ``sea`` command. If that works, the Debian CD was not burned
   correctly or has a defective boot sector(s). If ``sea`` doesn't work,
   then it's likely the CDROM drive is either broken, misconfigured, or
   just incompatible. Check CD-ROM drive data sheets to verify the drive
   is using 512byte sectors and not 2k sectors. The IODC (PARISC "BIOS")
   can only deal with 512 byte sectors. Some older CD-ROMs only support
   2k sectors and can not be used to boot.

#. Verify the integrity of the Debian CD. Reading the CD on another
   linux system using something like "``dd if=/dev/cdrom of=/dev/null
   bs=2k``". If all blocks are readable and a Windows machine was used
   to burn the CD, then the CD likely wasn't burned in "raw" mode and
   the boot blocks are mangled. Try burning the CD in "raw" mode. If the
   CD was burned without padding option, the CD integrity could also be
   checked with "``dd if=/dev/cdrom | md5sum``" and the result should
   match the value of the original iso image.

Serial console seems stuck and continues when pressing enter
------------------------------------------------------------

On newer Linux distributions the plymouth daemon tries to activate
the serial console. This sometimes interferes with when using
console on serial port.
Either remove the plymouth daemon, or add "``plymouth.ignore-serial-consoles``"
to the kernel boot command line.
