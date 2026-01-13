=============================
PA-RISC Linux Project History
=============================

OLD NEWS
========

Dec 2024
~~~~~~~~
- `Qemu v9.2.0 was released <https://www.qemu.org/>`__.
  Includes some important fixes and a new SeaBIOS-hppa v17 firmware.
- Dave committed `a fix to glibc which implements vfork() by calling the kernel
  vfork syscall instead of emulating it via the clone() syscall
  <https://sourceware.org/pipermail/libc-alpha/2024-December/162834.html>`__.
  There is quite some difference like when which process continues. This showed up
  in the testcases of the strace package.
- Helge authored and Dave committed `a glibc cacheflush() syscall wrapper
  <https://sourceware.org/pipermail/libc-alpha/2024-December/163052.html>`__.
- `Michael Lorenz reported that he has developed graphics drivers for Visualize EG,
  HCRX and Visualize FX as well as an Xorg framebuffer driver for NetBSD
  <https://lore.kernel.org/linux-parisc/20241216064156.6bbe1330@bushmills>`__.
- `Paul Weissmann added a section about "PA-RISC Emulation QEMU" to the openpa.net
  website <https://www.openpa.net/qemu_pa-risc_emulation.html>`__.

Oct 2024
~~~~~~~~
- The PA-RISC Wiki has been replaced by a ReadTheDocs static website and
  is now hosted at https://parisc.docs.kernel.org. The website source code
  is available at https://github.com/hdeller/parisc-website
- `Dave Anglin <mailto:dave.anglin@bell.net>`__ works on
  `switching gcc to use LRA <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=113933>`__
- `Dmitry Sibirtsev <mailto:sibirtsevdl@gmail.com>`__ added support for
  the HPPA/PA-RISC architecture to the `Capstone
  disassembly/disassembler framework
  <https://github.com/capstone-engine/capstone/commit/9daa1ffbac35f553c4b2c0428af0b5cfdd6850d7#diff-c579e1e2290a10e71b47294265bc0be7b553b8c9fea1d3bd5b9b9546417ff5f5R2>`__.
  Capstone is used by by QEMU and other projects.

Sep 2024
~~~~~~~~
- Qemu v9.1.0 released. Needs at least `one patch for system-mode
  <https://gitlab.com/qemu-project/qemu/-/commit/ead5078cf1a5f11d16e3e8462154c859620bcc7e>`__
  and `one patch for user-mode
  <https://gitlab.com/qemu-project/qemu/-/commit/d33d3adb573794903380e03e767e06470514cefe>`__
  to run stable with 64-bit CPUs.

- Some patches for Linux kernel to fix 64-bit userspace issues.

Jul 2024
~~~~~~~~
- The `Debian 64-bit time_t transition
  <https://wiki.debian.org/ReleaseGoals/64bit-time>`__ has finished and
  stability of PA-Linux has improved greatly. Important next milestones
  will be to port rust to hppa, and maybe to add a LLVM backend for
  parisc.

Jun 2024
~~~~~~~~
- `Dave Anglin contributed a patch
  <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=72d95924ee35c8cd16ef52f912483ee938a34d49>`__
  which prevents the random segfaults we faced on PA8800 and PA8900 CPU
  machines. It is included in kernel 6.6.35, 6.9.6, and 6.10.0 (and
  higher).
- Work has started to add clock_gettime() syscalls in kernel vDSO.

Apr 2024
~~~~~~~~
- The `Debian 64-bit time_t transition
  <https://wiki.debian.org/ReleaseGoals/64bit-time>`__ is going very
  well. Upgrades were broken until now, but since end of april 2024 it's
  possible again to upgrade most existing debian hppa installations to
  the new binaries from the debian-ports archive. Since now 64-bit
  time_t is enabled by default, and since the LFS64 filesystem calls are
  enabled by default as well, we expect much lesss problems running
  debian hppa.
- `Qemu 9.0 was released. <https://wiki.qemu.org/ChangeLog/9.0>`__ Note
  that the 64-bit hppa emulation is still incomplete and may work with
  Linux guests only.

Mar 2024
~~~~~~~~

- Improving 64-bit CPU emulation in Qemu, e.g. with `CPU fixes from
  Richard <https://lists.nongnu.org/archive/html/qemu-devel/2024-03/msg06872.html>`__
  and `HP-UX 11 64-bit support fixes from Sven
  <https://lists.nongnu.org/archive/html/qemu-devel/2024-03/msg06020.html>`__.
- The `Offline_Diagnostic_Environment\_(ODE)
  <Offline_Diagnostic_Environment_(ODE)>`__ CD-ROM is helpful to verify
  quality of Qemu CPU emulation.
- `Debian 64-bit time transition is ongoing
  <https://wiki.debian.org/ReleaseGoals/64bit-time>`__. Lots of hppa
  packages have to be bootstrapped again.

Jan 2024
~~~~~~~~
- `Initial rust libc support for hppa
  <https://github.com/rust-lang/libc/pull/3539>`__ was added (and
  temporarily removed again).

Nov 2023
~~~~~~~~
- Upcoming Qemu v8.2 will support booting a 64-bit Linux kernel.

Sep 2023
~~~~~~~~
- `Kernel 6.6
  <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=847165d7c83ddb32aefab3ad4e7424fad919eb05>`__
  and `Qemu 8.2 <https://gitlab.com/qemu-project/qemu/-/commit/cb8a8b2ca9b25fdf561b0fd887df8344fe7927fd>`__
  with `SeaBIOS-hppa v9 <https://github.com/hdeller/seabios-hppa/commit/feb446728ae83f2973b58d9542bf25491dbf888d>`__
  supports Block-TLBs (BTLB) on 32-bit kernels.
- Kernel 6.6 includes `native eBPF JIT compiler for 32- and 64-bit
  kernels <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=48d25d382643a9d8867f8eb13af231268ab10db5>`__.
- `Dave fixed glibc v2.38 to prevent ``unaligned access to 0xf7ebadcd at
  ip 0xf5f7e307`` syslog warnings <https://sourceware.org/bugzilla/show_bug.cgi?id=30750>`__
- Development to support 64-bit PA-RISC CPU emulation and Astro/Elroy
  PCI-Bridge emulated device in QEMU started. Goal is to (hopefully)
  allow booting a 64-bit Linux kernel and HP/UX 11 with 64-bit support
  in QEMU.
- The `T2 System Development Environment <https://t2sde.org/>`__ Linux
  distribution added support for 32- and 64-bit PA-RISC.

Aug 2023
~~~~~~~~
- `Qemu 8.1.0 released <https://www.qemu.org/>`__.
- `Found a 10-year old bug (since kernel 3.11) which affected 32-bit parisc
  kernels <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=382d4cd1847517ffcb1800fd462b625db7b2ebea>`__.
- Many kernel bugs fixed in aio, ...

Jul 2023
~~~~~~~~
- `Qemu 8.0.3 released with many parisc fixes (SMP CPU fixes, graphics fixes) <https://www.qemu.org/>`__.

Jun 2023
~~~~~~~~
- Work started to implement native eBPF for Linux kernel.

May 2023
~~~~~~~~
- `palo version 2.24 <https://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__
  released.
- `STI text console support on 64-bit machines
  <https://patchwork.kernel.org/project/linux-parisc/patch/ZFKAK7Z4oi8f/ro4@p100/>`__,
  e.g. this allows using all original HP graphics cards in text-mode on
  C8000 workstations.
- `Fix palo to speed up printing the IPL menu on C8000 workstations
  <https://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/commit/?h=devel&id=37481ef2e3776292d15883b9f4aa855d4090ee2d>`__.

Apr 2023
~~~~~~~~
- Debian-12 (Bookworm) preparations, with `more than 13,000 pre-built
  packages <https://buildd.debian.org/status/architecture.php?a=hppa&suite=sid>`__.
- Progress on an `upcoming Visualize-FX5 fbdev graphics driver
  <https://patchwork.kernel.org/project/linux-parisc/patch/ZEWqkJekzyIlhBlW@p100/>`__.

Mar 2023
~~~~~~~~
- `More than 12,900 pre-built Debian packages available.
  <https://buildd.debian.org/status/architecture.php?a=hppa&suite=sid>`__

Jan 2023
~~~~~~~~
- Availability of a new `HP J6700
  <https://www.openpa.net/systems/hp-visualize_j6000_j6700.html>`__
  dual-core debian porterbox `parisc.debian.net
  <https://db.debian.org/machines.cgi?host=parisc>`__, sponsored by
  `Cypress Technology Inc <http://www.cypress-tech.com>`__. This
  machines was upgraded to 8 GB RAM and 2 x 300GB discs with parts of
  the former "parisc" `HP A500-44
  <https://www.openpa.net/systems/hp_a400_a500.html>`__ server.

Dec 2022
~~~~~~~~
- Adrian Glaubitz built a `new Debian 11 installation media
  <https://cdimage.debian.org/cdimage/ports/snapshots/2022-12-09/debian-11.0.0-hppa-NETINST-1.iso>`__.
- Helge submitted many fixes for qemu , e.g. for `getsockopt()
  <https://lists.nongnu.org/archive/html/qemu-devel/2022-12/msg02460.html>`__,
  `msync() <https://lists.nongnu.org/archive/html/qemu-devel/2022-12/msg02218.html>`__,
  `/proc/cpuinfo file <https://lists.nongnu.org/archive/html/qemu-devel/2022-12/msg02218.html>`__,
  `madvise() <https://lists.nongnu.org/archive/html/qemu-devel/2022-12/msg02218.html>`__,
  `strace <https://lists.nongnu.org/archive/html/qemu-devel/2022-12/msg02218.html>`__,
  `sendmsg() <https://lists.nongnu.org/archive/html/qemu-devel/2022-12/msg02218.html>`__
  and published an `initial port of libc for rust
  <https://github.com/rust-lang/libc/pull/3032#issuecomment-1359127774>`__.
- Dave `fixed a closure to function pointer bug in python-libcffi
  <https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1024271>`__ and
  spent a large amount of time reviewing debian buildd logs, doing give
  backs, and reporting issues. Furthermore Dave ported libffi to
  hppa64-hpux which exposed some issues in the 32-bit linux and hpux
  ports, e.g. `1 <https://github.com/libffi/libffi/issues/756>`__, `2
  <https://github.com/libffi/libffi/issues/755>`__ and `3
  <https://github.com/libffi/libffi/issues/753>`__.
- Linux kernel v6.2 will include a `patch which allows parisc to use the
  same MADV constants for madvise() like other platforms
  <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=71bdea6f798b425bc0003780b13e3fdecb16a010>`__.

Nov 2022
~~~~~~~~
- Installed new buildd servers "paladin" (a qemu-user emulation running
  Debian-x86 in VirtualBox on Windows).

Oct 2022
~~~~~~~~
- After 22 years the power supply in our A500-44 server (which was
  sponsored by HP) finally broke. It had hosted the ftp site and the
  mailing lists and was replaced by a x86-VM. Installed new buildd
  servers "pad" (a qemu-user emulation running Debian-x86 in VirtualBox
  on Windows) and "atlas" (physical C8000 machine).

Sep 2022
~~~~~~~~
- Lots of `enhancements to the qemu-user emulation
  <https://gitlab.com/qemu-project/qemu/-/commit/36cd0aeac3337af06875e08683380389df48ccd0>`__.
  Many of the debian buildd servers run now on x86 hardware which use
  qemu-user to build packages.

Aug 2022
~~~~~~~~
- New `palo version 2.22
  <https://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__
  released, `with support for booting without palo- or /boot paritions
  <https://lore.kernel.org/all/15eee327-ff0a-2bea-408d-259158915813@gmx.de/>`__.
  QEMU version 7.1.0 with many PA-RISC related enhancements and fixes
  has been released.

Jun 2022
~~~~~~~~
- `Sam James <mailto:sam@gentoo.org>`__ added `seccomp support in unit
  files on HPPA to systemd v252
  <https://github.com/systemd/systemd/pull/23181>`__ and worked on
  `elfutils support for HPPA
  <https://lists.debian.org/debian-hppa/2022/02/msg00000.html>`__.
- `John David Anglin <mailto:dave.anglin@bell.net>`__ helped to `fix a
  glibc bug about broken argv adjustments
  <https://sourceware.org/bugzilla/show_bug.cgi?id=29165>`__
- `Mark Cave-Ayland <mailto:mark.cave-ayland@ilande.co.uk>`__ provided
  lots of internal cleanups to the hppa PS/2 keyboard and PS/2 mouse
  emulation in the upcoming QEMU v7.1 to bring the source code up to
  latest QEMU coding standards.

May 2022
~~~~~~~~
- Linux kernel v5.18 released, includes vDSO-, CPU hotplug- and improved
  cache flushing support.
- `QEMU v7.1: Fix X11 cursor emulation in HP-UX and allow emulated PS/2
  keyboard input in firmware boot menu
  <https://git.qemu.org/?p=qemu.git;a=commit;h=78ac2eebbab9150edf5d0d00e3648f5ebb599001>`__
- `QEMU v7.1: Fix serial port pass-through from host to guest
  <https://git.qemu.org/?p=qemu.git;a=commit;h=0234342e0ea9411ad032dac61f3d632536bda789>`__

Apr 2022
~~~~~~~~
- CPU Hotplug support added to Kernel v5.18.
- `PALO v2.16 released. Prior versions didn't showed all available
  kernels when listing /boot directory at IPL menu.
  <http://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__

Mar 2022
~~~~~~~~
- A `new set of debian-hppa installation medias for debian unstable
  <https://lists.debian.org/debian-hppa/2022/03/msg00001.html>`__
  provided by Adrian Glaubitz.
- 16 years after the `initial vDSO patch by Randolf Chung
  <https://lore.kernel.org/linux-parisc/4544A34A.6080700@tausq.org/>`__,
  Linux kernel v5.18 will now finally include `vDSO support
  <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=df24e1783e6e0eb3dc0e3ba5a8df3bb0cc537408>`__.
  In addition Kernel v5.18 includes multiple TLB/cache-flush fixes and
  improvements.

Feb 2022
~~~~~~~~
- Many `Qemu patches were applied
  <https://github.com/qemu/qemu/commit/8f3e5ce773c62bb5c4a847f3a9a5c98bbb3b359f>`__
  to improve HP-UX graphics and mouse, allows up to 16 vCPUs, TOC
  support and much more. See our `Qemu <Qemu>`__ page.

Jan 2022
~~~~~~~~
- Kernel v5.16 released, with as many parisc enhancements and patches than never before!

Dec 2021
~~~~~~~~
- Richard Henderson and Helge Deller `fix a mis-translation of the stby
  assembly instruction in Qemu
  <https://lists.nongnu.org/archive/html/qemu-devel/2021-12/msg04501.html>`__.
- Multiple important kernel fixes by Dave Anglin.

Nov 2021
~~~~~~~~
- Sven fixes a `long-standing endianess bug in midnight commander which
  hangs the console when using F10 to exit
  <https://midnight-commander.org/ticket/3887>`__.
- Kernel v5.16 will include `TOC
  <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=bc294838cc3443a2fbec58f8936ad4bd0a0b3055>`__
  and `KFENCE <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=ec5c115050f59114e216212837f1c1ebc54bdfc9>`__
  support.

Sep 2021
~~~~~~~~
- Starting with Linux kernel 5.15, you will need to specify
  "ARCH=parisc64" on the command line when bulding a 64-bit kernel.
- Adrian announced that he built `new installation medias for debian
  unstable <https://lists.debian.org/debian-hppa/2021/09/msg00003.html>`__,
  `ISO images are available here
  <https://cdimage.debian.org/cdimage/ports/snapshots/2021-09-23/>`__.

Jul 2021
~~~~~~~~
- The power supply of our old A400 server ("parisc") broke and got replaced. Machine is now back up again.

Feb 2021
~~~~~~~~
- Various fixes (e.g. `1 <https://lists.nongnu.org/archive/html/qemu-devel/2021-02/msg03433.html>`__\
  `2 <https://lists.nongnu.org/archive/html/qemu-devel/2021-02/msg00298.html>`__)
  for qemu v5.2 to further improve qemu-user emulation.
- `Switch to a faster page table locking mechanism
  <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b7795074a04669d0a023babf786d29bf67c68783>`__
  with Linux kernel v5.12.

Jan 2021
~~~~~~~~
- `PALO v2.15 released
  <http://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__.
  New parisc Debian build server `"pasta"
  <https://buildd.debian.org/status/architecture.php?a=hppa&suite=sid&buildd=buildd_hppa-pasta>`__,
  which runs in a qemu-user parisc chroot environment on a x86-64
  virtual machine with 8 CPUs and 16GB RAM. Many thanks to the `OSU Open
  Source Lab <http://osuosl.org/>`__ who sponsors this VM!
- Additionally there is a new parisc Debian build server `"paq"
  <https://buildd.debian.org/status/architecture.php?a=hppa&suite=sid&buildd=buildd_hppa-paq>`__.
  This qemu-user based build server runs on a Synology DS920+ in a
  x86-64 virtual machine with 3 Intel Celeron J4125 CPUs and 1.5GB RAM.

Dec 2020
~~~~~~~~
- Reached `12.000 pre-built debian packages <http://buildd.debian.org/status/architecture.php?a=hppa&suite=sid>`__.

Oct 2020
~~~~~~~~
- In Kernel 5.10 O_NONBLOCK was changed to become 000200000 (instead of
  000200004). This is a binary ABI change and requires userspace (glibc,
  dietlibc, systemd) to be recompiled with the new value. The kernel has
  wrapper functions which provide backwards compatibility.

Aug 2020
~~~~~~~~
- `Roger Sayle <mailto:roger@nextmovesoftware.com>`__ fixed a
  long-standing `gcc compiler bug which unnecessarily slowed down
  compile time <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=87256>`__.

Jul 2020
~~~~~~~~
- Helge steps up as new `rbootd <https://git.kernel.org/pub/scm/linux/kernel/git/deller/rbootd.git/>`__
  maintainer.
- Dave fixes a `long-lasting kernel CPU soft lockup bug
  <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=be6577af0cef934ccb036445314072e8cb9217b9>`__.
- Many bugfixes `1 <https://git.qemu.org/?p=qemu.git;a=commit;h=79826f99feb7222b7804058f0b4ace9ee0546361>`__\
  `2 <https://git.qemu.org/?p=qemu.git;a=commit;h=b1af755c33bf0d690553a5ccd93689dfd15a98e8>`__
  and improvements for qemu and preparations for qemu-5.1.0 release.  - 

Feb 2020
~~~~~~~~
- The graphics and device patches for PA-RISC have been applied to QEMU git.

Sep 2019
~~~~~~~~
- `PALO v2.12 released
  <http://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__.
  Added `kexec support <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=fc697dc0c26a5908d467454e49440862d7fe96d0>`__
  and `enhanced ftrace support <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=52a22e6c27be9089fdd9c4e1857939f0dec8f57c>`__
  to Linux kernel v5.4. LASI support (serial, parallel, network, PS/2
  keyboard & PS/2 mouse) and Artist graphics w/STI emulation for QEMU
  and SeaBIOS.

Jul 2019
~~~~~~~~
- `Released PALO version v2.11
  <http://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__
  with important enhancements by `James Bottomley
  <mailto:James.Bottomley@HansenPartnership.com>`__ like added
  ext4-support, IPL overlap with newer ext2/ext3 resize_inode was fixed
  and the iplboot bootcode loader is now placed inside the disk label.

May 2019
~~~~~~~~
- Linux kernel 5.2 supports `SPARSEMEM instead of DISCONTIGMEM
  <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=dbdf0760990583649bfaca75fd98f76afd5f3905>`__,
  `static branches <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=62217beb394e654bbd2bb87c533dadd2d8bf62c6>`__
  and `per-pagetable spinlocks <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b37d1c1898b288c69f3dc9267bc2c41af06f4a4b>`__.

Apr 2019
~~~~~~~~
- `Sven <mailto:svens@stackframe.org>`__ adds `KGDB
  <http://parisc.wiki.kernel.org/index.php/KGDB>`__ and `KPROBES
  <http://lwn.net/Articles/132196/>`__ support for the Linux kernel
  version 5.2.

Mar 2019
~~~~~~~~
- Lots of fixes and improvements in the QEMU CPU- and SCSI emulation
  code as well as for the SeaBIOS firmware due to efforts of `Sven
  Schnelle <mailto:svens@stackframe.org>`__. See the `PA-RISC Qemu Wiki
  page <http://parisc.wiki.kernel.org/index.php/Qemu>`__ for latest
  updates.

Jan 2019
~~~~~~~~
- Debian kernel 4.20 will ship built as SMP kernel only (for 32- and
  64-bit), which then patches itself at bootup to run faster on
  uniprocessor machines if it detects only one CPU.
- The OSUOSL team was so nice to upgrade the debian buildd servers:
  "parisc" has now two 300 GB (instead of two 68 GB) and panama three
  300 GB (instead of two 18 GB) SCSI discs. Thanks!

Nov 2018
~~~~~~~~
- A new complete `parisc-linux mailing list archive is now available at
  lore.kernel.org <http://lore.kernel.org/linux-parisc/>`__. It even
  contains the `very first parisc-linux mail
  <http://lore.kernel.org/linux-parisc/Pine.LNX.4.04.9901291134480.1532-100000@sprite.osfi-bsif.gc.ca/>`__.
- Linux kernel 4.20 includes the `"alternative"-feature which enables us
  to do live-patching of the kernel code at bootup time
  <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3847dab77421867fbc77faacb2f377d44e729e1b>`__.
  With this feature we will be able to build a SMP kernel which is
  patched at boottime to run faster on a UP system.

Sep 2018
~~~~~~~~
- Reached `11.700 pre-built debian unstable packages <http://buildd.debian.org/status/architecture.php?a=hppa&suite=sid>`__.

Jul 2018
~~~~~~~~
- Dave fixed a `long-standing binutils bug which generated wrong debug
  unwind info <http://sourceware.org/ml/binutils/2018-07/msg00474.html>`__.
- `Debian porterbox panama.debian.net is now available <http://db.debian.org/machines.cgi?host=panama>`__.

Apr 2018
~~~~~~~~
- The Linux kernel `now powers off rp34x0 and C8000 machines at shutdown
  via built-in BMC controller <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c6185e285c5c7cfeab739bae7f206ced695f09c7>`__.

Mar 2018
~~~~~~~~
- New documentation on how to access the :doc:`Baseboard Management
  Controller (BMC) in rp34x0 and C8000 machines <bmc>`.

Feb 2018
~~~~~~~~
- `QEMU 2.12 supports parisc system-emulation (qemu-system-hppa)
  <Qemu>`__ thanks to `contributions by Richard Henderson and Helge
  Deller <https://git.qemu.org/?p=qemu.git;a=commit;h=b05631954d6dfe93340d516660397e2c1a2a5dd6>`__.

Jan 2018
~~~~~~~~
- New `palo bootloader v2.00 released
  <http://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__.
  The callee copies ABI of gcc (on 32-bit hppa) created lots of
  optimization issues and problems with OpenMP, so `Dave committed a
  patch to switch to the caller copy ABI
  <http://gcc.gnu.org/ml/gcc-patches/2018-01/msg01518.html>`__ instead.
- First Qemu-related `patches to Linux kernel to reduce power
  consumption on the host <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=310d82784fb4d60c80569f5ca9f53a7f3bf1d477>`__
  have been submitted and the Qemu hardware emulation works fully stable
  with a booted Linux. Even an emulated 32-CPU PA-RISC SMP machine was
  running stable.

Dec 2017
~~~~~~~~
- Development on parisc hardware emulation via qemu is ongoing. Richard
  Henderson maintains the `hppa qemu git tree
  <https://github.com/rth7680/qemu/commits/tgt-hppa-softmmu>`__ while
  Helge Deller works on a `parisc IODC and PDC firmware emulation via a
  SeaBIOS fork <https://github.com/hdeller/seabios-hppa/commits/parisc_firmware>`__.
  The emulation already boots a Debian install CD, runs through whole
  kernel initialization code and even runs quite far into the init
  process.

Sep 2017
~~~~~~~~
- New Debian-9 installer image is now available at `official cdimage
  places <https://cdimage.debian.org/cdimage/ports/>`__. Many thanks to
  Adrian Glaubitz!
- Kernel 4.14 includes full support for Page Deallocation Table (PDT),
  the UBSAN checker and kernel self-decompressor.

Aug 2017
~~~~~~~~
- Kernel 4.13 includes initial Page Deallocation Table (PDT) support to
  avoid using broken memory by the Linux kernel.
- Dave submitted all outstanding patches for hppa to glibc v2.26.
  Linuxthread support was dropped and switched to the generic nptl
  support instead. Unwinding was fixed to support pthread cancellation.
- Thomas Bogendoerfer fixed the PCI memory bar assignment on Dino/Cujo
  machines and as such got ATI graphic cards to work on such machines.
- New `palo bootloader v1.99 released
  <https://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__
  which fixes problems when loading huge kernels, e.g. with kernel UBSAN
  support enabled.

June 2017
~~~~~~~~~
- `Mike Hosken announced a new set of Debian 9 installation medias
  <https://lists.debian.org/debian-hppa/2017/06/msg00006.html>`__ which
  are `available for download
  <http://prometheus.parisc-linux.org/debian-cd/>`__
- New `palo bootloader v1.98 released
  <https://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__
  which fixes serial console on rp3410 and rp3440 servers.
- There are now `more than 11.600 pre-built debian unstable packages
  <https://buildd.debian.org/status/architecture.php?a=hppa&suite=sid>`__
  available.

May 2017
~~~~~~~~
- `Roberto C. Sánchez <mailto:roberto@debian.org>`__ sponsored a `HP
  rp3410 server
  <http://www.openpa.net/systems/hp-9000_rp3410_rp3440.html>`__ with
  :doc:`one 800 MHz PA8900 CPU which we upgraded to 5 GB RAM and added a 146
  GB hard disc <rp3410>`. The server is now reachable as
  `panama.parisc-linux.org <https://monitor.jrtc27.com/>`__ and acts as
  a debian buildd machine. `Hosting is sponsored by OSU Open Source Lab
  <http://osuosl.org/>`__. Thanks to all people involved, especially to
  the great system administrators at OSUOSL (Samarendra Hedaoo and
  Justin Dugger).

Apr 2017
~~~~~~~~
- John Paul Adrian Glaubitz and James Clarke made the `debian-installer
  available in debian-ports repositories
  <https://lists.debian.org/debian-powerpc/2017/04/msg00023.html>`__

Feb 2017
~~~~~~~~
- Alan Modra and Dave Anglin `fix pie support on hppa
  <https://sourceware.org/git/gitweb.cgi?p=binutils-gdb.git;h=8a3cacb06d6ecfbe3ab0e0059bf6a4eb8b4b9dc6>`__
  and `add -z relro support to binutils <https://sourceware.org/bugzilla/show_bug.cgi?id=21000>`__

Jan 2017
~~~~~~~~
- `New Debian-9 installation CDs
  <https://lists.debian.org/debian-hppa/2017/01/msg00001.html>`__ by
  Mike Hosken. Reached `more than 11.500 pre-built debian unstable
  packages <https://buildd.debian.org/status/architecture.php?a=hppa&suite=sid>`__.

Dec 2016
~~~~~~~~
- Reached more than 11.400 pre-built debian unstable packages. John
  David Anglin and John Paul Adrian Glaubitz fix the `Firefox webbrowser
  <https://buildd.debian.org/status/package.php?p=firefox&suite=sid>`__
  with help of Michael Karcher. The debian buildd servers switched to a
  unified automated build setup by James Clarke. Status of the buildds
  can be seen at https://monitor.jrtc27.com/

Nov 2016
~~~~~~~~
- `Richard Henderson announced initial work on a user-only target of
  hppa to QEMU
  <https://marc.info/?l=glibc-alpha&m=147869603031638&w=2>`__. The
  website and domain http://pateam.org has expired and all content is
  now archived at http://pateam.parisc-linux.org/. Thanks to Thibaut
  Varene for his work on pateam.org.

Oct 2016
~~~~~~~~
- New `palo v1.96 bugfix release available
  <https://git.kernel.org/cgit/linux/kernel/git/deller/palo.git/>`__.

Sep 2016
~~~~~~~~
- Reached more than 11.300 pre-built debian unstable packages. Linux
  Kernel 4.7 now `includes a high-resolution timer
  <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=54b668009076caddbede8fde513ca2c982590bfe>`__
  based on the cr16 clocksource. Debian perl transition finished. Debian
  Linux Kernel 4.7.5 available.

Aug 2016
~~~~~~~~
- Debian transitions to gcc-6 and boost1.61, Debian on hppa changes the
  default mpi implementation from mpich to openmpi

Jul 2016
~~~~~~~~
- Reached more than 11.200 pre-built debian unstable packages.

Jun 2016
~~~~~~~~
- Reached more than 11.100 pre-built debian unstable packages.

Apr 2016
~~~~~~~~
- Linux kernel 4.7 got improved kernel ftrace support, kernel regset
  support and a native high-resolution clock/timing support. Additional
  debian buildd make server added (savaria.parisc-linux.org).

Mar 2016
~~~~~~~~
- Solved a `bug in gcc-4.9 (and higher)
  <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=70188>`__ which `crashes
  kernel 4.3+ in the SCSI/block layer
  <http://article.gmane.org/gmane.linux.ports.parisc/26425>`__.

Feb 2016
~~~~~~~~
- We now have more than 11.000 pre-built debian unstable packages. Many
  bugs in kernel and userspace were fixed.

Jan 2016
~~~~~~~~
- Reached more than 10.900 pre-built debian unstable packages.

Dec 2015
~~~~~~~~
- `Huge Page support for PA-RISC was added to the Linux Kernel
  <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=736d2169338a50c8814efc186b5423aee43b0c68>`__.
- A long-standing `syscall restart bug was fixed
  <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=71a71fb5374a23be36a91981b5614590b9e722c3>`__.

Oct 2015
~~~~~~~~
- Dave & Helge fixed some bugs in `gcc
  <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=68079>`__ and `glibc
  <https://sourceware.org/bugzilla/show_bug.cgi?id=19170>`__ which e.g.
  fixed the build of Qt5/KDE packages. For the first time ever we now
  have more pre-built debian packages than alpha or ppc64 and reached
  more than 10.800 packages.

Sep 2015
~~~~~~~~
- We have an additional Debian buildd server (phantom.parisc-linux.org
  with 4 x 1 GHz PA8900 CPUs, 12 GB RAM, 120 GB SSD) online. Dave `fixed
  64bit CAS LWS so that we will get 64bit gcc atomic builtins
  <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=1b59ddfcf1678de38a1f8ca9fb8ea5eebeff1843>`__.
  Helge `fixed boot problems because of early serial port irqs on C8000
  with PA8900 CPUs
  <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b1b4e435e4ef7de77f07bf2a42c8380b960c2d44>`__.

Aug 2015
~~~~~~~~
- One of our buildd servers (hpviz) died. Debian-ports archive is now
  integrated with Debian buildd service again.

Jul 2015
~~~~~~~~
- Reached more than 10.700 pre-built debian unstable packages.

Jun 2015
~~~~~~~~
- Reached more than 10.600 pre-built debian unstable packages.

Apr 2015
~~~~~~~~
- Reached more than 10.520 pre-built debian unstable packages which is
  just around 60 packages less than the leading debian-ports
  architectures alpha and ppc64.

Mar 2015
~~~~~~~~
- Installation numbers of hppa debian machines grow to 25 in the `Debian
  Popularity Contest <http://popcon.debian.org>`__.

Feb 2015
~~~~~~~~
- `DEBIAN 8.0 (jessie) Installer images
  <http://ftp.debian-ports.org/debian-cd/hppa/debian-8.0/>`__ and
  `Installation instructions <Debian_Ports_Installation>`__ now
  available.

Jan 2015
~~~~~~~~
- Compared to other architectures on debian-ports, the PA-RISC / HPPA
  architecture now has the most number of packages up-to-date:
  http://buildd.debian-ports.org/stats/graph2-week-big.png

Dec 2014
~~~~~~~~
- Full range of gcc atomic builtins committed into gcc-4.9 which helped
  us to build packages which were not posible before (in total we are
  now at 10.400 prebuilt debian packages).

Nov 2014
~~~~~~~~
- We now fully support systemd with debian packages glibc-2.19-13 and
  linux-image-3.16.0-4 (kernel 3.16.7).
- The `contents of the parisc-linux kernel mailinglist were imported
  into gmane.org archive <http://dir.gmane.org/gmane.linux.ports.parisc>`__

Oct 2014
~~~~~~~~
- Reached 10.300 pre-built debian unstable packages.
- `Upcoming patches to support systemd on hppa
  <https://patchwork.kernel.org/patch/5048751>`__

Sep 2014
~~~~~~~~
- Browsers will now automatically be transferred to this Wiki if one
  visits http://www.parisc-linux.org.
- We now have more than 10.200 pre-built debian unstable packages.

Aug 2014
~~~~~~~~
- We now have more than 10.100 pre-built debian unstable packages.
- Helge Deller now owns and maintains the parisc-linux domain.

Jul 2014
~~~~~~~~
- New `Debian unstable installer images available
  <Debian_Ports_Installation>`__.
- Reached more than 10.000 pre-built debian unstable packages.

Jun 2014
~~~~~~~~
- The A500-44 is up and running as additional debian buildd server at
  http://parisc.osuosl.org.
- Kernel 3.15 is out.
- Added a very fast 4-way 1GHz C8000 machine with 250GB SATA SSD drive
  as additional Debian buildd server (sibaris.parisc-linux.org), machine
  and hosting sponsored by `Bálint Sándor Németh
  <mailto:pannonmage@googlemail.com>`__.

May 2014
~~~~~~~~
- More than 9900 debian packages built.
- Kernel 3.15 will be the most best kernel ever.
- The A500 (mkhppa3) buildd which was sponsored and hosted by ESIEE
  sadly died.

Apr 2014
~~~~~~~~
- The PARISC boot loader palo is back in debian unstable.
- More than 9750 debian packages built.

Mar 2014
~~~~~~~~
- The A500-44 which hosted our main webpage is being moved to OSUOSL.org.
- More than 9500 debian packages built.

Feb 2014
~~~~~~~~
- The new parisc Wiki is now at http://parisc.wiki.kernel.org. Contents
  of the old Wiki at wiki.parisc-linux.org was moved and the webpage
  redirects here.

Jan 2014
~~~~~~~~
- Updated :doc:`Debian unstable install instructions <debian_ports_installation>`.
- More than `8000 newly built native
  Debian packages <http://buildd.debian-ports.org/stats>`__ available.

Dec 2013
~~~~~~~~
- `5 buildd servers for debian unstable online <http://unstable.buildd.net/index-hppa.html>`__.
- The servers hpviz and rio including hosting are sponsored by Dave Land (http://landcomp.net).
- Stable Debian kernel 3.12 available.

Aug 2013
~~~~~~~~
- Linux kernel 3.11 supports C8000 workstation (SMP, serial port, FireGL
  card, BMC, ...)

Apr 2013
~~~~~~~~
- Website updated, added :doc:`Debian ports installation instructions
  <debian_ports_installation>` and updated :doc:`TODO <todo>` list.

Mar 2013
~~~~~~~~
- Debian 7.0 (wheezy) without HPPA.


Feb 2013
~~~~~~~~
- Many PA-RISC Linux kernel patches have been integrated into Kernel
  3.8. Stability will improve even further with kernel 3.9.

Feb 2012
~~~~~~~~
- Website is back up! Work continues on the debian-ports support for HPPA.

Sep 2010
~~~~~~~~
- HPPA dropped as an official release architecture for squeeze.

Sep 2009
~~~~~~~~
- NPTL transition is ready, thanks to the efforts of Carlos!

Feb 2009
~~~~~~~~
- Debian 5.0!

Apr 2007
~~~~~~~~
- Debian 4.0!

Jul 2006
~~~~~~~~
- James fixes (mostly) parisc-linux on PA8800! w00t!

Mar 2006
~~~~~~~~
- Fixed stifb on 64-bit machines. X now works with a 64-bit kernel!

Dec 2005
~~~~~~~~
- Fixed framebuffer console on 32bit HCRX graphic cards

Dec 2005
~~~~~~~~
- PCMCIA support added (e.g. used in Tadpole PA-RISC laptops)

Jun 2005
~~~~~~~~
- Debian 3.1!

Mar 2005
~~~~~~~~
- The parisc-linux patch set is less than 200K for the first time ever!
  Thanks to willy for doing such a great job pushing patches upstream!

Jun 2004   
~~~~~~~~
- Support for PA8800/zx1 committed!

PROJECT HISTORY
===============
.. image:: media/www_parisc-linux_org-front.jpg
   :width: 400px
   :align: right

.. image:: media/www_parisc-linux_org-back.jpg
   :width: 400px
   :align: right

.. image:: media/sibaris.jpg
   :width: 400px
   :align: right

.. image:: media/rp3410_panama_front.jpg
   :width: 400px
   :align: right

.. image:: media/rp3410_panama_back.jpg
   :width: 400px
   :align: right

December 1, 2000
~~~~~~~~~~~~~~~~
The PA-RISC web site has moved to www.parisc-linux.org domain. Besides
the appearance of the Linuxcare penguin Lux (in gear suitable for
working on the PA-RISC engine), the PA-RISC site is fresh with a content
update, face lift and co-branding with HP.

December 15, 1999
~~~~~~~~~~~~~~~~~
The Puffin Group and Linuxcare join forces, providing us all with
additional resources and backing to dedicate to the porting effort. In
fact, we are now working even more closely with Hewlett-Packard. While
we were in California getting all the details sorted out, a number of
people posted to the mailing list with concerns that the project was
dead. This couldn't be further from the truth. Martin Fink from HP
posted the following message to help clear up the misunderstanding:
http://puffin.external.hp.com/mailing-lists/parisc-linux/1999/12-Dec/0073.html.

November 19, 1999
~~~~~~~~~~~~~~~~~
Hewlett-Packard, in cooperation with The Puffin Group, has released the
source code to their SOM linker product.

August 16, 1999
~~~~~~~~~~~~~~~
The source code repository is now `cross-referenced
<http://puffin.external.hp.com/lxr/source>`__ online.

July 6, 1999
~~~~~~~~~~~~
Philipp Rumpf gets interruptions working, you can now enter them and the
system returns from them.

June 26-27, 1999
~~~~~~~~~~~~~~~~
Matthew Wilcox and Thomas Bogendoerfer in cooperation with HP
demonstrate the booting Linux kernel at LinuxTag '99 in Kaiserslautern,
Germany.

June 25, 1999
~~~~~~~~~~~~~
Helge Deller boots the first time Linux on a 715/64 PA-RISC machine
which promptly dies after displaying kernel version information. Much
celebration.

May 19-24, 1999
~~~~~~~~~~~~~~~
The Puffin Group hosts Bird of Feather session at Linux Expo, and
participates in HP's booth. Tremendous interest and support shown for
the project.

May 15, 1999
~~~~~~~~~~~~
HP, through the The Puffin Group, distributes A180C machines to developers.

March 9, 1999
~~~~~~~~~~~~~
HP releases first round of documentation on the basic boot up sequence
and PDC interfaces. See the [/documentation/index.html Documentation]
section on this page.

March 1999
~~~~~~~~~~
Hewlett-Packard Formally Sponsors Project - At Linux World Expo on March
1st, HP officially announces its support and endorsement of the PARISC
Linux project. They promised to provide marketing and technical
resources to the project, and talks began towards releasing
documentation and hardware through The Puffin Group.

HP, in conjunction with The Puffin Group, releases documents - On March
9th, HP releases documentation regarding the basic bootup sequence and
PDC interfaces. See the [/documentation/index.html Documentation]
section.

February 1999
~~~~~~~~~~~~~
Establishing the Framework - Christopher Beard sets up the web pages and
mailing lists, with Phil Schwan configuring the CVS repository. The web
pages are then added to several Linux development sites, and there is a
noticable increase in interest in the project.

The Cross-Compiler - Paul Lahaie joins the project, working with Alex
deVries on the cross-compiler, based on egcs 1.1.1 and the mkLinux
patches.

Boot Loader - Jason Eckhardt releases the first test boot image for
bootstrapping, Alex packages the rpmboot package, so now you can boot
*something*.

January 1999
~~~~~~~~~~~~
Getting Machines into the Hands of Developers - Neil Van Dyke offers up
a machine on loan to the project, with Michael Fratoni of Boston
assisting with shipping arrangements.

Martin Petersen joins the development team, with limited access to
several systems.

Work continues on getting machines to people who need them, as well as
getting the proper adapters for monitors and serial consoles.

October 1998
~~~~~~~~~~~~
The PARISC Linux Project Officially Begins - Conceived by Christopher
Beard and Alex deVries over lunch at the `Atlanta Linux Showcase
<http://linuxshowcase.org/>`__, with Travis Melhiser offering up a
machine to be used for development.

The Puffin Group Formally Sponsors the Project - The Puffin Group, a
Linux development and consulting firm, formally sponsors the project
providing server space and programming resources.

