====
TODO
====

PA-RISC status and TODO 2025-09-21
==================================

Kernel
------

- STARTED: Fix kernel: make ARCH=parisc tools/perf, needed by sysprof-48 and elfutils packages

- convert parisc arch code to use GENERIC_ENTRY

- Check graphics drivers / VisualizeFX with drivers from NetBSD - see mail thread
  "Graphics support" from Michael Lorenz on 12/16/24 https://lore.kernel.org/linux-parisc/20241216064156.6bbe1330@bushmills/
  Note: NetBSD drm manpage at https://man.netbsd.org/drm.4 says:
  drm is large and complicated and has no shortage of bugs.  On systems where
  graphics is not important, you may wish to use userconf(4) to disable the
  special-purpose drm drivers for your graphics device and fall back to vga(4) or
  genfb(4) with the default display configuration provided by firmware.

- Implement RUST (https://github.com/rust-lang/libc/pull/3542)

  - https://dreamcast.wiki/Rust_on_Dreamcast
  - https://github.com/rust-lang/rustc_codegen_gcc/issues/49
  - https://github.com/rust-lang/rustc_codegen_gcc/blob/master/Readme.md

- Port python-greenlet, needed by debian-installer (complicated!)

- QEMU testing, see mail from Guenter Roeck (12/6/23):

  - The e1000 and e1000-82544gc network interfaces don't work (those
    work fine with the 32-bit emulation)

  - ne2k_pci doesn't work anywhere. I get either a hang or a spinlock
    recursion error if I try.

  - hung task crashes in usb_start_wait_urb/usb_kill_urb during shutdown
    when booting from usb or when using an usb network interface

- STARTED: cacheflush(2) syscall was added to glibc, TODO: update man-pages

- Fix palo on c3000 to load initrd > 32MB

- Add parisc to https://www.qemu.org/docs/master/user/main.html#linux-user-space-emulator wiki

- ipmi-sensors: crashes A500: "ipmi-sensors --sensor-types=fan"

- Setup BTLB on secondary CPUs on 32-bit kernel

- Enable BlockTLB on PA1.x for STI framebuffer (see block tlb info in
  sti.pdf documentation) and ftp://parisc.parisc-linux.org/docs/pdc32.pdf

- check kselftest/nolibc:
  https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1c59d383390f970b891b503b7f79b63a02db2ec5

- Add core-objtool for parisc -
  http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=26660a4046b171a752e72a1dd32153230234fe3a

- Enhance PDC_CHASSIS driver to detect and report FAN and PSU issues.

- parisc timers: convert to generic clockevent (Arnd Bergmann) - https://patchwork.kernel.org/project/linux-parisc/patch/20201008154651.1901126-14-arnd@arndb.de/

- Kernel for qemu: Add CONFIG_NO_HZ to speed up qemu

- convert irq handling to use CONFIG_IRQ_DOMAIN_HIERARCHY (serial port
  problem, action handler msising when registering irq)

- NO_IRQ -> should be converted to (0) instead of (-1) -> Helge (check
  assembly, check in drivers/parisc/\*)

- STARTED: graphics mmap: Make mapping aliasing physical memory.

- STARTED: Visualizefx driver

- `Visualize FX framebuffer/DRM support (by Sven Schnelle) <https://patchwork.kernel.org/project/linux-parisc/patch/20211031204952.25678-2-svens@stackframe.org/>`__

- trace irq flags - http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=24131a61ec8c2d83431cb818281d3c9db986ab08

- ONGOING: Enable UBSAN and fix fallouts

- Enable SPARSEMEM_VMEMMAP_ENABLE

- STARTED: implement CONFIG_VMAP_STACK "virtually mapped stacks and
  thread_info cleanup" (see arm) -> https://lwn.net/Articles/693026/

- check usage of CONFIG_MLONGCALLS, only 14MB/s with debian kernel and
  dd if=/dev/zero of=here bs=1k count=100000 ?

- Implement TLB lazy_mmu_mode, see include/linux/pgtable.h and search
  for arch_leave_lazy_mmu_mode

- STARTED: :doc:`VDSO <vdso>` - Review gettimeofday and clock_gettime()
  implementation, possibly use a light-weight-syscall and rely on cr16
  and cpu speed for more accurate timing?

  - Enhance vDSO: gettimeofday(), getcpu(),... see
    e.g.c32c47aa364096124c9c69c1a44918433832562b

- stifb: Fix HCRX text scrolling, missing functions? - see latest
  patches to stifb

QEMU full system emulation
~~~~~~~~~~~~~~~~~~~~~~~~~~

- STARTED: Implement HPPA-64 support in qemu

- QEMU: Add support for running MPE with https://www.openpa.net (& Craig Lalley <mr_lalley@yahoo.com>)

- STARTED: Implement CPU reset: resettable_class_set_parent_phases(rc, NULL, hppa_cpu_reset_hold, NULL,...)
  https://lists.nongnu.org/archive/html/qemu-devel/2024-10/msg04876.html

- Mark Cave-Ayland <mark.cave-ayland@ilande.co.uk> offers to do contract
  work for QEMU through his company.

- Soumyajyotii Ssarkar <soumyajyotisarkar23@gmail.com> works on Google-Summer-of-Code 2025 projects:
  - STARTED: Finish LASI and 82596 NIC for QEMU -> https://github.com/hdeller/qemu-hppa/commits/lasi

  - STARTED: ncr53c710 driver from amiga: https://github.com/tonioni/WinUAE/blob/master/qemuvga/lsi53c710.cpp

- Check "HPPA support for IGNITE-UX install discs" for HP Logic
    Analyzer 16700A (Mail from Keith Monahan <keith@techtravels.org> Sept 2020)

- QEMU: Implement 32/24 bit HCRX graphics

- Fix migration of VMs (noticed by: Mark Cave-Ayland
  <mark.cave-ayland@ilande.co.uk>): Boot a qcow2 image in QEMU using the
  GTK, Wait a random time anywhere between 5-20s, Pause the VM, Switch
  to the "monitor" tab, Run "savevm foo" to save the current VM state as
  "foo", Type "quit" to exit QEMU, Add "-loadvm foo" to your QEMU
  command line and launch QEMU, Unpause the VM

QEMU user-mode emulation
~~~~~~~~~~~~~~~~~~~~~~~~

- Building curl hangs at end of package build in stunnel

- ONGOING: qemu-user brk() fixes:
  https://lists.nongnu.org/archive/html/qemu-devel/2023-07/msg04657.html

  - qemu brk testcases:

    - https://lore.kernel.org/qemu-devel/CACPK8XeyqcEDyyL3Jw2WYWs_gGdtTCf2=Ly04CMgkshSMdj7RA@mail.gmail.com/#t (armhf binary on a ppc64le host)

    - brk testcase in qemu commit: 4d1de87c75007ee7e29dd271ebb4afdcf01ad7aa

- check qemu-user build errors:

  - qemu can not emulate robust mutexes. That's why lmdb package and dependend
    packages like neomutt fail to build because the
    pthread_mutexattr_setpshared(PTHREAD_PROCESS_SHARED) call fails.
    Solution: Allow lmdb to fall back to MDB_USE_POSIX_SEM.

  - Check why raft package testcases fail

  - It looks like haskell packages still fail on qemu (e.g., haskell-swish).

  - https://buildd.debian.org/status/fetch.php?pkg=qtwebsockets-opensource-src&arch=hppa&ver=5.15.10-2&stamp=1688842351&raw=0

  - check vnlog: very easy testcase for clone/fork issue, see mail 07-31-2023.

- STARTED: kronosnet - port SCTP to qemu

- fix openvswitch on qemu

- check glibc tst-pthread-timedlock-lockloop testcase failure (The futex
  facility returned an unexpected error code., xclock_gettime.c:29:
  clock_gettime (0): Unknown error -1)

- Impossible: Implement io_submit() syscalls (not portable syscalls).

glibc
-----

- add backtrace() function, problems when building elfutils
  package: http://buildd.debian-ports.org/status/package.php?p=elfutils&suite=sid
  and dovecot https://buildd.debian.org/status/fetch.php?pkg=dovecot&arch=hppa&ver=1%3A2.3.19.1%2Bdfsg1-2%2Bb1&stamp=1666756774&raw=0

- STARTED: Finfish MADV transition in glibc:
  https://sourceware.org/pipermail/libc-alpha/2023-February/145452.html
  , dietlibc: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1030998

- STARTED: port :doc:`Gccgo_for_hppa <gccgo_for_hppa>` gccgo -> https://github.com/hdeller/gcc/tree/gccgo-hppa

executable stack
----------------

TODO: Convert gcc nested function trampoline
(pa_asm_trampoline_template) to don't need an executable stack

(see https://lore.kernel.org/all/87lebjz9z6.fsf@gentoo.org/) Starting
with Kernel 5.18, the parisc Linux kernel gained support to run without
executable stacks. In gcc-14 GNU stack notes were enabled. An executable
stack is still needed by applications for nested functions. They are
still called via a stack trampoline. The GNU stack note indicates
whether an object needs an executable stack or not. These notes are
collected by linker. The glibc loader determines whether to setup an
executable stack or not.

Once gcc-14 becomes the default compiler, we will have to enable GNU
stack notes in previous gcc versions. We will still have executable
stacks until everything is rebuilt. We will need to update that default
in Binutils too. Currently (nov 2023), there are no architecture
dependencies in the ld --enable-warn-execstack and
--enable-default-execstack configure options. The -z execstack and -z
noexecstack ld options can override the GNU notes, or lack thereof. We
may have to fix some assembly code. Maybe binutils should be built with
--enable-warn-execstack once we switch to gcc-14. I don't think we want
--enable-default-execstack after switching to gcc-14.

Debian
------

- ask to get palo-installer, partman-palo, aboot-installer into
  debian-installer (import from old Git-Backup, Alex Wirt?) - Mail from
  Adrian 05/30/23

gcc compiler
------------

- gcc: Add \_builtin_thread_pointer() and
  \_builtin_set_thread_pointer(), e.g.:
  https://gcc.gnu.org/ml/gcc-patches/2012-07/msg00428.html for glibc:
  ports/sysdeps/hppa/nptl/tls.h \__set_cr27()

- implement gcc builtins for break asm, like on other platforms, for usage in kernel

- Why is compiling fife so slow with cc1plus?

- STARTED: why is GNU AS so slow, e.g. when building freeorion,
  quantlib-swig or yade packages, testcases: openturns
  see https://sourceware.org/bugzilla/show_bug.cgi?id=18427

- SeaBIOS and palo: muldi3 and divdi3 (from libgcc.a) trashes fr22-fr25
  because of xmpyu instruction. Doesn't seem to have negative effect,
  but those registers should better be saved/restored in pdc_entry() and
  iodc_entry(), or libgcc should be fixed to not use floating point
  instructions.

- INEQUIVALENT ALIASES occur building gnuradio on debian.

- \_mcount can be optimized (smaller) by not subtracting offset)

- check if we can use -fentry from gcc for \_mcount optimization

- put PLABELS into read-only section (gcc function pa_reloc_rw_mask()
  needs modification, see "section mismatches" mails, 11.09.2017)

- http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=787192

- provide libphobos packages - gcc-defaults -
  http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=763103, Work by Dave:
  https://gcc.gnu.org/ml/gcc-testresults/2018-11/msg03460.html

- gcc -> join 32- and 64bit compiler, make "-m64" and "-m32" work and get rid of
  hppa64-linux-gnu-gcc

- **64-bit userspace support (from Dave Anglin, Nick Hudson)**

  - 64-bit userspace, :doc:`Binutils <binutils>` 64-bit binutils needs to
    be fixed to get multiple stub section support.

  - hppa64 can use the generic glibc thread code

  - 64-bit support in gcc is probably pretty good as 64-bit HP-UX works fine.

  - gdb could be a problem due to a lack of a maintainer.

- debian parisc perl bug
  (http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=721537) shows that
  mmap on parisc linux is horribly inefficient. We allocate huge maps
  for small files. This should be improved. Mailthread: " parisc mmap:
  private file maps",
  http://www.spinics.net/lists/linux-parisc/msg05083.html or
  https://rt.perl.org/Public/Bug/Display.html?id=119567
  https://rt.perl.org/Public/Bug/Display.html?id=119567

JAVA
----

- Finish OpenJDK, see patch and mail from Adrian Glaubitz 05.02.2021.

- IMPORTANT: fix OpenJDK - fixes required in zeroport (stack grows up!).

Rust language
-------------

- STARTED:
  https://github.com/rust-lang/libc/compare/master...hdeller:libc:hppa-devel2

- From Adrian Glaubitz (9/29/2021): The Rust project merged a change [1]
  that allows rustc to use GCC instead of LLVM as codegen backend for
  generating machine code through libgccjit. This means that we are in
  principal able now to build Rust code for all the architectures that
  GCC support but LLVM doesn't, which includes hppa. Please see this [2]
  and this [3] comment to see how to get started with cross-building the
  Rust compiler for a new target using libgccjit. We will have to add
  these new architectures to the libc crate first, see an example PR for
  that here [4]. Since I can't work on all these targets myself, I
  wanted to ask you to help me and get the support for hppa added to
  libc and rustc where necessary.

  - [1] https://github.com/rust-lang/rust/pull/87260
  - [2] https://github.com/rust-lang/rust/pull/87260#issuecomment-930221503
  - [3] https://github.com/rust-lang/rust/pull/87260#issuecomment-930223504
  - [4] https://github.com/rust-lang/libc/pull/2414

- In debian, stunnel4 requires python-cryptography package, and that was
  needed by something else I forgot...

kernel work
-----------

- STARTED: convert old-style rtc driver drivers/input/misc/hp_sdc_rtc.c
  to new RTC model, remove the procfs and miscdevice interfaces first
  and replace the ioctl with a struct rtc_class_ops. Arnd Bergmann can
  review those patches, but Alexandre and Alessandro are the ones who
  would merge them once the driver is moved to drivers/rtc. (Mail from
  Arnd Bergmann, 28.04.2016) - see https://patchwork.kernel.org/patch/10701397/

- Add some cond_resched() calls to avoid RCU stalls, see commit
  2a8bc5316adc998951e8f726c31e231a6021eae2

- Check if https://lkml.org/lkml/2020/7/23/1246 was added to avoid TLB
  stalls (commits https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c6fe44d96fc1536af5b11cd859686453d1b7bfd1
  and https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2a9127fcf2296674d58024f83981f40b128fffea) - kernel v5.9 seems good.

- ALPHA processor port:

  - Add https://lkml.org/lkml/2007/8/14/259 aboot patch

  - New one: https://patchwork.kernel.org/patch/10141217/ (alpha: fix
    crash if pthread_create races with signal delivery)

  - Kernel patches: http://marc.info/?t=140610652200001&r=1&w=2, The
    proposed fix is in: http://marc.info/?t=140675670100010&r=1&w=2,
    http://marc.info/?t=142327510200001&r=1&w=2

- How to upgrade libgcc
  ::

    apt install -f -o Dpkg::Options::="--force-overwrite"

  see: https://raphaelhertzog.com/2011/08/01/understanding-dpkgs-file-overwrite-error/

- megaraid on my rp5470 hangs at boot (maybe because SCSI chassis not
  connected)? -> use new megaraid driver for debian -> maybe patch
  https://patchwork.kernel.org/patch/7738911/ needed to enable PCI-PCI
  bridge?

- Fix FireGL X1/X3 in C8000 - currently broken (no DRM and software
  accel is incredibly slow [STRIKEOUT:plus colors in 24bpp mode are
  wrong] seems ok in debian 8 sid / 3.16.7 kernel) - Info: manual
  "Radeon R5xx Acceleration" -
  http://www.x.org/docs/AMD/R5xx_Acceleration_v1.1.pdf and generic:
  http://www.botchco.com/agd5f/?p=50

- maybe: optimize flush_dcache_page() implementation like sparc. See
  Documentation/cachetlb.txt, e.g. used in aio subsystem

- maybe implement transparent huge pages (due to cache coherency
  technically only possible on PA8500-PA8700 CPUs, not possible on
  PA8800-PA8900 CPUs)

- implement BUG() and WARN() with inline assembly so that interrupt
  happens in delay branch of "or,<> %r0,cond,%r0". Probably not
  possible, because gcc will report "control reaches end of non-void
  function" warnings/errors.

- disable FP-register save/restore at kernel entry? -
  https://patchwork.kernel.org/patch/3975291/

- very Low prio: enable 16k/64k kernel page sizes (still cacheflushing-issues with SCSI
  driver, additional work in glibc needed to deal with >4k page size,
  e.g. ld-loader alignments of data/code segments)

- Superdome support - can someone test? Should work better now with
  latest kernels (maybe ask Dennis Clarke <dclarke@blastwave.org>, Jan
  2019).

Debian packages / Userspace work
--------------------------------

- STARTED: Port grub2 to hppa? -> https://github.com/hdeller/grub

- Various big endian issues, see mail thread "s390x architecture status?": https://lists.debian.org/debian-devel/2024/10/msg00295.html

- WebKitGTK still builds in s390x, but the Skia graphics library does not support big-endian machines so if the Cairo backend is ever dropped then we probably won't be able to support s390x any longer:  https://github.com/WebKit/WebKit/blob/webkitgtk-2.47.1/Source/ThirdParty/skia/include/private/base/SkLoadUserConfig.h#L56

- teach "blkid" about palo partition

- Circular dependency problems with kde packages caused by vlc and
  ffmpeg (Dave)

- Solution: Rebuild ffmpeg outside the buildd (as some dependencies have
  probably been deleted) and upload a +b1 version. Then trigger bin-nmu
  of phonon package. Finally this resolves then the dependencies of vlc,
  ffmpeg, phonon, chromaprint, polkit-kde-agent-1, kwrited, kwallet-kf5
  and libkf5notifications-dev...

- Due to very bad performance running multiple copies of dot program
  (Dave)

- In vtk6 debian package, adding DOT_NUM_THREADS = 1 helps.

- Floating point NaN (see: https://en.wikipedia.org/wiki/NaN)

- PA-RISC uses "mips" NaN format which differs from x86, etc. It should
  be called PA-RISC format as I think PA was first. The quiet NaN bit is
  opposite to than in current IEEE standard.

- Debian packages with NaN issues: libime, `capnproto
  <https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=781787>`__,
  `python-shapely <https://buildd.debian.org/status/fetch.php?pkg=python-shapely&arch=hppa&ver=2.0.6-4%2Bb1&stamp=1735949853&raw=0>`__
  .. code-block:: pycon
  *     00800000017FF80000000000007FF80000000000007FF8000000000000
  * vs. 00800000017FF7FFFFFFFFFFFF7FF7FFFFFFFFFFFF7FF7FFFFFFFFFFFF

palo (PA-RISC Boot Loader)
--------------------------

- PALO - add regular rewind & load to avoid tftp boot timing issues. See
  mail from Frank Schreiner "PALO issues" (Dec 2018).

- When listing directory contents, resolve and print symlink target file
  names too, e.g. "vmlinux -> linux-5.12.1-64bit".

- Fix palo deb package to build iplboot natively
  (https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=851792)

- Modify (if possible), that palo can read (sequential)
  tftpbootable-vmlinuxes as well as the current boot-vmlinux images
  (simplifies build/handling)

Upstream PA-RISC-related kernel patch history
=============================================

.. _kernel_3.8:

Kernel 3.8
----------

- `unbreak automounter support on 64-bit kernel with 32-bit userspace <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4f4ffc3a5398ef9bdbb32db04756d7d34e356fcf>`__

.. _kernel_3.9:

Kernel 3.9
----------

- Integrate Dave Anglin's latest cacheflush patchset (fixes unusual userspace crashes) https://patchwork.kernel.org/patch/2480891/ - Dave
- add ucmpdi2() symbol to kernel - https://patchwork.kernel.org/patch/2468471/ - Dave
- patch to disable -mlong-calls compiler option when building kernel modules - https://patchwork.kernel.org/patch/2480001/
- James Bottomley's explanation of kernel gateway page - in 3.10
- lib/atomic64_test.c:131:2: warning: #warning Please implement atomic64_dec_if_positive for your architecture - in 3.10
- add interrupt statistics like x86 to /proc/interrupts - in 3.10
- kernel"-crashes in the area of do_softirq() - irq stacks implemented: https://patchwork.kernel.org/patch/2497501/
- test/build kernel with allmodconfig/allyesconfig
- error in lib/mpi/longlong.h,umul_ppmm() uses floating point -> https://patchwork.kernel.org/patch/2521621/
- update MAINTAINERS file, git tree -> https://patchwork.kernel.org/patch/2580161/
- lockdep issues with irqstack patch in UP configurations. Fixed -> https://patchwork.kernel.org/patch/2589321/
- add /proc/interrupt counters for floating point assist and unaligned handler traps -> https://patchwork.kernel.org/patch/2589161
- CONFIG_PREEMPT works now -> https://patchwork.kernel.org/patch/2624241/
- parisc: make default cross compiler search more robust (v3)
- parisc: fix SMP races when updating PTE and TLB entries in entry.S
- parisc: use long branch in fork_like macro
- parisc: only re-enable interrupts if we need to schedule or deliver signals when returning to userspace

.. _kernel_3.10:

Kernel 3.10
-----------

- stifb broken (PCI broken?) -> https://patchwork.kernel.org/patch/2636821/ -> 3.10-rc4
- parport hangs / lp -> https://patchwork.kernel.org/patch/2638331/ -> 3.10-rc4
- MAINTAINERS update -> https://patchwork.kernel.org/patch/2636601/ -> 3.10-rc4

.. _kernel_3.11:

Kernel 3.11
-----------

- parport (lp) fix
- defaultconfig for C8000
- C8000 workstation support in 3.11 and 3.10.X stable (serial port irq, IOMMU range fixes, FireGL radeon AGP fix, vma memory fix)
- remove arch/parisc/kernel/sys32.h
- pa_memcpy() was broken on 64bit (due to gcc miscompiling nonstandard C-code)

.. _kernel_3.12:

Kernel 3.12
-----------

- Fix 64bit SMP kernel on HP Visualize J-6750, 32bit SMP does work (Machine owners: Dave Land, Domenico Andreoli) -> fixed in 3.12 and backported >= kernel 2.6.12 (http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=54e181e073fc1415e41917d725ebdbd7de956455)
- fix kernel crash when running "echo t > /proc/sysrq-trigger" http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=59b33f148cc08fb33cbe823fca1e34f7f023765e
- regenerate defconfigs to include CONFIG_DEVTMPFS=y and CONFIG_DEVTMPFS_MOUNT=y to avoid udev/initrd problems. Done. http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=c1b14041aa86800cfd9e21b280b0eaee885fa623
- IMPI/BMC patch from Thomas Bogendoerfer - http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=fdbeb7de700a3c8fc85d7e595a6bf1c92a27c0a1 - test with: "ipmitool sdr"

.. _kernel_3.13:

Kernel 3.13
-----------

- STIcon broken on 64bit with > 4GB RAM - https://patchwork.kernel.org/patch/3149621/
- New defaultconfigs for 32- and 64-bit - https://patchwork.kernel.org/patch/2825923/, enabled BMC IPMI & disabled HP SDC for 64bit
- implement audit functionality, https://patchwork.kernel.org/patch/3046731/
- avoid dependencies on bzimage during make install, https://patchwork.kernel.org/patch/3069691/
- exception table entries can be of type int (32bit), even on 64bit kernel -> saves space. - https://patchwork.kernel.org/patch/3033801/

.. _kernel_3.14:

Kernel 3.14
-----------

- Native types in uapi/asm/stat.h
- **Dropped HP-UX Support: changed EWOULDBLOCK = EAGAIN ->** https://patchwork.kernel.org/patch/3499551/ (important ABI change!)
- parisc: Make EWOULDBLOCK be equal to EAGAIN on parisc
- sticon font problem fixed, sticon now automatically chooses the Linux fb fonts if available instead of using the HP fonts
- `parisc: add flexible mmap memory layout support <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9dabf60dc4abe6e06bebcc2ee46b4d76ec8741f2>`__
- parisc: convert uapi/asm/stat.h to use native types only
- parisc: wire up sched_setattr and sched_getattr
- parisc: fix cache-flushing

.. _kernel_3.15:

Kernel 3.15
-----------

- `epoll_pwait bug: debian packages libio-epoll-perl and python-falcon failed <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=ab3e55b119c9653b19ea4edffb86f04db867ac98>`__
- `pthread_create() out-of-memory bug, failed only when used with GNU make, e.g. zeromq3 package. Reason: stack hard limit problems <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b5f3c61d06165c3dddddbc30772f079c2c8589ca>`__
- `parisc: change value of SHMLBA from 0x00400000 to PAGE_SIZE <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=0ef36bd2b37815719e31a72d2beecc28ca8ecd26>`__ (backported to v3.13+)
- parisc: add renameat2 syscall
- parisc: Improve LWS-CAS performance
- parisc: ratelimit userspace segfault printing
- parisc: Use generic uapi/asm/resource.h file
- parisc: remove \_STK_LIM_MAX override
- parisc: fix epoll_pwait syscall on compat kernel
- parisc: change value of SHMLBA from 0x00400000 to PAGE_SIZE
- parisc: Replace \__get_cpu_var uses for address calculation
- parisc: locks: remove redundant arch\_*_relax operations
- parisc: wire up sys_utimes
- parisc: Remove unused CONFIG_PARISC_TMPALIAS code

.. _kernel_3.16:

Kernel 3.16
-----------

- parisc: Eliminate memset after alloc_bootmem_pages
- parisc: Remove SA_RESTORER define
- parisc: drop unused defines and header includes
- parisc: fix fanotify_mark() syscall on 32bit compat kernel
- parisc: add serial ports of C8000/1GHz machine to hardware database

.. _kernel_3.17:

Kernel 3.17
-----------

- parisc: Fix serial console for machines with serial port on superio chip
- parisc: Only use -mfast-indirect-calls option for 32-bit kernel builds
- parisc: pdc_stable.c: Avoid potential stack overflows
- parisc: pdc_stable.c: Cleaning up unnecessary use of memset in conjunction with strncpy
- parisc: ptrace: use secure_computing_strict()
- `parisc: Implement new LWS CAS supporting 64 bit operations, CAS implementation: implement 1,2,4 and 8 byte CAS <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=89206491201cbd1571009b36292af781cef74c1b>`__ (backported to v3.13+)
- parisc: Wire up seccomp, getrandom and memfd_create syscalls
- parisc: dino: fix %d confusingly prefixed with 0x in format string
- parisc: sys_hpux: NUL terminator is one past the end
- parisc: Use get_signal() signal_setup_done()

.. _kernel_3.18:

Kernel 3.18
-----------

- `parisc: Reduce SIGRTMIN from 37 to 32 to behave like other Linux architectures <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1f25df2eff5b25f52c139d3ff31bc883eee9a0ab>`__ (important ABI change!)
- parisc: Avoid using CONFIG_64BIT in userspace exported headers
- parisc: Use compat layer for msgctl, shmat, shmctl and semtimedop syscalls (backported to v3.10+)
- parisc: Use BUILD_BUG() instead of undefined functions
- parisc: Wire up bpf syscall

.. _kernel_3.19:

Kernel 3.19
-----------

- parisc: fix out-of-register compiler error in ldcw inline assembler function

.. _kernel_4.0:

Kernel 4.0
----------

- parisc: Wire up execveat syscall
- parisc: Add error checks when building up signal trampoline handler
- `parisc: hpux - Drop support for HP-UX binaries <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=04c1614977168fb8f002e2d81f704eeabe0c5ebd>`__
- parisc: hpux - Do not compile hpux subdirectory
- parisc: hpux - Delete files in hpux subdirectory
- parisc: hpux - Remove hpux gateway page
- parisc: hpux - Remove HPUX syscall numbers
- fs/binfmt_som: Drop kernel support for HP-UX SOM binaries
- parisc/uaccess: fix sparse errors
- parisc: macro whitespace fixes
- parisc: Add compile-time check when adding new syscalls
- parisc: Fix pmd code to depend on PT_NLEVELS value, not on CONFIG_64BIT
- parisc: mm: don't count preallocated pmds

.. _kernel_4.1:

Kernel 4.1
----------

- parisc: Replace PT_NLEVELS with CONFIG_PGTABLE_LEVELS
- parisc: Eliminate sg_virt_addr() and private scatterlist.h
- parisc: copy_thread(): rename 'arg' argument to 'kthread_arg'
- `parisc,metag: Fix crashes due to stack randomization on stack-grows-upwards architectures <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=d045c77c1a69703143a36169c224429c48b9eecd>`__ (backported to v3.16+)

  - DONE: fix VA randomization regarding to stack limits (testcase: ocaml build, temporarily fix: echo 0 > /proc/sys/kernel/randomize_va_space or add to /etc/sysctl.conf: sys.kernel.randomize_va_space = 0)

- parisc: %pf is only for function pointers
- parisc: copy_thread(): rename 'arg' argument to 'kthread_arg'

.. _kernel_4.2:

Kernel 4.2
----------

- parisc: don't use module_init for non-modular core pdc_cons code
- parisc: use for_each_sg()
- stifb: Implement hardware accelerated copyarea
- `parisc: Fix some PTE/TLB race conditions and optimize \__flush_tlb_range based on timing results <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=01ab60570427caa24b9debc369e452e86cd9beb4>`__ (backported to v3.18+)
- `parisc: mm: Fix a memory leak related to pmd not attached to the pgd <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=4c4ac9a48ac512c6b5a6cca06cfad2ad96e8caaa>`__ (backported to v4.0+)

.. _kernel_4.3:

Kernel 4.3
----------

- parisc: Define ioremap_uc and ioremap_wc
- `PCI,parisc: Enable 64-bit bus addresses on PA-RISC <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=e02a653e15d8d32e9e768fd99a3271aafe5c5d77>`__
- parisc: Additionally check for in_atomic() in page fault handler
- `parisc: Filter out spurious interrupts in PA-RISC irq handler <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b1b4e435e4ef7de77f07bf2a42c8380b960c2d44>`__ - fixes crashes due to serial ports at boot on C8000 with PA8900 CPUs (backported to all)
- parisc: Drop CONFIG_SMP around update_cr16_clocksource()
- parisc: Use platform_device_register_simple("rtc-generic")
- `parisc: Use double word condition in 64bit CAS operation <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=1b59ddfcf1678de38a1f8ca9fb8ea5eebeff1843>`__ - fixes 64bit gcc builtin atomics (backported to all)

.. _kernel_4.4:

Kernel 4.4
----------

- `parisc: Change L1_CACHE_BYTES to 16 <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=a01fece2e4185ac173abd16d10304d73d47ebf00>`__
- net/xps: Fix calculation of initial number of xps queues
- parisc: reduce syslog debug output
- parisc: serial/mux: Convert to uart_console_device instead of open-coded
- parisc: Wire up userfaultfd syscall
- parisc: allocate sys_membarrier system call number
- parisc: Drop hpux_stat64 struct from stat.h header file
- `parisc: Fixes and cleanups in kernel uapi header files <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=d0cf62fb63f760e98244d31396b3b58f3a1e326b>`__ (backported to 3.18+)
- parisc: Fix wrong comment regarding first pmd entry flags
- parisc: Drop definition of start_thread_som for HP-UX SOM binaries
- parisc: Drop unused MADV_xxxK_PAGES flags from asm/mman.h
- **parisc: Add defines for Huge page support**
- parisc: Initialize the fault vector earlier in the boot process.
- parisc: Increase initial kernel mapping to 32MB on 64bit kernel
- parisc: Use long branch to do_syscall_trace_exit
- **parisc: Add Huge Page and HUGETLBFS support**
- parisc: Map kernel text and data on huge pages
- parisc: Remove unused pcibios_init_bus()
- parisc: Wire up mlock2 syscall
- parisc: Disable huge pages on Mako machines
- `parisc iommu: fix panic due to trying to allocate too large region <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=e46e31a3696ae2d66f32c207df3969613726e636>`__ (backported to all)
- `parisc: Fix syscall restarts <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=71a71fb5374a23be36a91981b5614590b9e722c3>`__ (backported to all)

.. _kernel_4.5:

Kernel 4.5
----------

- parisc: Protect huge page pte changes with spinlocks
- parisc: Imporove debug info about space registers and TLB configuration
- parisc: Drop parisc-specific NSIGTRAP define
- `parisc: Fix \__ARCH_SI_PREAMBLE_SIZE <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=e60fc5aa608eb38b47ba4ee058f306f739eb70a0>`__ (backported to all)
- parisc: Reduce overhead of parisc_requires_coherency()
- parisc: Initialize PCI bridge cache line and default latency (related to https://patchwork.kernel.org/patch/7738911/)
- parisc: Use parentheses around expression in floppy.h
- `parisc: Fix ptrace syscall number and return value modification <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=98e8b6c9ac9d1b1e9d1122dfa6783d5d566bb8f7>`__ (backported to v4.0+)
- parisc: Wire up copy_file_range syscall

.. _kernel_4.6:

Kernel 4.6
----------

- parisc: Panic immediately when panic_on_oops
- parisc: Drop alloc_hugepages and free_hugepages syscalls
- parisc,metag: Implement CONFIG_DEBUG_STACK_USAGE option
- `parisc: Use generic extable search and sort routines <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=0de798584bdedfdad19db21e3c7aec84f252f4f3>`__
- parisc: Wire up preadv2 and pwritev2 syscalls
- `parisc: Fix SIGSYS signals in compat case <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=4f4acc9472e54ce702f1d85fc9e6d57767dec91f>`__ (backported to v4.5)
- `parisc: Fix and enable seccomp filter support <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=910cd32e552ea09caa89cdbe328e468979b030dd>`__ (backported to v4.5)
- `parisc: Handle R_PARISC_PCREL32 relocations in kernel modules <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=592570c950fb455226cd255603de7cede2be83e6>`__
- `parisc: Avoid function pointers for kernel exception routines <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=e3893027a300927049efc1572f852201eb785142>`__ - backported to all
- `parisc: Fix kernel crash with reversed copy_from_user() <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=ef72f3110d8b19f4c098a0bff7ed7d11945e70c6>`__ - backported to all
- `parisc: Unbreak handling exceptions from kernel modules <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=2ef4dfd9d9f288943e249b78365a69e3ea3ec072>`__ - backported to all
- parisc: Update comment regarding relative extable support
- `parisc: Fix ftrace function tracer <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=366dd4ea9d5f0eb78fdf4982d76506f99480ec0a>`__ (not backported, maybe it should have been?)
- `parisc: fix a bug when syscall number of tracee is \__NR_Linux_syscalls <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=f0b22d1bb2a37a665a969e95785c75a4f49d1499>`__ (backported to v4.0+)

.. _kernel_4.7:

Kernel 4.7
----------

- parisc: Fix typo in pdc.h
- parisc: Fix typo in ldcw.h
- parisc: Fix typos in eisa_eeprom.h
- parisc: Fix typo in fpudispatch.c
- `parisc: Add syscall tracepoint support <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=fc79168a7c75423047d60a033dc4844955ccae0b>`__
- parisc: Simplify and speed up get_user() and put_user()
- parisc: Add 64bit get_user() and put_user() for 32bit kernel
- `parisc: Add ARCH_TRACEHOOK and regset support <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=64e2a42bca12e408f0258c56adcf3595bcd116e7>`__
- `parisc: Add native high-resolution sched_clock() implementation <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=54b668009076caddbede8fde513ca2c982590bfe>`__
- parisc: Merge ftrace C-helper and assembler functions into .text.hot section
- parisc: Use long jump to reach ftrace_return_to_handler()
- parisc: Whitespace cleanups in unistd.h
- parisc: Update futex.h to match generic implementation
- parisc: Fix printk time during boot
- `parisc: Fix pagefault crash in unaligned \__get_user() call <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=8b78f260887df532da529f225c49195d18fef36b>`__ - backported to all
- parisc: Move die_if_kernel() prototype into traps.h header
- `parisc: Fix backtrace on PA-RISC <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=be24a89700eef61bedaba40f3b05ef07f5806e38>`__
- `53c700: fix BUG on untagged commands <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=8beb330044d0d1878c7b92290e91c0b889e92633>`__ - fixes SCSI on 712/715 machines with sym7xx driver (backported to 4.6)

.. _kernel_4.8:

Kernel 4.8
----------

- parisc: Change structure intialisation to C99 style in iomap.c
- `parisc: Add <asm/hash.h> <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=773e1c5fa4bf1faa25e119490b26ece2ef1bdb46>`__
- parisc: Add break statements to pdc_pat_io_pci_cfg_read()
- `parisc: Fix automatic selection of cr16 clocksource <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=ae141830b118c3fb5b7eab6fa7c8ab7b7224b0a4>`__ (backported to 4.7)
- `parisc: Fix order of EREFUSED define in errno.h <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=3eb53b20d7bd1374598cfb1feaa081fcac0e76cd>`__ (backported to all)

.. _kernel_4.9:

Kernel 4.9
----------

- `parisc: Increase KERNEL_INITIAL_SIZE for 32-bit SMP kernels <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=690d097c00c88fa9d93d198591e184164b1d8c20>`__ (backported to 4.4+)
- `parisc: Drop bootmem and switch to memblock <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=4fe9e1d957e45ad8eba9885ee860a0e93d13a7c7>`__
- `parisc: Add hardened usercopy feature <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=9e91db6b4abecd58647a5e984d538187f1c2ea09>`__
- parisc: Add cfi_startproc and cfi_endproc to assembly code
- parisc: Move hpmc stack into page aligned bss section
- `parisc: Fix self-detected CPU stall warnings on Mako machines <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=92420bd0d01f040bbf754e1d090be49ca6a1c8d6>`__ (backported to 4.7+)
- `parisc: Report trap type as human readable string <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b391667eb45a5a888bc9372462c5f647418c31af>`__
- parisc: Update comment regarding implementation of copy_user_page_asm
- parisc: Use kmalloc_array() in add_system_map_addresses()
- parisc: Check return value of smp_boot_one_cpu()
- `parisc: Drop BROKEN_RODATA config option <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b5d5cf2b8a68618a8ec646cab5746e2f539dc244>`__
- `parisc: Increase initial kernel mapping size <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=65bf34f59594c11f13d371c5334a6a0a385cd7ae>`__ (backported to v4.4+)
- `parisc: Fix kernel memory layout regarding position of \__gp <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=f8850abb7ba68229838014b3409460e576751c6d>`__ (backported to v4.4+)
- parisc: Move exception table into read-only section
- parisc: Zero-initialize newly alloced memblock
- `parisc: Show trap name in kernel crash <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=0a862485f42af7df530b0b5f0e5ba8ffbb3a4a12>`__
- parisc: Migrate exception table users off module.h and onto extable.h
- parisc: use KERN_CONT when printing device inventory
- parisc: Use LINUX_GATEWAY_ADDR define instead of hardcoded value
- parisc: Ignore the pkey system calls for now
- `parisc: Avoid trashing sr2 and sr3 in LWS code <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=f4125cfdb3008363137f744c101e5d76ead760ba>`__
- `parisc: Ensure consistent state when switching to kernel stack at syscall entry <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=6ed518328d0189e0fdf1bb7c73290d546143ea66>`__ (backported to all)
- parisc: Fix printk continuations in system detection
- `parisc: Switch to generic sched_clock implementation <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=43b1f6abd59063a088416a0df042b36450f91f75>`__ (backported to v4.7+)
- `parisc: Fix races in parisc_setup_cache_timing() <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=741dc7bf1c7c7d93b853bb55efe77baa27e1b0a9>`__ (backported to v3.18+)
- `parisc: Fix race in pci-dma.c <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=c0452fb9fb8f49c7d68ab9fa0ad092016be7b45f>`__ (backported to v3.16+)
- `parisc: Also flush data TLB in flush_icache_page_asm <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=5035b230e7b67ac12691ed3b5495bbb617027b68>`__ (backported to v3.16+)
- `parisc: Fix TLB related boot crash on SMP machines <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=24d0492b7d5d321a9c5846c8c974eba9823ffaa0>`__ (backported to v3.18+)
- `parisc: Purge TLB before setting PTE <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=c78e710c1c9fbeff43dddc0aa3d0ff458e70b0cc>`__ (backported to v3.16+)
- `parisc: Remove unnecessary TLB purges from flush_dcache_page_asm and flush_icache_page_asm <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=febe42964fe182281859b3d43d844bb25ca49367>`__ (backported to v3.16+)

.. _kernel_4.10:

Kernel 4.10
-----------

- parisc: perf: return -EFAULT on error
- `parisc: Enable KASLR <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=18d98a79382cbe5a7569788d5b7b18e7015506f2>`__
- `parisc: Re-enable interrupts early <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=5c38602d83e584047906b41b162ababd4db4106d>`__
- `parisc: Enhance CPU detection code on PAT machines <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=637250cc8f5e2bfc5c3a96802d1d96b4987a1d62>`__
- `parisc: Optimize timer interrupt function <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=160494d381373cfa21208484aea4e5db2d3cb0a8>`__
- `parisc: Mark cr16 clocksource unstable on SMP systems <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=41744213602a206f24adcb4a2b7551db3c700e72>`__ (backported to v4.8+)
- `parisc: Drop TIF_RESTORE_SIGMASK and switch to generic code <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=1fe0a7e0bc52024a445945c9e7691551aba97390>`__
- `parisc: Add line-break when printing segfault info <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b4a9eb4cd5966c8aad3d007d206a2cbda97d6928>`__ (backported to v4.9)
- `parisc, parport_gsc: Fixes for printk continuation lines <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=83b5d1e3d3013dbf90645a5d07179d018c8243fa>`__
- `parisc: Don't use BITS_PER_LONG in userspace-exported swab.h header <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=2ad5d52d42810bed95100a3d912679d8864421ec>`__ (backported to all, build-fix for qemu)

.. _kernel_4.11:

Kernel 4.11
-----------

- parisc: eisa: Remove coding style errors
- parisc: eisa: Fix resource leaks in error paths
- parisc: ccio-dma: Handle return NULL error from ioremap_nocache
- parisc: fix a printk
- parisc: Define access_ok() as macro
- `parisc: Remove flush_user_dcache_range and flush_user_icache_range <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=ef470a60e10eb12635d7b84c9502cea3028d44e8>`__
- parisc: perf: Fix potential NULL pointer dereference
- parisc: Wire up statx system call
- parisc: Avoid compiler warnings with access_ok()
- `parisc: Fix system shutdown halt <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=73580dac7618e4bcd21679f553cf3c97323fec46>`__
- `parisc: Optimize flush_kernel_vmap_range and invalidate_kernel_vmap_range <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=316ec0624f951166daedbe446988ef92ae72b59f>`__
- `parisc: support R_PARISC_SECREL32 relocation in modules <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5f655322b1ba4bd46e26e307d04098f9c84df764>`__ (backported to v4.10+)
- `parisc: Fix access fault handling in pa_memcpy() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=554bfeceb8a22d448cd986fc9efce25e833278a1>`__ (backported to v4.9+)
- `parisc: Clean up fixup routines for get_user()/put_user() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=554bfeceb8a22d448cd986fc9efce25e833278a1>`__ (backported to v4.9+)
- `parisc: Avoid stalled CPU warnings after system shutdown <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=476e75a44b56038bee9207242d4bc718f6b4de06>`__ (backported to v4.9+)
- `parisc: fix bugs in pa_memcpy <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=409c1b250e30ad0e48b4d15d7319b4e18c046c4f>`__ (backported to v4.9+)
- `parisc: Fix get_user() for 64-bit value on 32-bit kernel <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3f795cef0ecdf9bc980dd058d49bdab4b19af1d3>`__ (backported to v4.9+)

.. _kernel_4.12:

Kernel 4.12
-----------

- (none beside backports, pull request was too late during merge window)

.. _kernel_4.13:

Kernel 4.13
-----------

- `parisc: use compat_sys_keyctl() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b0f94efd5aa8daa8a07d7601714c2573266cd4c9>`__ (backported to all)
- parisc: Inline trivial exception code in lusercopy.S
- parisc: Drop per_cpu uaccess related exception_data struct
- `parisc: Enhance detection of synchronous cr16 clocksources <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c8c3735997a3aa184fa81742bb6c4062a26af2f3>`__
- `parisc: Add Page Deallocation Table (PDT) support <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c9c2877d08d9aa0ca0a5c227ac795fbb76269300>`__
- `parisc/mm: Ensure IRQs are off in switch_mm() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=649aa24254e85bf6bd7807dd372d083707852b1f>`__ (backported to v4.7+)
- parisc: Avoid zeroing gr[0] in fixup_exception()
- parisc: Don't hardcode PSW values in gsc\_*() functions
- parisc: Don't hardcode PSW values in hpmc code
- `parisc: Report SIGSEGV instead of SIGBUS when running out of stack <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=247462316f85a9e0479445c1a4223950b68ffac1>`__ (backported to all, fixes libsigsegv package)
- `parisc: DMA API: return error instead of BUG_ON for dma ops on non dma devs <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=33f9e02495d15a061f0c94ef46f5103a2d0c20f3>`__ (backported to v3.13+, fixes parport_pc module crash)
- parisc: ->mapping_error
- parisc: pdc_stable: constify attribute_group structures.
- `parisc: Disable further stack checks when panic occurs during stack check <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5bc64bd246ca00e0bb0b7137afaf586f2f66a911>`__
- `parisc: Merge millicode routines via linker script <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6cd819e8e978b361ae558056a9e79fd30e6acb0d>`__
- parisc: regenerate defconfig files
- `parisc: Fix crash when calling PDC_PAT_MEM PDT firmware function <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=f520e55241e1cf0c10d308ccf47513f28533f60a>`__
- parisc: Add function to return DIMM slot of physical address
- parisc: Show DIMM slot number which holds broken memory module
- `parisc: Suspend lockup detectors before system halt <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=56188832a50f09998cb570ba3771a1d25c193c0e>`__ (backported to v4.9+)
- `parisc: Prevent TLB speculation on flushed pages on CPUs that only support equivalent aliases <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=ae7a609c34b6fb12328c553b5f9aab26ae74a28e>`__ (backported to v4.9+)
- `parisc: Extend disabled preemption in copy_user_page <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=56008c04ebc099940021b714da2d7779117cf6a7>`__
- `parisc: Define CONFIG_CPU_BIG_ENDIAN <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=74ad3d28af2104b92dd83a43add79e6a8c45d8e2>`__
- `parisc: Increase thread and stack size to 32kb <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8f8201dfed91a43ac38c899c82f81eef3d36afd9>`__ (backported to v4.11+)
- `parisc: pdc_stable: Fix locking when creating sysfs links <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=93964fd4ea6ab86a2d2853a9ae56ae0c24cbbe16>`__
- `parisc: Handle vma's whose context is not current in flush_cache_range <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=13d57093c141db2036364d6be35e394fc5b64728>`__ (backported to v4.9+)
- printk-formats.txt: Better describe the difference between %pS and %pF
- `parisc: pci memory bar assignment fails with 64bit kernels on dino/cujo <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4098116039911e8870d84c975e2ec22dab65a909>`__ (backported to all)

.. _kernel_4.14:

Kernel 4.14
-----------

- parisc: Add MADV_HWPOISON and MADV_SOFT_OFFLINE
- `parisc: PDT/firmware: Add support to read PDT on older PAT-machines <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8a5aa00e6b9630d36d7681b38062a3d48d8b6ee6>`__
- `parisc: PDT: Add full support for memory failure via Page Deallocation Table (PDT) <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=54ac8fcbd6e6777929235ac767467991005d2df3>`__
- parisc: Static initialization of spinlocks in perf and unwind code
- parisc: Drop exception_data struct
- parisc: Static initialization of pcxl_res_lock spinlock
- parisc: Drop MADV_SPACEAVAIL, MADV_VPS_PURGE and MADV_VPS_INHERIT
- parisc/random: Add machine specific randomness
- `parisc: Enable UBSAN support <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e35a2ce692821563583fa8b11ddf4acb24e54580>`__
- `parisc: Add core code for self-extracting kernel <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2f3c7b8137ef6a3f90850d7cb5dbd05d8940403a>`__
- parisc: Make existing core files reuseable for bootloader
- `parisc: Wire up support for self-extracting kernel <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b6adc16e08e0ee8bbc9cccf7af3882b6e1cb30c2>`__
- parisc/harmony: Fix section mismatches
- parisc/sticore: Fix section mismatches
- parisc/mux: Fix section mismatches
- parisc/8250_gsc: Fix section mismatches
- parisc/scsi/zalon: Fix section mismatches
- parisc/scsi/lasi700: Fix section mismatches
- parisc/parport_gsc: Fix section mismatches
- parisc: Fix section mismatches in parisc core drivers
- parisc/serio: Fix section mismatches in gscps2 and hp_sdc drivers
- parisc/net/lasi_82596: Fix section mismatches
- parisc/input/hilkbd: Fix section mismatches
- parisc/ipmi_si_intf: Fix section mismatches on parisc platform
- parisc/core: Fix section mismatches
- `parisc: Fix up devices below a PCI-PCI MegaRAID controller bridge <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d81f734462d439291531266c4004ab97e9a033cc>`__
- printk-formats.txt: Add examples for %pF and %pS usage
- parisc: Optimize switch_mm
- parisc: Fix too large frame size warnings
- `parisc: Stop unwinding at start of stack <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e77900abfd8be4e207412d8b7752dbb9838e2571>`__
- parisc: Move start_parisc() into init section
- parisc: Add wrapper for pdc_instr() firmware function
- parisc: Add PDCE_CHECK instruction to HPMC handler
- parisc: Check if initrd was loaded into broken RAM
- parisc: Move init_per_cpu() into init section
- `parisc: Add HWPOISON page fault handler code <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=606f95e4255845155f62504a9e1f12665b1853c8>`__
- `parisc: Reintroduce option to gzip-compress the kernel <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=af21b01d1166248f282fc02d0f459c94de06615e>`__
- `parisc: Unbreak bootloader due to gcc-7 optimizations <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8c031ba63f8f2a9efc471cb45b2ff18271556544>`__
- parisc: Export \__cmpxchg_u64 unconditionally
- `parisc: Fix detection of nonsynchronous cr16 cycle counters <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8642b31ba9eef8a01845146a26682d4869e62513>`__ (backported to v4.13+)
- `parisc: Fix double-word compare and exchange in LWS code on 32-bit kernels <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=374b3bf8e8b519f61eb9775888074c6e46b3bf0c>`__ (backported to all/v3.13+)

.. _kernel_4.15:

Kernel 4.15
-----------

- arch: Fix duplicates in Kconfig for parisc and sparc
- `parisc: Make some PDC structures accessible in uapi headers <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=bc5a768e567df51f43778da381477f6555f3824c>`__
- `parisc: Pass endianness info to sparse <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3744d988c05a27f40408003352841aefadba1324>`__
- `parisc: Add CPU topology support <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=bf7b4c1b3c92f246a535a7c792177041d0442011>`__
- `parisc: Fix validity check of pointer size argument in new CAS implementation <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=05f016d2ca7a4fab99d5d5472168506ddf95e74f>`__ (backported to all/v3.13+)
- parisc: Fix indenting in puts() (backported to v4.14)
- parisc: Align os_hpmc_size on word boundary (backported to v4.14)
- `parisc: Hide Diva-built-in serial aux and graphics card <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=bcf3f1752a622f1372d3252d0fea8855d89812e7>`__ (backported to all/v3.0+) - **important for rp3410/rp3440 machines**
- `Revert "parisc: Re-enable interrupts early" <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9352aeada4d8d8753fc0e414fbfe8fdfcb68a12c>`__ (backported to v4.10+)
- `parisc: Reduce thread stack to 16 kb <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=da57c5414f49ef9e4bcb9ae0bbafd1d650b31411>`__
- parisc: remove duplicate includes
- parisc: Show unhashed hardware inventory
- parisc: Show initial kernel memory layout unhashed
- parisc: Show unhashed HPA of Dino chip
- parisc: Show unhashed EISA EEPROM address
- `parisc: Fix alignment of pa_tlb_lock in assembly on 32-bit SMP kernel <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=88776c0e70be0290f8357019d844aae15edaa967>`__ (backported to v4.0+)
- `parisc: qemu idle sleep support <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=310d82784fb4d60c80569f5ca9f53a7f3bf1d477>`__ (backported to v4.9+)

.. _kernel_4.16:

Kernel 4.16
-----------

- `parisc: Hide virtual kernel memory layout <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=fd8d0ca2563151204f3fe555dc8ca4bcfe8677a3>`__ (backported to v4.15)
- `parisc: Check if secondary CPUs want own PDC calls <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=0ed1fe4ad394e54783bbffa84102faf435661a2e>`__
- `parisc: Use cr16 interval timers unconditionally on qemu <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5ffa8518851f1401817c15d2a7eecc0373c26ff9>`__ (backported to v4.9+)
- `parisc: Reduce irq overhead when run in qemu <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=636a415bcc7f4fd020ece8fd5fc648c4cef19c34>`__ (backported to v4.14+)
- `parisc: Fix ordering of cache and TLB flushes <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=0adb24e03a124b79130c9499731936b11ce2677d>`__ (backported to v4.9+)
- `parisc: Handle case where flush_cache_range is called with no context <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9ef0f88fe5466c2ca1d2975549ba6be502c464c1>`__ (backported to v4.9+)

.. _kernel_4.17:

Kernel 4.17
-----------

- parisc: Silence uninitialized variable warning in dbl_to_sgl_fcnvff()
- parisc/stifb: Use fb_memset() to avoid sparse warning
- parisc/parport_gsc: Use NULL to avoid sparse warning
- parisc/led: Fix sparse warnings
- parisc/gscps2: Fix sparse warnings
- parisc: Force to various endian types for sparse
- `parisc: Convert MAP_TYPE to cover 4 bits on parisc <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d5b59a71204b0a9a55eda633347b1214642bead3>`__
- parisc: Move various functions and strings to init section
- parisc/Kconfig: SMP kernels boot on all machines
- `parisc: machine_power_off() should call pm_power_off() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=24002d59376e2d19a2a523fe88d5b32e691a43af>`__
- parisc: Directly call machine_power_off() in power button driver
- `parisc: Fix HPMC handler by increasing size to multiple of 16 bytes <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d5654e156bc4d68a87bbaa6d7e020baceddf6e68>`__
- `parisc/pci: Switch LBA PCI bus from Hard Fail to Soft Fail mode <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b845f66f78bf42a4ce98e5cfe0e94fab41dd0742>`__ (backported to all kernels: 3.18.111 4.4.134 4.9.104 4.14.45 4.16.13 4.17)
- `parisc: Add code generator for Qemu/SeaBIOS machine info <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a77ab0e7ce232a62adac3d85b9ae66d0f48385ac>`__
- parisc: Fix out of array access in match_pci_device()
- `ipmi/parisc: Add IPMI chassis poweroff for certain HP PA-RISC and IA-64 servers <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c6185e285c5c7cfeab739bae7f206ced695f09c7>`__
- parisc/signal: Add FPE_CONDTRAP for conditional trap handling
- parisc: Move cache flush functions into .text.hot section
- `parisc: Switch to generic COMPAT_BINFMT_ELF <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=71d577db01a5177c7807a2f3d3df9bab9d21c500>`__
- `parisc: Prevent panic at system halt <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=67698287031be7c1821f9b151237ca8cdb231fd1>`__
- `parisc: time: Convert read_persistent_clock() to read_persistent_clock64() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=f76cdd00ef0e39d880139b074e3b247594dff95a>`__
- parisc: Make bzImage default build target
- parisc: Document rules regarding checksum of HPMC handler
- parisc: drivers.c: Fix section mismatches
- parisc: Fix section mismatches

.. _kernel_4.18:

Kernel 4.18
-----------

- parisc: Convert printk(KERN_LEVEL) to pr_lvl()
- `parisc: Drop struct sigaction from not exported header file <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=704e38303153c797d66c41bbe8325202f549b53c>`__
- `parisc: Mark 16kB and 64kB page sizes BROKEN <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1c971f39e62222d567f179ccaa1a186f1c203b52>`__
- `parisc: Default to 4 SMP CPUs <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=435d34c7a48de5e89047ef9c7dce6528831b258b>`__
- `parisc: Wire up io_pgetevents syscall <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2765b3edc41bdf18960ca7e6b656fb933ac191d6>`__
- `parisc: Reduce debug output in unwind code <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=63ba82c0e63f1dd400d84e12f2142c2cb691aec1>`__
- `parisc: Build kernel without -ffunction-sections <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=24b6c22504a27210a8377e54d24d425ae414f2c1>`__
- `parisc: Enable CONFIG_MLONGCALLS by default <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=66509a276c8c1d19ee3f661a41b418d101c57d29>`__ (backported to v4.0+)
- `parisc: Define mb() and add memory barriers to assembler unlock sequences <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=fedb8da96355f5f64353625bf96dc69423ad1826>`__ (backported to v4.0+)

.. _kernel_4.19:

Kernel 4.19
-----------

- `parisc: merge pcx_dma_ops and pcxl_dma_ops <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a34a9b9682a5a9de53386ed0872c7d38ca9d6b38>`__
- `parisc: always use flush_kernel_dcache_range for DMA cache maintainance <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7f1501053811414ddeff63db8f5d41bdbe38068f>`__
- `parisc: use generic dma_noncoherent_ops <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c1f59375b3782f478ac2c488889abdc00dd8e25f>`__
- `parisc: Drop architecture-specific ENOTSUP define <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=93cb8e20d56be40c541475f77b5f565fbb385a4b>`__
- parisc: Add HAVE_REGS_AND_STACK_ACCESS_API feature
- `parisc: Fix and improve kernel stack unwinding <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c8921d72e390cb6fca3fb2b0c2badfda851647eb>`__
- `parisc: Remove ordered stores from syscall.S <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7797167ffde1f00446301cb22b37b7c03194cfaf>`__ (backported to v4.0+)
- `parisc: Remove unnecessary barriers from spinlock.h <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3b885ac1dc35b87a39ee176a6c7e2af9c789d8b8>`__ (backported to v4.0+)
- `parisc: prefer \_THIS_IP\_ and \_RET_IP\_ statement expressions <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4a53ec1ccf932f86845c96a5092ba4507621c84d>`__
- `parisc: Restore possibility to execute 64-bit applications <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5b00ca0b8035e49ef7c466e959c5cb457a654351>`__
- `parisc: Fix ptraced 64-bit applications to call 64-bit syscalls <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b6fc0cccb6b35815a7d1cfc9279cdbfc2c61d00d>`__
- `parisc: Update comments in syscall.S regarding wide userland <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=54c770da992387abfa7df4817404d000ef034fa8>`__
- `parisc: Consolidate unwind initialization calls <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9e0d5c451f9e559dd06af3fff49a0d2068c634c4>`__
- `parisc: Fix boot failure of 64-bit kernel <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8801ccb9fa524c195322c26b6d44e99827772bde>`__
- `parisc: Add hardware description to stack traces <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=dbf2a4b1ffab2867505be3b24221d5efa2200c91>`__
- parisc: Fix uninitialized variable usage in unwind.c

.. _kernel_4.20:

Kernel 4.20
-----------

- parisc: remove the dead ccio-rm-dma driver
- parisc: Use PARISC_ITLB_TRAP constant in entry.S
- `parisc: Add SYSTEM_INFO and REGISTER TOC PAT functions <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=fe8376dbbd6ac1febb0fd6389e3ec4f349e70c71>`__
- parisc: Clean up crash header output
- parisc: dino: Utilize DINO_MASK_IRQ() macro
- parisc: Ratelimit dino stuck interrupt warnings
- `parisc: Fix map_pages() to not overwrite existing pte entries <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3c229b3f2dd8133f61bb81d3cb018be92f4bba39>`__ (backported to all)
- `parisc: Fix exported address of os_hpmc handler <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=99a3ae51d557d8e38a7aece65678a31f9db215ee>`__ (backported to 4.9+)
- `extract-vmlinux: Check for uncompressed image as fallback <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=db139d71c4c377c56a67ae219f120186ce9934d1>`__
- `parisc: Include compressed vmlinux file in vmlinuz boot kernel <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=34c201ae49fe9e0bf3b389da5869d810f201c740>`__
- `parisc: Add alternative coding infrastructure <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3847dab77421867fbc77faacb2f377d44e729e1b>`__
- parisc: Use zdep for shlw macro on PA1.1 and PA2.0
- parisc: Drop two instructions from pte lookup code
- parisc: Add PDC PAT cell_info() and pd_get_pdc_revisions() functions
- parisc: Retrieve and display the PDC PAT capabilities
- `parisc: Reorder TLB flush timing calculation <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a886c9791aed64d73f8c0038cc1506741ba216fa>`__
- parisc: Remove PTE load and fault check from L2_ptep macro
- `parisc: Release spinlocks using ordered store <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d27dfa13b9f77ae7e6ed09d70a0426ed26c1a8f9>`__
- parisc: Purge TLB entries after updating page table entry and set page accessed flag in TLB handler
- `parisc: Fix address in HPMC IVA <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1138b6718ff74d2a934459643e3754423d23b5e2>`__ (backported to all)
- parisc: Remove pte_inserted define
- parisc: Optimze cache flush algorithms
- parisc: remove check for minimum required GCC version
- `parisc: Fix A500 boot crash <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c9fa406f62ec952bc4689b5120a02759ce42a68d>`__
- `parisc: Use LINUX_GATEWAY_SPACE constant in entry.S <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=87613bb9d20c556b5eeae04f4caf40701189f07b>`__
- `parisc64: change \__kernel_suseconds_t to match glibc <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9a298b445514b3de08252c71833f9273b7727355>`__
- parisc: Revert "Release spinlocks using ordered store"
- `parisc: Enable -ffunction-sections for modules on 32-bit kernel <http://git.kernel.org/pub/scm/linux/kernel/git/deller/parisc-linux.git/commit/?h=parisc-4.20-4&id=1e8249b8a4e960018e4baca6b523b8a4500af600>`__ (backported to v4.18+)

.. _kernel_5.0:

Kernel 5.0
----------

- parisc: move \__IGNORE\* entries to non uapi header
- parisc: add \__NR_syscalls along with \__NR_Linux_syscalls
- parisc: remove \__NR_Linux from uapi header file.
- `parisc: add system call table generation support <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=85e69701f58c9b2ec2975e31518b60e056bfebdf>`__
- parisc: generate uapi header and system call table files
- parisc: syscalls: ignore nfsservctl for other architectures
- `parisc: Split out alternative live patching code <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8cc28269b9574103aae9088a02a20929ec75f983>`__
- parisc: Fix serio address output
- parisc: Fix HP SDC hpa address output
- `parisc: Remap hugepage-aligned pages in set_kernel_text_rw() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=dfbaecb2b707cfdc5276b548d52b437384bd6483>`__ (backported to 4.20)
- `parisc: Fix ptrace syscall number modification <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b7dc5a071ddf69c0350396b203cba32fe5bab510>`__ (backported to 4.5+)
- CREDITS/MAINTAINERS: Retire parisc-linux.org email domain

.. _kernel_5.1:

Kernel 5.1
----------

- parisc: don't include <asm/cacheflush.h> in <asm/dma-mapping.h>
- parisc: move internal implementation details out of <asm/dma-mapping.h>
- parisc: turn GET_IOC into an inline function
- parisc: properly type the iommu field in struct pci_hba_data
- parisc: properly type the return value of parisc_walk_tree
- parisc/dino: use container_of in DINO_DEV
- parisc/lba_pci: use container_of in LBA_DEV
- parisc: remove the HBA_DATA macro
- `parisc: Hide built-in serial aux port of Tosca GSP card <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=81fe5bba88a3fbe34ee6c5d18e327c6aaacd588c>`__
- parisc: Show rescheduling interrupts on SMP machines only
- `parisc: Count IPI function call interrupts <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b102f29b2d8603f15071384e88125b8eab5f8faa>`__
- `parisc: Improve initial IRQ to CPU assignment <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=f73493eb4aecdd6edd8a749890e6e2dcfa0a7978>`__
- [STRIKEOUT:parisc: Use F_EXTEND() macro in iosapic code] (reverted later on)
- parisc: Add constants for PDC_RELOCATE PDC call
- `parisc: Show machine product number during boot <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8207d4ee44d3f525bf9aa57807ac9db5d0d90c1a>`__
- parisc: Add constant for PDC_PAT_COMPLEX firmware call
- parisc: Add constants for various PDC firmware calls
- parisc: remove meaningless ccflags-y in arch/parisc/boot/Makefile
- parisc: use memblock_alloc() instead of custom get_memblock()
- parisc: replace oops_in_progress manipulation with bust_spinlocks()
- parisc: Add PDC_CRASH_PREP PDC function number
- `Revert: parisc: Use F_EXTEND() macro in iosapic code <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c2f8d7cb32cd95e3005bed58ce02afa686b9f357>`__
- `parisc: Detect QEMU earlier in boot process <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d006e95b5561f708d0385e9677ffe2c46f2ae345>`__ (backported to v4.9+)
- `parisc: regs_return_value() should return gpr28 <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=45efd871bf0a47648f119d1b41467f70484de5bc>`__ (backported to v4.14+)
- `parisc: also set iaoq_b in instruction_pointer_set() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=f324fa58327791b2696628b31480e7e21c745706>`__ (backported to v4.19+)

.. _kernel_5.2:

Kernel 5.2
----------

- parisc: Consider stack randomization for mmap base only when necessary
- `parisc: Export running_on_qemu symbol for modules <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3e1120f4b57bc12437048494ab56648edaa5b57d>`__ (backported to v4.9+)
- parisc: Tune LASI LAN for QEMU
- parisc: Skip registering LED when running in QEMU (backported to v4.9+)
- parisc: PA-Linux requires at least 32 MB RAM
- parisc: Show n/a if product number not available
- `parisc: Switch from DISCONTIGMEM to SPARSEMEM <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=dbdf0760990583649bfaca75fd98f76afd5f3905>`__
- parisc: Add memory barrier to asm pdc and sync instructions (backported to v4.20+)
- `parisc: Allow live-patching of \__meminit functions <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d19a12906e5e558c0f6b6cfece7b7caf1012ef95>`__ (backported to v4.20+)
- parisc: Rename LEVEL to PA_ASM_LEVEL to avoid name clash with DRBD code (backported to all)
- parisc: Use PA_ASM_LEVEL in boot code (backported to v4.14+)
- `parisc: Add static branch and JUMP_LABEL feature <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=62217beb394e654bbd2bb87c533dadd2d8bf62c6>`__
- `parisc: Remove lock code to serialize TLB operations in pacache.S <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6c63ef80014b60ab13bee7e683d0a95521bb7fdd>`__
- parisc: Use ldcw instruction for SMP spinlock release barrier
- `parisc: Add memory clobber to TLB purges <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=44224bdb99150ad17cf394973b25736cb92c246a>`__ (backported to v4.20+)
- parisc: Update huge TLB page support to use per-pagetable spinlock
- `parisc: Use per-pagetable spinlock <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b37d1c1898b288c69f3dc9267bc2c41af06f4a4b>`__
- parisc: add set_fixmap()/clear_fixmap()
- `parisc: add parisc code patching <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=620a53d522ba007a79fffc444bd75e8d3775f5b8>`__
- `parisc: add KGDB support <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=eacbfce19d8b6dbd7958cbe01d65a21324cc2fad>`__
- parisc: add functions required by KPROBE_EVENTS
- `parisc: Implement kprobes <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8858ac8e9e9b1894f7bb218bc0035532371b8d7e>`__
- parisc: remove kprobes.h from generic-y
- `parisc: Implement kretprobes <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e0b59b7b633ab72c08bb9bac36afaeaade011ddf>`__
- doc: update kprobes supported architecture list
- parisc: remove unused flags parameter in \__patch_text()
- parisc: update feature lists
- parisc: enable wide mode early
- `parisc: Drop LDCW barrier in CAS code when running UP <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e6eb5fe9123f05dcbf339ae5c0b6d32fcc0685d5>`__
- `parisc: Enable the ro_after_init feature <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8d0e051cc75e2b1a7e2fd51fc56af332c9619618>`__
- `parisc: Allow building 64-bit kernel without -mlong-calls compiler option <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=200036a8e802dee09103031eeeaea91dd212ec99>`__
- parisc: Fix compiler warnings in float emulation code
- `parisc: Fix crash due alternative coding for NP iopdir_fdc bit <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=527a1d1ede98479bf90c31a64822107ac7e6d276>`__ (backported to v5.0+)
- `parisc: Use implicit space register selection for loading the coherence index of I/O pdirs <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=63923d2c3800919774f5c651d503d1dd2adaddd5>`__ (backported to all)
- `parisc: Use lpa instruction to load physical addresses in driver code <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=116d753308cf032159c7b7aa87c9605bb5354784>`__
- parisc: configs: Remove useless UEVENT_HELPER_PATH
- parisc: Kconfig: remove ARCH_DISCARD_MEMBLOCK
- parisc/slab: cleanup after /proc/slab_allocators removal
- parisc: Fix module loading error with JUMP_LABEL feature

.. _kernel_5.3:

Kernel 5.3
----------

- `parisc: add dynamic ftrace <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6ca6366220ed285e29ee22f4cf5c68a0397cb005>`__
- compiler.h: add CC_USING_PATCHABLE_FUNCTION_ENTRY
- parisc: add support for patching multiple words
- parisc: add spinlock to patch function
- parisc: add WARN_ON() to clear_fixmap
- parisc: use pr_debug() in kernel/module.c
- parisc: asm: psw.h: missing header guard
- `parisc: Fix kernel panic due invalid values in IAOQ0 or IAOQ1 <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=10835c854685393a921b68f529bf740fa7c9984d>`__ (backported to all)
- `parisc: Ensure userspace privilege for ptraced processes in regset functions <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=34c32fc603311a72cb558e5e337555434f64c27b>`__ (backported to 4.7+)
- `parisc: Avoid kernel panic triggered by invalid kprobe <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=59a783dbc0d5fd6792aabff933055373b6dcbf2a>`__ (backported to 5.2)
- parisc: Wire up clone3 syscall
- parisc: add kprobe_fault_handler()
- `parisc: Flush ITLB in flush_tlb_all_local() only on split TLB machines <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=69245c97560b40f88c609b078f4b51c5be81d88b>`__
- parisc: Mark expected switch fall-throughs in fault.c
- parisc: Fix fall-through warnings in fpudispatch.c
- `parisc: Fix build of compressed kernel even with debug enabled <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3fe6c873af2f2247544debdbe51ec29f690a2ccf>`__
- `parisc: Strip debug info from kernel before creating compressed vmlinuz <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e50beea8e7738377b4fa664078547be338038ff9>`__
- `parisc: Add archclean Makefile target <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=f2c5ed0dd5004c2cff5c0e3d430a107576fcc17f>`__
- parisc: rename default_defconfig to defconfig
- parisc: fix race condition in patching code

.. _kernel_5.4:

Kernel 5.4
----------

- `parisc: Add assembly implementations for memset, strlen, strcpy, strncpy and strcat <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=83af58f8068ea3f7b3c537c37a30887bfa585069>`__ (was reverted)
- parisc: Add ALTERNATIVE_CODE() and ALT_COND_RUN_ON_QEMU
- parisc: speed up flush_tlb_all_local with qemu
- parisc: Avoid warning when loading hppb driver
- parisc: Convert eisa_enumerator to use pr_cont()
- parisc: Drop comments which are already in pci.h
- parisc: Save some bytes in dino driver
- `parisc: Disable HP HSC-PCI Cards to prevent kernel crash <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5fa1659105fac63e0f3c199b476025c2e04111ce>`__ (backported to all)
- parisc: Have git ignore generated real2.S and firmware.c
- kprobes/parisc: remove arch_kprobe_on_func_entry()
- parisc/ftrace: Add ARCH_SUPPORTS_FTRACE_OPS support
- parisc/ftrace: Add KPROBES_ON_FTRACE
- parisc: Update feature list
- parisc: trigger die notifier chain in parisc_terminate()
- `kexec: add KEXEC_ELF <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=175fca3bf91a1111b7e46f6655666640556b9059>`__
- kexec_elf: change order of elf\_*_to_cpu() functions
- kexec_elf: remove parsing of section headers
- kexec_elf: remove PURGATORY_STACK_SIZE
- kexec_elf: remove Elf_Rel macro
- kexec_elf: remove unused variable in kexec_elf_load()
- kexec_elf: support 32 bit ELF files
- parisc: add \__pdc_cpu_rendezvous()
- `parisc: add kexec syscall support <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=fc697dc0c26a5908d467454e49440862d7fe96d0>`__
- parisc: wire up kexec_file_load syscall
- parisc: add support for kexec_file_load() syscall
- `parisc: Remove 32-bit DMA enforcement from sba_iommu <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c32c47aa364096124c9c69c1a44918433832562b>`__
- `parisc: Fix vmap memory leak in ioremap()/iounmap() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=513f7f747e1cba81f28a436911fba0b485878ebd>`__ (backported to v3.16+)
- parisc: prefer \__section from compiler_attributes.h
- parisc: sysctl.c: Use CONFIG_PARISC instead of \__hppa\_ define
- MAINTAINERS: Add hp_sdc drivers to parisc arch

.. _kernel_5.5:

Kernel 5.5
----------

- parisc: Do not hardcode registers in checksum functions
- kexec: Fix pointer-to-int-cast warnings
- parisc: Avoid spurious inequivalent alias kernel error messages
- parisc: use pgtable-nopXd instead of 4level-fixup
- parisc: soft_offline_page() now takes the pfn
- parisc: Fix compiler warnings in debug_core.c
- parisc: fix compilation when KEXEC=n and KEXEC_FILE=y
- parisc: add missing \__init annotation

.. _kernel_5.6:

Kernel 5.6
----------

- parisc: Regenerate parisc defconfigs
- parisc: map_pages(): cleanup page table initialization
- parisc: Fix defconfig selection

.. _kernel_5.7:

Kernel 5.7
----------

- parisc: remove nargs from \__SYSCALL
- parisc: Refactor alternative code to accept multiple conditions
- parisc: Rework arch_rw locking functions
- parisc: Improve interrupt handling in arch_spin_lock_flags()
- parisc: Replace setup_irq() by request_irq()
- `parisc: Fix kernel panic in mem_init() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=bf71bc16e02162388808949b179d59d0b571b965>`__ (backported to all)

.. _kernel_5.8:

Kernel 5.8
----------

- parisc: use -fno-strict-aliasing for decompressor
- parisc: suppress error messages for 'make clean'
- parisc: Kconfig: Update references to parisc website
- parisc: firmware: Update references to parisc website
- parisc: hardware: Update references to parisc website
- parisc: module: Update references to parisc website
- parisc: MAINTAINERS: Update references to parisc website
- `parisc: add sysctl file interface panic_on_stackoverflow <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b6522fa409cfafbc3968679b09e4028f0609f2b9>`__
- `parisc: Add atomic64_set_release() define to avoid CPU soft lockups <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=be6577af0cef934ccb036445314072e8cb9217b9>`__ (backported to all - fixes TLB stalls)
- parisc: add support for cmpxchg on u8 pointers

.. _kernel_5.9:

Kernel 5.9
----------

- parisc: Replace HTTP links with HTTPS ones
- parisc: Convert to BIT_MASK() and BIT_WORD()
- parisc: Report bad pages as HardwareCorrupted
- Revert "parisc: Improve interrupt handling in arch_spin_lock_flags()"
- Revert "parisc: Drop LDCW barrier in CAS code when running UP"
- Revert "parisc: Use ldcw instruction for SMP spinlock release barrier"
- Revert "parisc: Revert "Release spinlocks using ordered store""
- `parisc: Do not use an ordered store in pa_tlb_lock() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e72b23dec1da5e62a0090c5da1d926778284e230>`__ (backported to 5.0+)
- parisc: elf.h: delete a duplicated word
- parisc: make the log level string for register dumps const
- sections.h: dereference_function_descriptor() returns void pointer
- parisc: Whitespace cleanups in atomic.h
- `parisc: Implement \__smp_store_release and \__smp_load_acquire barriers <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e96ebd589debd9a6a793608c4ec7019c38785dea>`__ (backported to 4.14+)
- parisc/kernel/ftrace: Remove function callback casts
- `parisc: mask out enable and reserved bits from sba imask <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5b24993c21cbf2de11aff077a48c5cb0505a0450>`__ (backported to all)
- `parisc: fix PMD pages allocation by restoring pmd_alloc_one() <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6f6aea7e966cda5a817d091e938c2d9b52209893>`__

.. _kernel_5.10:

Kernel 5.10
-----------

- parisc: disable CONFIG_IDE in defconfigs
- fw_cfg: Add support for parisc architecture
- `parisc: Add qemu fw_cfg interface <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=024f5b597564acced9e69305f7a9ef1202186a61>`__
- parisc: Avoid external interrupts when IPI finishes
- `parisc: Drop HP-UX specific fcntl and signal flags <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=41f5a81c07cd410917c9ae3c165b9b761d48ba75>`__
- `parisc: Define O_NONBLOCK to become 000200000 <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=75ae04206a4d0e4f541c1d692b7febd1c0fdb814>`__
- parisc: Drop useless comments in uapi/asm/signal.h
- parisc: Add ioread64_hi_lo() and iowrite64_hi_lo()
- parisc: Install vmlinuz instead of zImage file
- parisc: Add MAP_UNINITIALIZED define
- parisc/sticon: Always register sticon console driver
- `parisc/sticon: Add user font support <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7ff3f14ddc355bfbc94c766f43b90606b1f79e83>`__
- parisc: Add alternative patching to synchronize_caches define
- parisc: Fix comments and enable interrupts later
- parisc: Mark pointers volatile in \__xchg8(), \__xchg32() and \__xchg64()
- `parisc: Switch to more fine grained lws locks <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=53a42b6324b8ddce1e9d2f34da2ca68ef21c2084>`__
- parisc: Rewrite tlb flush threshold calculation
- parisc: Improve spinlock handling
- parisc: Improve error return codes when setting rtc time
- `hil/parisc: Disable HIL driver when it gets stuck <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=879bc2d27904354b98ca295b6168718e045c4aa2>`__
- `parisc: Add wrapper syscalls to fix O_NONBLOCK flag usage <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=44a4c9e443674e6cd3368d3e642dfe9c429d5525>`__
- ata: pata_ns87415.c: Document support on parisc with superio chip

.. _kernel_5.11:

Kernel 5.11
-----------

- parisc: pci-dma: fix warning unused-function
- parisc/uapi: Use Kbuild logic to provide <asm/types.h>
- parisc: Drop loops_per_jiffy from per_cpu struct
- parisc: Use \_TIF_USER_WORK_MASK in entry.S
- `parisc: Make user stack size configurable <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=22ee3ea588dfc84ccb8cea5ea37051dfed91b9b9>`__
- parisc: Remove leftover reference to the power_tasklet
- `parisc: Enable -mlong-calls gcc option by default when !CONFIG_MODULES <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=00e35f2b0e8acb88d4e1aa96ff0490e3bfe46580>`__ (backported to v5.6+)

.. _kernel_5.12:

Kernel 5.12
-----------

- `parisc: Optimize per-pagetable spinlocks <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b7795074a04669d0a023babf786d29bf67c68783>`__
- `binfmt_misc: pass binfmt_misc flags to the interpreter <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2347961b11d4079deace3c81dceed460c08a8fc1>`__ (needed for better qemu-user support on all platforms)
- `parisc: Fix IVT checksum calculation wrt HPMC <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c70919bd9d0782a6078ccd37d7f861d514f5481e>`__
- `parisc: Bump 64-bit IRQ stack size to 64 KB <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=31680c1d1595a59e17c14ec036b192a95f8e5f4a>`__
- parisc: Drop out of get_whan() if task is running again
- parisc: Use the generic devmem_is_allowed()
- parisc: Replace test_ti_thread_flag() with test_tsk_thread_flag()
- parisc: Enable -mlong-calls gcc option with CONFIG_COMPILE_TEST (backported to v5.6+)
- arch/parisc/kernel: remove duplicate include in ptrace
- parisc: math-emu: Few spelling fixes in the file fpu.h
- parisc: avoid a warning on u8 cast for cmpxchg on u8 pointers (backported to v5.8+)
- parisc: parisc-agp requires SBA IOMMU driver (backported to all)
- parisc: Remove duplicate struct task_struct declaration

.. _kernel_5.13:

Kernel 5.13
-----------

- parisc: switch to generic syscall header scripts

.. _kernel_5.14:

Kernel 5.14
-----------

- `Revert "parisc: Add assembly implementations for memset, strlen, strcpy, strncpy and strcat" <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=f6a3308d6feb351d9854eb8b3f6289a1ac163125>`__ (backported to 5.4+)

.. _kernel_5.15:

Kernel 5.15
-----------

- parisc: switch from 'pci\_' to 'dma\_' API
- parisc/parport_gsc: switch from 'pci\_' to 'dma\_' API
- `parisc: Increase size of gcc stack frame check <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=55b70eed81cba1331773d4aaf5cba2bb07475cd8>`__
- `parisc: math-emu: Fix fall-through warnings <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6f1fce595b78b775d7fb585c15c2dc3a6994f96e>`__
- parisc: Replace symbolic permissions with octal permissions
- parisc: math-emu: Avoid "fmt" macro collision
- `parisc: remove unused arch/parisc/boot/install.sh and its phony target <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d220da0967dbda232350c5dc39317e04e0892743>`__
- `parisc: move core-y in arch/parisc/Makefile to arch/parisc/Kbuild <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6ef4661cad32b5098ffb31be3282c866befde85f>`__
- `parisc: Fix compile failure when building 64-bit kernel natively <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5f6e0fe01b6b33894cf6f61b359ab5a6d2b7674e>`__
- `parisc: Rename PMD_ORDER to PMD_TABLE_ORDER <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7bf82eb3873fbbee8273f60ddef584194b99f6c1>`__
- `parisc: fix crash with signals and alloca <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=030f653078316a9cc9ca6bd1b0234dcf858be35d>`__ (backported to all)
- parisc: ccio-dma.c: Added tab instead of spaces
- parisc: Make struct parisc_driver::remove() return void
- `parisc: Drop \__arch_swab16(), arch_swab24(), \_arch_swab32() and \__arch_swab64() functions <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e5a2cac908df691f1637f9272d4c6dec83239611>`__
- `parisc: Fix unaligned-access crash in bootloader <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c42813b71a06a2ff4a155aa87ac609feeab76cf3>`__ (backported to v5.14)
- parisc: Move pci_dev_is_behind_card_dino to where it is used
- parisc: Add missing FORCE prerequisite in Makefile
- `parisc: Drop strnlen_user() in favour of generic version <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1260dea6d2eb75706c978da828a36f0def590d3a>`__
- parisc: Drop useless debug info and comments from signal.c
- `parisc: Check user signal stack trampoline is inside TASK_SIZE <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3e4a1aff2a97cb4fd7f0268e4b69e8c9d3641277>`__
- `parisc: Reduce sigreturn trampoline to 3 instructions <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e4f2006f1287e7ea17660490569cff323772dac4>`__ **(was reverted in v5.15 and v5.16)**
- `parisc: Mark sched_clock unstable only if clocks are not syncronized <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d97180ad68bdb7ee10f327205a649bc2f558741d>`__
- `parisc: Implement \__get/put_kernel_nofault() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=671028728083e856e9919221b109e3b2cd2ccc49>`__ (Dropped CONFIG_SET_FS incl. KERNEL_DS and USER_DS)
- `parisc: Use absolute_pointer() to define PAGE0 <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=90cc7bed1ed19f869ae7221a6b41887fe762a6a3>`__
- `parisc: Declare pci_iounmap() parisc version only when CONFIG_PCI enabled <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9caea0007601d3bc6debec04f8b4cd6f4c2394be>`__

.. _kernel_5.16:

Kernel 5.16
-----------

- parisc: Make use of the helper macro kthread_run()
- parisc: make parisc_acctyp() available outside of faults.c
- `parisc: Switch to ARCH_STACKWALK implementation <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=aeb1e833a4c38efffad9556cf7f458c4e5de5b45>`__
- `parisc: Add KFENCE support <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=ec5c115050f59114e216212837f1c1ebc54bdfc9>`__
- parisc: Define FRAME_ALIGN and PRIV_USER/PRIV_KERNEL in assembly.h
- parisc: Allocate task struct with stack frame alignment
- parisc: Use FRAME_SIZE and FRAME_ALIGN from assembly.h
- parisc: Use PRIV_USER instead of 3 in entry.S
- task_stack: Fix end_of_stack() for architectures with upwards-growing stack
- `parisc: Fix ptrace check on syscall return <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8779e05ba8aaffec1829872ef9774a71f44f6580>`__ (backported to all)
- `parisc: Move thread_info into task struct <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2214c0e77259b420402e279e9ab4277ef320d371>`__
- parisc: Use PRIV_USER in syscall.S
- parisc: Use PRIV_USER and PRIV_KERNEL in ptrace.h
- parisc: Drop ifdef \__KERNEL\_\_ from non-uapi kernel headers
- `parisc: enhance warning regarding usage of O_NONBLOCK <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3759778e6b8c0d547d77f681a7779edccdf1710a>`__
- parisc: Remove unused constants from asm-offsets.c
- parisc: Update defconfigs
- `parisc: Fix set_fixmap() on PA1.x CPUs <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6e866a462867b60841202e900f10936a0478608c>`__ (backported to v5.3+)
- parisc: decompressor: remove repeated depenency of misc.o
- parisc: decompressor: clean up Makefile
- parisc/unwind: use copy_from_kernel_nofault()
- parisc: disable preemption during local tlb flush
- parisc: deduplicate code in flush_cache_mm() and flush_cache_range()
- parisc: fix preempt_count() check in entry.S
- parisc: disable preemption in send_IPI_allbutself()
- parisc: fix warning in flush_tlb_all
- parisc/unwind: fix unwinder when CONFIG_64BIT is enabled
- parisc: move virt_map macro to assembly.h
- parisc: add PIM TOC data structures
- parisc/firmware: add functions to retrieve TOC data
- `parisc: add support for TOC (transfer of control) <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=bc294838cc3443a2fbec58f8936ad4bd0a0b3055>`__
- parisc/kgdb: add kgdb_roundup() to make kgdb work with idle polling
- parisc: mark xchg functions notrace
- parisc/ftrace: set function trace function
- parisc/ftrace: use static key to enable/disable function graph tracer
- parisc: Use swap() to swap values in setup_bootmem()
- `parisc: don't enable irqs unconditionally in handle_interruption() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=014966dcf76bce5717f7d974d0410d3402a651c2>`__
- `parisc: Don't disable interrupts in cmpxchg and futex operations <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7e992711dddbdb1c27d077432d8440fefd44819f>`__
- `parisc: move CPU field back into thread_info <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2a2e8202c7a16a85a881ad2b6e32ccbebdc01dda>`__
- `parisc: Fix backtrace to always include init funtion names <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=279917e27edc293eb645a25428c6ab3f3bca3f86>`__ (backported to v5.4+ - and **reverted in v5.16**)
- parisc: Fix implicit declaration of function \__kernel_text_address
- `parisc: Flush kernel data mapping in set_pte_at() when installing pte for user page <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=38860b2c8bb1b92f61396eb06a63adff916fc31d>`__ (backported to v5.12+)
- `parisc/entry: fix trace test in syscall exit path <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3ec18fc7831e7d79e2d536dd1f3bc0d3ba425e8a>`__ to-be-backported!
- parisc: Include stringify.h to avoid build error in crypto/api.c
- parisc: Wire up futex_waitv
- parisc: Wrap assembler related defines inside \__ASSEMBLY\_\_
- `Revert "parisc: Reduce sigreturn trampoline to 3 instructions" <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=79df39d535c7a3770856fe9f5aba8c0ad1eebdb6>`__ (reverted in v5.15 too)
- parisc: Enable CONFIG_PRINTK_TIME=y in 32bit defconfig
- `parisc/sticon: fix reverse colors <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=bec05f33ebc1006899c6d3e59a00c58881fe7626>`__ (backported to all)
- parisc: Increase FRAME_WARN to 2048 bytes on parisc
- `parisc: Provide an extru_safe() macro to extract unsigned bits <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=169d1a4a2adb2c246396c56aa2f9eec3868546f1>`__
- parisc: Convert PTE lookup to use extru_safe() macro
- `Revert "parisc: Fix backtrace to always include init funtion names" <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=98400ad75e95860e9a10ec78b0b90ab66184a2ce>`__ (backported to v5.4+)
- `parisc: Fix extraction of hash lock bits in syscall.S <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=df2ffeda6370a77011902e7c9d7a1eb1cbffed4f>`__
- `parisc: Fix KBUILD_IMAGE for self-extracting kernel <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1d7c29b77725d05faff6754d2f5e7c147aedcf93>`__ (backported to v4.14+)
- parisc: Enable sata sil, audit and usb support on 64-bit defconfig
- parisc/agp: Annotate parisc agp init functions with \__init
- `parisc: Fix "make install" on newer debian releases <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=0f9fee4cdebfbe695c297e5b603a275e2557c1cc>`__ (backported to v3.13+)
- `parisc: Mark cr16 CPU clocksource unstable on all SMP machines <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=afdb4a5b1d340e4afffc65daa21cc71890d7d589>`__ (backported to v4.14+)
- parisc: Clear stale IIR value on instruction access rights trap
- `parisc: Correct completer in lws start <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8f66fce0f46560b9e910787ff7ad0974441c4f9c>`__ (backported to v4.19+)
- `parisc: Fix mask used to select futex spinlock <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d3a5a68cff47f6eead84504c3c28376b85053242>`__ (backported to v5.10+)
- parisc: remove ARCH_DEFCONFIG

.. _kernel_5.17:

Kernel 5.17
-----------

- parisc: io: Improve the outb(), outw() and outl() macros
- parisc: pdc_stable: use default_groups in kobj_type
- `parisc: Switch user access functions to signal errors in r29 instead of r8 <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4b9d2a731c3d22a05c1bccdb11b6e00054ff5fda>`__
- `parisc: Fix pdc_toc_pim_11 and pdc_toc_pim_20 definitions <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=712a270d2db967b387338c26c3dc04ccac3fcec3>`__ (backported to v5.16)
- `parisc: Add kgdb io_module to read chars via PDC <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c1c72d9bbf2bf91670ac589adf037f433642fff5>`__
- `parisc: Enable TOC (transfer of contents) feature unconditionally <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d6ab9fc74513ae6501afcdae2547334a03b9a5c9>`__
- parisc: Re-use toc_stack as hpmc_stack
- `sections: Fix \__is_kernel() to include init ranges <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=16f035d9e264d95d61d5f4056bb00d8169a7a3d1>`__
- `parisc: Default to 16 CPUs on 32-bit kernel <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=68d247ad38b1ef46bd945a5220fa6d28c901c2f2>`__
- `parisc: Define depi_safe macro <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=45458aa49abe3b0ac68ce86b3d4ca3a97eaeac53>`__
- `parisc: Fix lpa and lpa_user defines <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=db19c6f1a2a353cc8dec35b4789733a3cf6e2838>`__ (backported to v5.2+)
- parisc: Avoid calling faulthandler_disabled() twice
- parisc: Don't call faulthandler_disabled() in do_page_fault()
- `parisc: Enhance page fault termination message <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=20dda87bdc6567e864942ead40bc149ebbe3ae79>`__
- `parisc: Rewrite light-weight syscall and futex code <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d0585d742ff2d82accd26c661c60a6d260429c4a>`__
- `parisc: Add lws_atomic_xchg and lws_atomic_store syscalls <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=72c3dd8207de4178ae50553666129af7231f86e7>`__
- parisc: decompressor: do not copy source files while building
- parisc: Add visible flag to toc_stack variable
- parisc: Use safer strscpy() in setup_cmdline()
- `parisc: Autodetect default output device and set console= kernel parameter <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5f7ee6e37a3cadefe45378c17c4285fa41141d92>`__
- parisc: Fix missing prototype for 'toc_intr' warning in toc.c
- parisc: pdc_stable: Fix memory leak in pdcs_register_pathentries
- parisc: Add ioread64_lo_hi() and iowrite64_lo_hi()
- `parisc: Show error if wrong 32/64-bit compiler is being used <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b160628e9ebcdc85d0db9d7f423c26b3c7c179d0>`__ [STRIKEOUT:(backported to v5.15+)] (reverted in v6.0)
- `parisc: Fix some apparent put_user() failures <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=dbd0b42350d5717786cb8257fbe5b528f3af9772>`__
- `parisc: Drop \__init from map_pages declaration <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9129886b88185962538180625ca8051362b01327>`__ (backported to v5.4+)
- `parisc: Fix data TLB miss in sba_unmap_sg <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b7d6f44a0fa716a82969725516dc0b16bc7cd514>`__ (backported to all)
- `parisc: Fix sglist access in ccio-dma.c <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d7da660cab47183cded65e11b64497d0f56c6edf>`__ (backported to all)
- `serial: parisc: GSC: fix build when IOSAPIC is not set <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6e8793674bb0d1135ca0e5c9f7e16fecbf815926>`__ (backported to all)
- `parisc/unaligned: Fix ldw() and stw() unalignment handlers <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a97279836867b1cb50a3d4f0b1bf60e0abe6d46c>`__ (backported to all)
- `parisc/unaligned: Fix fldd and fstd unaligned handlers on 32-bit kernel <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=dd2288f4a020d693360e3e8d72f8b9d9c25f5ef6>`__ (backported to all)

.. _kernel_5.18:

Kernel 5.18
-----------

- `parisc: Add vDSO support <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=df24e1783e6e0eb3dc0e3ba5a8df3bb0cc537408>`__
- video/fbdev/stifb: Implement the stifb_fillrect() function
- `parisc: Always use the self-extracting kernel feature <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b9f50eea4f277943842e82bc4d73f912a2f165c6>`__
- parisc: Add defines for various space register
- parisc: Use SR_USER and SR_KERNEL in get_user() and put_user()
- parisc: Use constants to encode the space registers like SR_KERNEL
- `parisc: Reduce code size by optimizing get_current() function calls <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8278cc16266326f23c05f94d748569faa81319bb>`__
- `parisc/unaligned: Use EFAULT fixup handler in unaligned handlers <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=df24e1783e6e0eb3dc0e3ba5a8df3bb0cc537408>`__
- parisc/unaligned: Rewrite inline assembly of emulate_ldh()
- parisc/unaligned: Rewrite inline assembly of emulate_ldw()
- parisc/unaligned: Rewrite 32-bit inline assembly of emulate_ldd()
- parisc/unaligned: Rewrite 32-bit inline assembly of emulate_sth()
- parisc/unaligned: Enhance user-space visible output
- parisc: Avoid calling SMP cache flush functions on cache-less machines
- parisc: Enable ARCH_HAS_DEBUG_VM_PGTABLE
- parisc: Improve CPU socket and core bootup info text
- `parisc: Avoid using hardware single-step in kprobes <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9b046d0245cec982c72a65d3ea1b834959a9708b>`__
- parisc: Avoid flushing cache on cache-less machines
- `parisc: Fix non-access data TLB cache flush faults <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=df24e1783e6e0eb3dc0e3ba5a8df3bb0cc537408>`__
- `parisc: Fix handling off probe non-access faults <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=df24e1783e6e0eb3dc0e3ba5a8df3bb0cc537408>`__
- parisc: Simplify fast path for non-access data TLB faults
- parisc: Increase parisc_cache_flush_threshold setting
- `parisc: Fix invalidate/flush vmap routines <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=53d862fac4a09b9c56cca0433fa9de5732fd05a1>`__
- `Revert "parisc: Fix invalidate/flush vmap routines" <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=08a491b2e4b1c4c618ee82d3f4b0ff3b20c5acf5>`__
- `parisc: Fix CPU affinity for Lasi, WAX and Dino chips <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=939fc856676c266c3bc347c1c1661872a3725c0f>`__
- parisc: Clean up cpu_check_affinity() and drop cpu_set_affinity_irq()
- parisc: Detect hppa-suse-linux-gcc compiler for cross-building
- parisc: Add constants for control registers and clean up mfctl()
- parisc: Ensure set_firmware_width() is called only once
- parisc: Switch from GENERIC_CPU_DEVICES to GENERIC_ARCH_TOPOLOGY
- parisc: Move store_cpu_topology() into text section
- parisc: Move CPU startup-related functions into .text section
- parisc: Move disable_sr_hashing_asm() into .text section
- parisc: Add PDC locking functions for rendezvous code
- `parisc: Implement \__cpu_die() and \__cpu_disable() for CPU hotplugging <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=88b3aac6228baaac6a3bcc0808845083b9d9f08f>`__
- `parisc: Rewrite arch_cpu_idle_dead() for CPU hotplugging <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=98903688e6106d9ca68e44c7d218e61336d54631>`__
- parisc: Move common_stext into .text section when CONFIG_HOTPLUG_CPU=y
- parisc: Find a new timesync master if current CPU is removed
- `parisc: Fix patch code locking and flushing <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a9fe7fa7d874a536e0540469f314772c054a0323>`__
- Revert "parisc: Mark cr16 CPU clocksource unstable on all SMP machines"
- Revert "parisc: Mark sched_clock unstable only if clocks are not syncronized"
- Revert "parisc: Fix patch code locking and flushing"
- parisc: Only list existing CPUs in cpu_possible_mask
- parisc: Update 32- and 64-bit defconfigs
- parisc: Re-enable GENERIC_CPU_DEVICES for !SMP
- `parisc: Merge model and model name into one line in /proc/cpuinfo <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5b89966bc96a06f6ad65f64ae4b0461918fcc9d3>`__ (backported to all)
- parisc: Change MAX_ADDRESS to become unsigned long long
- `parisc: Mark cr16 clock unstable on all SMP machines <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=340233dcc0160aafcce46ca893d1679f16acf409>`__ (backported to v5.16+)
- Revert "parisc: Increase parisc_cache_flush_threshold setting"
- parisc: Fix typos in comments
- parisc: Disable debug code regarding cache flushes in handle_nadtlb_fault()
- `parisc: Rewrite cache flush code for PA8800/PA8900 <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2de8b4cc2051ee1d40eedbcf94de0e7d04507c37>`__
- `parisc: Fix patch code locking and flushing <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=798082be69fea995a475ca1db8f9873589e207d9>`__

.. _kernel_5.19:

Kernel 5.19
-----------

- parisc: video: fbdev: stifb: Add sti_dump_font() to dump STI font
- parisc: Prevent ldil() to sign-extend into upper 32 bits
- parisc: Fix wrong comment for shr macro
- parisc: Add dep_safe() macro to deposit a register in 32- and 64-kernels
- `parisc: Optimize tmpalias function calls <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c1770918492414e8dc103d996f3f3860ee0bfd6c>`__
- parisc: Drop \__ARCH_WANT_OLD_READDIR and \__ARCH_WANT_SYS_OLDUMOUNT
- parisc: Don't enforce DMA completion order in cache flushes
- parisc: Don't hardcode assembler bit definitions in tmpalias code
- parisc/stifb: Implement fb_is_primary_device() (backported to v5.10+)
- parisc/stifb: Keep track of hardware path of graphics card (backported to v5.10+)
- `parisc: fix a crash with multicore scheduler <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6ba688364856ad083be537f08e86ba97f433d405>`__ (backported to v5.18)
- `parisc/stifb: Fix fb_is_primary_device() only available with CONFIG_FB_STI <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1d0811b03eb30b2f0793acaa96c6ce90b8b9c87a>`__ (backported to v5.10+)
- `parisc: Enable ARCH_HAS_STRICT_MODULE_RWX <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=0a1355db36718178becd2bfe728a023933d73123>`__ (backported to v5.2+)
- parisc: align '\*' in comment in math-emu code
- `parisc: Fix flush_anon_page on PA8800/PA8900 <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e9ed22e6e5010997a2f922eef61ca797d0a2a246>`__ (backported to v5.18)
- `parisc/unaligned: Fix emulate_ldw() breakage <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=96b80fcd2705fc50ebe1f7f3ce204e861b3099ab>`__ (backported to v5.18)
- `parisc: Fix vDSO signal breakage on 32-bit kernel <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=aa78fa905b4431c432071a878da99c2b37fc0e79>`__ (backported to v5.18)

.. _kernel_6.0:

Kernel 6.0
----------

- parisc: Clean up names in hardware database
- `parisc: Fix device names in /proc/iomem <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=cab56b51ec0e69128909cef4650e1907248d821b>`__ (backported to v4.9+)
- parisc: Drop pa_swapper_pg_lock spinlock
- `parisc: io_pgetevents_time64() needs compat syscall in 32-bit compat mode <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6431e92fc827bdd2d28f79150d90415ba9ce0d21>`__ (backported to v5.1+)
- parisc: Fix comment typo in fault.c
- parisc: Drop zero variable initialisations in mm/init.c
- parisc: Check the return value of ioremap() in lba_driver_probe()
- Input: gscps2 - check return value of ioremap() in gscps2_probe()
- parisc: Do not initialise statics to 0
- `parisc: Fix exception handler for fldw and fstw instructions <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7ae1f5508d9a33fd58ed3059bd2d569961e3b8bd>`__ (backported to all)
- [https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3dcfb729b5f4a0c9b50742865cd5e6c4dbcc80dc\ **parisc: Make CONFIG_64BIT available for ARCH=parisc64 only**] (backported to v5.15+)
- Revert "parisc: Show error if wrong 32/64-bit compiler is being used"
- `parisc: Add runtime check to prevent PA2.0 kernels on PA1.x machines <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=591d2108f3abc4db9f9073cae37cf3591fd250d6>`__
- parisc: ccio-dma: Fix typo in comment
- parisc: ccio-dma: Handle kmalloc failure in ccio_init_resources()
- parisc: led: Move from strlcpy with unused retval to strscpy
- `tools/include/uapi: Fix <asm/errno.h> for parisc and xtensa <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=95363747a6f39e88a3052fcf6ce6237769495ce0>`__ (backported to v5.10+)
- `parisc: Allow CONFIG_64BIT with ARCH=parisc <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=805ce8614958c925877ba6b6dc26cdf9f8800474>`__ (backported to v5.15+)
- parisc: remove obsolete manual allocation aligning in iosapic
- parisc: ccio-dma: Add missing iounmap in error path in ccio_probe()

.. _kernel_6.1:

Kernel 6.1
----------

- `parisc: Reduce kernel size by packing alternative tables <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b148766e2b8b7b61c9aef53aefedae33f637a1e7>`__
- `parisc: Convert PDC console to an early console <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=027c3d345e2a1ea61d6e4506a250eb392e6e7b18>`__
- `parisc: fbdev/stifb: Align graphics memory size to 4MB <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=aca7c13d3bee81a968337a5515411409ae9d095d>`__ (backported to all)
- `parisc: Fix userspace graphics card breakage due to pgtable special bit <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=70be49f2f6223ddd2fcddb0089a40864c37e1494>`__ (backported to v5.18+)
- `parisc: Make 8250_gsc driver dependend on CONFIG_PARISC <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e8a18e3f00f3ee8d07c17ab1ea3ad4df4a3b6fe0>`__ (backported to all)
- `parisc/serial: Rename 8250_gsc.c to 8250_parisc.c <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9e4e2ce1a78ed92ed91135e90c85f27d75388129>`__
- parisc: Use signed char for hardware path in pdc.h
- `parisc: Export iosapic_serial_irq() symbol for serial port driver <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a0c9f1f2e53b8eb2ae43987a30e547ba56b4fa18>`__ (backported to all)
- `parisc: Avoid printing the hardware path twice <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2b6ae0962b421103feb41a80406732944b0665b3>`__ (backported to v4.19+)
- MAINTAINERS: adjust entry after renaming parisc serial driver

.. _kernel_6.2:

Kernel 6.2
----------

- parisc: Fix inconsistent indenting in setup_cmdline()
- `parisc: Align parisc MADV_XXX constants with all other architectures <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=71bdea6f798b425bc0003780b13e3fdecb16a010>`__
- parisc: Drop PMD_SHIFT from calculation in pgtable.h (backported to v6.0+)
- `parisc: Fix locking in pdc_iodc_print() firmware call <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7236aae5f81f3efbd93d0601e74fc05994bc2580>`__ (backported to v6.0+)
- `parisc: Drop duplicate kgdb_pdc console <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7e6652c79ecd74e1112500668d956367dc3772a5>`__ (backported to v6.1+)
- `parisc: Drop locking in pdc console code <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7dc4dbfe750e1f18c511e73c8ed114da8de9ff85>`__ (backported to v6.1+)
- parisc: Move pdc_result struct to firmware.c
- `parisc: Add missing FORCE prerequisites in Makefile <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7dc4dbfe750e1f18c511e73c8ed114da8de9ff85>`__ (backported to v5.18+)
- `parisc: Show MPE/iX model string at bootup <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4934fbfb3ff09b8500f63d4624ed8b41647bb822>`__
- `parisc: led: Fix potential null-ptr-deref in start_task() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=41f563ab3c33698bdfc3403c7c2e6c94e73681e4>`__ (backported to all)
- `parisc: Fix return code of pdc_iodc_print() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5d1335dabb3c493a3d6d5b233953b6ac7b6c1ff2>`__ (backported to all)
- `parisc: Replace hardcoded value with PRIV_USER constant in ptrace.c <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3f0c17809a098d3f0c1ec83f1fb3ca61638d3dcd>`__ (backported to v5.16+)
- `parisc: Wire up PTRACE_GETREGS/PTRACE_SETREGS for compat case <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=316f1f42b5cc1d95124c1f0387c867c1ba7b6d0e>`__ (backported to v4.7+)
- `parisc: pdc_stable: use strscpy() to instead of strncpy() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=f2193bb2ee6e21d9c2da10ea3ce63b94aea69341>`__
- `highmem: round down the address passed to kunmap_flush_on_unmap() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=88d7b12068b95731c280af8ce88e8ee9561f96de>`__ - (backported to all)

.. _kernel_6.3:

Kernel 6.3
----------

- no patches

.. _kernel_6.4:

Kernel 6.4
----------

- parisc: Replace regular spinlock with spin_trylock on panic path
- parisc: Limit amount of kgdb breakpoints on parisc
- `parisc: Ensure page alignment in flush functions <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d755bd2caeb47fd806e12399fe8b56798fa5d2cc>`__ (backported to 6.0+)
- parisc: Drop HP-UX constants and structs from grfioctl.h
- `parisc: Fix argument pointer in real64_call_asm() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6e3220ba3323a2c24be834aebf5d6e9f89d0993f>`__ (backported to all)
- `parisc: Cleanup mmap implementation regarding color alignment <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=567b35159e76997e95b643b9a8a5d9d2198f2522>`__
- parisc: update kbuild doc. aliases for parisc64
- `parisc: Fix encoding of swp_entry due to added SWP_EXCLUSIVE flag <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6f9e98849edaa8aefc4030ff3500e41556e83ff7>`__ (backported to v6.3+)
- parisc: kexec: include reboot.h
- `parisc: Improve cache flushing for PCXL in arch_sync_dma_for_cpu() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=59fa12646d9f56c842b4d5b6418ed77af625c588>`__
- `parisc: Flush gatt writes and adjust gatt mask in parisc_agp_mask_memory() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d703797380c540bbeac03f104ebcfc364eaf47cc>`__
- `parisc: Use num_present_cpus() in alternative patching code <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b6405f0829d7b1dd926ba3ca5f691cab835abfaa>`__
- `parisc: Add lightweight spinlock checks <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=15e64ef6520ea8702998db05b87fa5c3d3d40710>`__
- `parisc: Enable LOCKDEP support <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=adf8e96a7ea670d45b5de7594acc67e8f4787ae6>`__
- `parisc: Allow to reboot machine after system halt <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2028315cf59bb899a5ac7e87dc48ecb8fac7ac24>`__ (backported to v4.14+)
- `parisc: Handle kprobes breakpoints only in kernel context <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=df419492e428b6a2bce98d0f613c58a13da6666c>`__ (backported to v5.18+)
- `parisc: Handle kgdb breakpoints only in kernel context <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6888ff04e37d01295620a73f3f7efbc79f6ef152>`__ (backported to v5.4+)
- `parisc: Fix flush_dcache_page() for usage from irq context <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=61e150fb310729c98227a5edf6e4a3619edc3702>`__ (backported to v5.18+) (and all)

.. _kernel_6.5:

Kernel 6.5
----------

- parisc: Move TLB_PTLOCK option to Kconfig.debug
- parisc: Check if IRQs are disabled when calling arch_local_irq_restore()
- `parisc: Add cacheflush() syscall <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c6d96328fecdda16e12f3b3c33f3677f4bcef89f>`__
- parisc: Fix missing prototype warning for arch_report_meminfo()
- parisc: Default to 8 CPUs for 64-bit kernel
- `sticon/parisc: Allow 64-bit STI calls in PDC firmware abstration <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=ededd9d27834ad1f300436c1b78e58ad4fcf5dd7>`__
- `sticon/parisc: Fix STI console on 64-bit only machines <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=99ef0c67bc85e2ea547e2c6c9ed29480cd361446>`__
- parisc: sba_iommu: Fix kdoc warnings
- parisc: Fold 32-bit compat code into audit_classify_syscall()
- parisc: drivers: Fix kdoc warnings
- parisc: firmware: Fix kdoc warnings
- parisc: pdc_chassis: Fix kdoc warnings
- parisc: module: Mark symindex \__maybe_unused
- parisc: Mark image_size \__maybe_unused in perf_write()
- parisc: pci-dma: Make pcxl_alloc_range() static
- parisc: pdc_stable: Fix kdoc and compiler warnings
- parisc: ccio-dma: Fix kdoc and compiler warnings
- parisc: sys_parisc: parisc_personality() is called from asm code
- parisc: processor: Fix kdoc for init_cpu_profiler()
- parisc: traps: Mark functions static
- parisc: init: Drop unused variable end_paddr
- parisc: unwind: Mark start and stop variables \__maybe_unused
- parisc: signal: Mark do_notify_resume() and sys_rt_sigreturn() asmlinkage
- parisc: unaligned: Include header file to avoid missing prototype warnings
- parisc: lba_pci: Mark two variables \__maybe_unused
- parisc: dino: Make dino_init() returning void
- parisc: Move init function declarations into header file
- parisc: irq: Add irq-related function declarations
- parisc: Refresh defconfigs
- parport: gsc: remove DMA leftover code
- `parisc/mm: preallocate fixmap page tables at init <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c2ff2b736c41cc63bb0aaec85cccfead9fbcfe92>`__ (backported to v6.3)
- parisc: pci-dma: remove unused and dead EISA code and comment
- parisc: unaligned: Add required spaces after ','
- `parisc: Fix lightweight spinlock checks to not break futexes <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a0f4b7879f2e14986200747d1b545e5daac8c624>`__ (backported to v6.4+)
- parisc: fault: Use C99 arrary initializers
- parisc: ioremap: Fix sparse warnings
- parisc: signal: Fix sparse incorrect type in assignment warning
- parisc: firmware: Fix sparse context imbalance warnings
- parisc: firmware: Mark pdc_result buffers local
- parisc: ucmpdi2: Fix no previous prototype for '\__ucmpdi2' warning
- parisc: parisc_ksyms: Include libgcc.h for libgcc prototypes
- parisc: dma: Add prototype for pcxl_dma_start
- parisc: Move proc_mckinley_root and proc_runway_root to sba_iommu
- parisc: unaligned: Include linux/sysctl.h for unaligned_enabled
- parisc: processor: Include asm/smp.h for init_per_cpu()
- parisc: boot: Nuke some sparse warnings in decompressor
- parisc: ftrace: Add declaration for ftrace_function_trampoline()
- parisc: perf: Make cpu_device variable static
- parisc: pdt: Use PTR_ERR_OR_ZERO() to simplify code
- `parisc: Fix CONFIG_TLB_PTLOCK to work with lightweight spinlock checks <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7a894c87374771f3cfb1b8e5453fbe03f1fb8135>`__ (backported to v6.4+)
- `lib/clz_ctz.c: Fix \__clzdi2() and \__ctzdi2() for 32-bit kernels <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/>`__ (backported to v3.11+) **10-year-old-bug**

.. _kernel_6.6:

Kernel 6.6
----------

- `lockdep: fix static memory detection even more <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=0a6b58c5cd0dfd7961e725212f0fc8dfc5d96195>`__ (asked to backport to v6.1+)
- `parisc: lasi: Register LASI power-off feature as sys_off_handler <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e71d47dc2a6c9a1ec83f015c7c3dd87b635ffcda>`__
- parisc: Drop the pa7300lc LPMC handler
- parisc: traps: Drop cpu_lpmc function pointer
- `parisc: Use page table locks only if DEBUG_KERNEL is enabled <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=51c70a09c315fd2ad0c836392c6dab53839a59d3>`__
- `parisc: Fix /proc/cpuinfo output for lscpu <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9f5ba4b3e1b3c123eeca5d2d09161e8720048b5c>`__
- `parisc: Use generic mmap top-down layout and brk randomization <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3033cd4307681c60db6d08f398a64484b36e0b0f>`__
- `parisc: Add 32-bit eBPF JIT compiler <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=ceb0e7267693d3e6c43bd65695cd79d7c072a42a>`__
- `parisc: Add 64-bit eBPF JIT compiler <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c95e269773b31a93515e201fc4bce02d491216c2>`__
- parisc: Add eBPF JIT compiler glue code and Makefile
- parisc: Fix comment on Elf64 function descriptor
- `parisc: Wire up eBPF JIT compiler <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4800a6215e335c6dade05e10c8fdbf919c04a3a7>`__
- parisc: unaligned: Simplify 32-bit assembly in emulate_std()
- parisc: Avoid ioremap() for same addresss in iosapic_register()
- `parisc: led: Reduce CPU overhead for disk & lan LED computation <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=358ad816e52d4253b38c2f312e6b1cbd89e0dbf7>`__ (backported to all)
- parisc: Makefile: Adjust order in which drivers should be loaded
- parisc: dino: Convert dino PCI bus driver to use arch_initcall()
- parisc: hppb: Convert HP PB bus driver to use arch_initcall()
- parisc: eisa: Convert HP EISA bus driver to use arch_initcall()
- parisc: ccio: Convert CCIO driver to use arch_initcall()
- parisc: gsc: Convert GSC bus driver to use arch_initcall()
- parisc: lba: Convert LBA PCI bus driver to use arch_initcall()
- parisc: led: Move register_led_regions() to late_initcall()
- parisc: sba_iommu: Convert SBA IOMMU driver to use arch_initcall()
- parisc: iosapic: Convert I/O Sapic driver to use arch_initcall()
- parisc: wax: Initialize wax driver via arch_initcall()
- parisc: asp: Initialize asp driver via arch_initcall()
- parisc: lasi: Initialize LASI driver via arch_initcall()
- `parisc: led: Fix LAN receive and transmit LEDs <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4db89524b084f712a887256391fc19d9f66c8e55>`__ (backported to all)
- `parisc: led: Rewrite LED/LCD driver to utilizize Linux LED subsystem <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=789e527adfc335681ea4c3e347e8b500753d4fde>`__
- parisc: chassis: Do not overwrite string on LCD display
- parisc: ccio-dma: Create private runway procfs root entry
- parisc: sba_iommu: Fix build warning if procfs if disabled
- parisc: sba: Fix compile warning wrt list of SBA devices
- parisc: sba-iommu: Fix sparse warnigs
- parisc: ccio-dma: Fix sparse warnings
- parisc: iosapic.c: Fix sparse warnings
- parisc: drivers: Fix sparse warning
- parisc: irq: Make irq_stack_union static to avoid sparse warning
- `parisc: shmparam.h: Document aliasing requirements of PA-RISC <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3f091387a39795812aab4303949bbc9baa22c077>`__
- parisc: Prepare for Block-TLB support on 32-bit kernel
- `parisc: BTLB: Clear possibly existing BTLB entries <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=eda205211a522312b667d5bd25d58bee8504c09e>`__
- `parisc: BTLB: Add BTLB insert and purge firmware function wrappers <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=510610f96d65277940a02f47d7bc7a06c8a2ab7a>`__
- parisc: BTLB: \_edata symbol has to be page aligned for BTLB support
- parisc: firmware: Simplify calling non-PA20 functions
- `parisc: BTLB: Initialize BTLB tables at CPU startup <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e5ef93d02d6c9cc3a14e7348481c9e41a528caa1>`__
- `linux/export: fix reference to exported functions for parisc64 <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=08700ec705043eb0cee01b35cf5b9d63f0230d12>`__
- `parisc: Fix crash with nr_cpus=1 option <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d3b3c637e4eb8d3bbe53e5692aee66add72f9851>`__ (backported to v5.18+)
- `parisc: Restore \__ldcw_align for PA-RISC 2.0 processors <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=914988e099fc658436fbd7b8f240160c352b6552>`__ (backported to all) (**IMPORTANT**)

.. _kernel_6.7:

Kernel 6.7
----------

- `parisc: Add nop instructions after TLB inserts <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=ad4aa06e1d92b06ed56c7240252927bd60632efe>`__ (backported to all)
- parisc: sba-iommu: Fix comment when calculating IOC number
- parisc: Add some missing PDC functions and constants
- `parisc: Allow building uncompressed Linux kernel <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=01fef8267390ccb6e763a8aa90b6a10385aa3145>`__
- **parisc/power: Add power soft-off when running on qemu** (backported to v6.0+)
- parisc/power: Trivial whitespace cleanups and license update
- parisc: Move parisc_narrow_firmware variable to header file
- parisc/firmware: Use PDC constants for narrow/wide firmware
- `parisc/agp: Use 64-bit LE values in SBA IOMMU PDIR table <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=86bb854d134f4429feb35d2e05f55c6e036770d2>`__
- parisc/pdc: Add width field to struct pdc_model
- parisc: Show default CPU PSW.W setting as reported by PDC
- `fbdev: stifb: Make the STI next font pointer a 32-bit signed offset <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8a32aa17c1cd48df1ddaa78e45abcb8c7a2220d6>`__ (backported to all)
- parisc: simplify smp_prepare_boot_cpu()
- parport: gsc: mark init function static
- `parisc/pgtable: Do not drop upper 5 address bits of physical address <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=166b0110d1ee53290bd11618df6e3991c117495a>`__ (backported to all)
- `parisc: Prevent booting 64-bit kernels on PA1.x machines <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a406b8b424fa01f244c1aab02ba186258448c36b>`__ (backported to v6.0+)''
- `parisc: fix mmap_base calculation when stack grows upwards <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5f74f820f6fc844b95f9e5e406e0a07d97510420>`__ (backported to v6.6+]
- `parisc/power: Fix power soft-off when running on qemu <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=793838138c157d4c49f4fb744b170747e3dabf58>`__ (backported to v6.0+)
- `prctl: Disable prctl(PR_SET_MDWE) on parisc <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=793838138c157d4c49f4fb744b170747e3dabf58>`__ (backported to v6.6) - backport to be checked!
- parisc: Replace strlcpy() with strscpy()
- **parisc: Mark ex_table entries 32-bit aligned in assembly.h** (backported to v6.0+)
- **parisc: Mark ex_table entries 32-bit aligned in uaccess.h** (backported to v6.0+)
- **parisc: Mark altinstructions read-only and 32-bit aligned** (backported to v6.0+)
- **parisc: Mark jump_table naturally aligned** (backported to v6.0+)
- **parisc: Mark lock_aligned variables 16-byte aligned on SMP** (backported to v6.0+)
- **parisc: Ensure 32-bit alignment on parisc unwind section** (backported to v6.0+)
- **parisc: Use natural CPU alignment for bug_table** (backported to v6.0+)
- `parisc: Drop the HP-UX ENOSYM and EREMOTERELEASE error codes <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e5f3e299a2b1e9c3ece24a38adfc089aef307e8a>`__ (backported to all)
- `parisc: Reduce size of the bug_table on 64-bit kernel by half <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=43266838515d30dc0c45d5c7e6e7edacee6cce92>`__
- `parisc: Fix asm operand number out of range build error in bug table <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=487635756198cad563feb47539c6a37ea57f1dae>`__

.. _kernel_6.8:

Kernel 6.8
----------

- `bcachefs: Fix build on parisc by avoiding \__multi3() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=eba38cc7578bef94865341c73608bdf49193a51d>`__
- `ipv6: Ensure natural alignment of const ipv6 loopback and router addresses <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=60365049ccbacd101654a66ddcb299abfabd4fc5>`__
- `parisc/firmware: Fix F-extend for PDC addresses <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=735ae74f73e55c191d48689bd11ff4a06ea0508f>`__ (backported to all)
- `parisc/power: Fix power soft-off button emulation on qemu <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=6472036581f947109b20664121db1d143e916f0b>`__ (backported to v6.0+)
- `arch/parisc: Detect primary video device from device instance <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=ca6c080eef42e4149110f79cf73a48a6ec4e965d>`__
- '''`fbdev: stifb: Fix crash in stifb_blank() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4b088005c897a62fe98f70ab69687706cb2fad3b>`__
- `parisc: Make RO_DATA page aligned in vmlinux.lds.S <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2751153b9945c31eb905deb9fbe2d7f127b4b34c>`__
- `parisc: Check for valid stride size for cache flushes <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b9402e3b97289ca9e0f0f79f4df64bd6c9176a86>`__
- `parisc: Prevent hung tasks when printing inventory on serial console <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=c8708d758e715c3824a73bf0cda97292b52be44d>`__ (backported to v6.0+)
- parisc: Drop unneeded semicolon in parse_tree_node()
- `parisc: Fix random data corruption from exception handler <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8b1d72395635af45410b66cc4c4ab37a12c4a831>`__ (backported to v6.0+)
- `parisc: BTLB: Fix crash when setting up BTLB at CPU bringup <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=913b9d443a0180cf0de3548f1ab3149378998486>`__ (backported to v6.6+)
- `ipv6: Ensure natural alignment of const ipv6 loopback and router addresses <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=60365049ccbacd101654a66ddcb299abfabd4fc5>`__
- `parisc: Fix stack unwinder <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=882a2a724ee964c1ebe7268a91d5c8c8ddc796bf>`__
- `Revert "parisc: Only list existing CPUs in cpu_possible_mask" <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=82b143aeb169b8b55798d7d2063032e1a6ceeeb0>`__ (backported to v6.0+)
- parisc/ftrace: add missing CONFIG_DYNAMIC_FTRACE check
- parisc/kprobes: always include asm-generic/kprobes.h

.. _kernel_6.9:

Kernel 6.9
----------

- parisc: avoid c23 'nullptr' idenitifier
- `parisc/unaligned: Rewrite 64-bit inline assembly of emulate_ldd() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e5db6a74571a8baf87a116ea39aab946283362ff>`__ (backported to v6.0+)
- `parisc: Fix ip_fast_csum <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a2abae8f0b638c31bb9799d9dd847306e0d005bd>`__ (backported to all)
- `parisc: Fix csum_ipv6_magic on 32-bit systems <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4408ba75e4ba80c91fde7e10bccccf388f5c09be>`__ (backported to all)
- `parisc: Fix csum_ipv6_magic on 64-bit systems <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4b75b12d70506e31fc02356bbca60f8d5ca012d0>`__ (backported to all)
- `parisc: Strip upper 32 bit of sum in csum_ipv6_magic for 64-bit builds <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=0568b6f0d863643db2edcc7be31165740c89fa82>`__ (backported to all)
- `parisc: Use irq_enter_rcu() to fix warning at kernel/context_tracking.c:367 <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=73cb4a2d8d7e0259f94046116727084f21e4599f>`__
- `parisc: Show kernel unaligned memory accesses <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=94a1b192290c9fdb33085ecacf82afb27bd63fa8>`__
- `parisc: Avoid clobbering the C/B bits in the PSW with tophys and tovirt macros <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=4603fbaa76b5e703b38ac8cc718102834eb6e330>`__ (backported to v5.10+)
- parisc: make parisc_bus_type const
- `parisc: led: Convert to platform remove callback returning void <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=26dd48780bd2232a8f50f878929a9e448b7fd531>`__

.. _kernel_6.10:

Kernel 6.10
-----------

- parisc/math-emu: Remove unused struct 'exc_reg'
- `parisc: Define sigset_t in parisc uapi header <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=487fa28fa8b60417642ac58e8beda6e2509d18f9>`__ (backported to v6.0+)
- `parisc: Define HAVE_ARCH_HUGETLB_UNMAPPED_AREA <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d4a599910193b85f76c100e30d8551c8794f8c2a>`__ (backported to v6.0+)
- `parisc: Try to fix random segmentation faults in package builds <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=72d95924ee35c8cd16ef52f912483ee938a34d49>`__ (backported to v6.6+) \ **THE MOST IMPORTANT PATCH TO FIX CACHE ISSUES.**\ 
- `parisc: use generic sys_fanotify_mark implementation <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=403f17a330732a666ae793f3b15bc75bb5540524>`__ (backported to v5.11+)
- `parisc: use correct compat recv/recvfrom syscalls <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=20a50787349fadf66ac5c48f62e58d753878d2bb>`__ (backported to v5.4+)

.. _kernel_6.11:

Kernel 6.11
-----------

- parisc: Clean up unistd.h file
- `parisc: Add 32-bit gettimeofday() and clock_gettime() vDSO functions <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=e23d9c0b5266e083a654b62756ede0dd5bf1ac3d>`__
- `parisc: Add 64-bit gettimeofday() and clock_gettime() vDSO functions <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5f55e098b8d032ef25b576d18fb91b16ef2d1c41>`__
- `parisc: Add support for CONFIG_SYSCTL_ARCH_UNALIGN_NO_WARN <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=cbade823342cd013f1fbd46f6e3b74825fecbc16>`__
- parisc: Fix warning at drivers/pci/msi/msi.h:121
- parisc: Use max() to calculate parisc_tlb_flush_threshold
- `wireguard: allowedips: avoid unaligned 64-bit memory accesses <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=948f991c62a4018fb81d85804eeab3029c6209f8>`__
- `parisc: fix unaligned accesses in BPF <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=1fd2c10acb7b35d72101a4619ee5b2cddb9efd3a>`__
- `parisc: fix a possible DMA corruption <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=7ae04ba36b381bffe2471eff3a93edced843240f>`__
- `parisc: Delay write-protection until mark_rodata_ro() call <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=213aa670153ed675a007c1f35c5db544b0fefc94>`__ (backported to v6.10+)

.. _kernel_6.12:

Kernel 6.12
-----------

- `parisc: Convert to generic clockevents <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b5ff52be891347f8847872c49d7a5c2fa29400a7>`__
- `parisc: Fix stack start for ADDR_NO_RANDOMIZE personality <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=f31b256994acec6929306dfa86ac29716e7503d6>`__ (backported to v5.2+)
- `parisc: Fix 64-bit userspace syscall path <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=d24449864da5838936669618356b0e30ca2999c3>`__ (backported to v4.19+)
- `parisc: Fix itlb miss handler for 64-bit programs <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=9542130937e9dc707dd7c6b7af73326437da2d50>`__ (backported to v4.19+)
- `parisc: Allow mmap(MAP_STACK) memory to automatically expand upwards <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=5d698966fa7b452035c44c937d704910bf3440dd>`__ (backported to v5.10+)
- parisc: Use PRIV_USER instead of hardcoded value
- parisc: pdc_stable: Constify struct kobj_type
- `crypto: xor - fix template benchmarking <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=ab9a244c396aae4aaa34b2399b82fc15ec2df8c1>`__

.. _kernel_6.13:

Kernel 6.13
-----------

- `parisc/ftrace: Fix function graph tracing disablement <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=a5f05a138a8cac035bf9da9b6ed0e532bc7942c8>`__ (backported to v5.16+)
- `perf tools parisc: Support syscall header <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=02f2d58f235ddcf8bc33c084cff84fd685a1be11>`__
- parisc: get rid of private asm/unaligned.h

.. _kernel_6.14:

Kernel 6.14
-----------

- `parisc: Temporarily disable jump label support <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3599bae489d86fbabe039f9a2ab5472ffb04f7f1>`__ (backported to v6.12+)
- parisc: Remove memcpy_toio and memset_io
- `parisc: add vdso linker script to 'targets' instead of extra-y <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=36b2f3aefc486ea2f773db5c42539a52e1fcbcb3>`__

Kernel 6.15
-----------

- Input: gscps2 - Describe missing function parameters
- parisc: led: Use scnprintf() to avoid string truncation warning
- parisc: Fix formatting errors in io.c
- parisc: Remove memcpy_fromio
- parisc: perf: use named initializers for struct miscdevice
- parisc: PDT: Fix missing prototype warning
- `parisc: Fix double SIGFPE crash <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=de3629baf5a33af1919dec7136d643b0662e85ef>`__ (backported to all)

Kernel 6.16
-----------

- `parisc: Temporarily disable jump label support <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=3599bae489d86fbabe039f9a2ab5472ffb04f7f1>`__ (backported to v6.12+)
- `parisc: Check region is readable by user in raw_copy_from_user() <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=91428ca9320edbab1211851d82429d33b9cd73ef>`__
- parisc: Rename pte_needs_flush() to pte_needs_cache_flush() in cache.c
- parisc: Define and use set_pte_at()
- parisc: Try to fixup kernel exception in bad_area_nosemaphore path of do_page_fault()
- parisc: Drop WARN_ON_ONCE() from flush_cache_vmap
- `parisc: Revise gateway LWS calls to probe user read access <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=f6334f4ae9a4e962ba74b026e1d965dfdf8cbef8>`__
- `parisc: Revise __get_user() to probe user read access <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=89f686a0fb6e473a876a9a60a13aec67a62b9a7e>`__
- parisc: Makefile: fix a typo in palo.conf
- `parisc: Makefile: explain that 64BIT requires both 32-bit and 64-bit compilers <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=305ab0a748c52eeaeb01d8cff6408842d19e5cb5>`__


.. _done_items:

DONE items
==========

.. _debian_kernel_and_userspace_programs:

Debian kernel and userspace programs
------------------------------------

- PALO (http://git.kernel.org/cgit/linux/kernel/git/deller/palo.git/)

  - DONE: increase palo command line from 128 bytes (max from kernel is 1024 currently) -> fixed in palo 1.92.
  - DONE: accept /boot/ in front of files, strip path (e.g. "/boot") when given for kernel/ramdisk
  - DONE: Migrate to git.kernel.org -> done: http://git.kernel.org/cgit/linux/kernel/git/deller/palo.git/
  - DONE: add gzip-support (allow loading gzipped vmlinux files)
  - DONE: in interactive mode, add reboot option command
  - DONE: `Unbreak loading Linux kernels bigger than 16 MB <https://git.kernel.org/cgit/linux/kernel/git/deller/palo.git/commit/?id=70bd7a9a41e318c0575755a78c4d18ad97495c47>`__ (fixed in palo 1.96, Oct. 2016)
  - DONE: Du to alternative coding in 4.20, ship debian kernel as SMP variant only (Kernel 4.20)
  - DONE: PALO: Enable "list directory content" of other partiton than /boot (done in v2.22)

Debian kernel / Generic Kernel bugs fixed

- 

  - DONE: Fixed 64bit SMP kernel & sticon on machines with more than 4GB RAM (Kernel 3.12)
  - DONE: Lots of syscall fixes regarding compat kernel
  - DONE: send patches to remove UP from debian kernels, use sil680 instead of siimage, see http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=721191 -> fixed in 3.12 / 3.13
  - DONE: Enable BMC in c8000 default kernel config - ipmi driver (CONFIG_IPMI_HANDLER=y) - http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=747482
  - DONE: remove HIL RTC from debian kernel - it creates a backtrace at boot because we have already another rtc0 active.
  - DONE: add ppdev driver, needed by CUPS, systemd reports missing module load (3.17 & 3.16 kernel)
  - DONE: Add crc-modules, event, isofs, jfs, mouse, sata, sound, squashfs, udf, uinput, scsi-common, scsi-extra, loop, serial and usb-serial debian-installer packages
  - DONE: Kernel build problem: a) disable -mfast-indirect-calls in Makefile, b) gcc compiler bug in > gcc-4.8.3-3
  - DONE: Drop HPUX-support from kernel (Kernel 4.0)
  - DONE: implemented HUGE pages
  - DONE: kernel timerfd_settime/timerfd_gettime issue, test with debian package: liblinux-fd-perl -> https://patchwork.kernel.org/patch/7951661/, backported to all kernels
  - DONE: kernel ptrace bug: http://thread.gmane.org/gmane.linux.ports.parisc/26273, possible fix: https://patchwork.kernel.org/patch/8063301/
  - DONE: Implement CONFIG_DEBUG_STACK_USAGE, in kernel 4.6
  - DONE: Use 32bit exception table entries, in kernel 4.6
  - DONE: implemented seccomp syscal, including SECCOMP_MODE_STRICT and mode 2
  - DONE: Fixed FTRACE support (http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=366dd4ea9d5f0eb78fdf4982d76506f99480ec0a)
  - DONE: Add syscall ftrace support
  - DONE: Simplify get_user()/put_user() with regard to %sr0/%sr2 usage.
  - DONE: Add tracehook support (needed for seccomp testcases, gdb, ...)
  - DONE: Added high-resolution sched_clock clocksources in 4.7, needed for debian packages: ismrmrd, haskell-http-conduit, ...
  - DONE: Detect physically broken memory via Page Deallocation Table (PDT), initial support in kernel 4.13
  - DONE: Make kernel self-decompressing (instead of palo reading gzipped kernels)
  - DONE: fix %pF and %pS usage in all kernel code
  - DONE: Enabled BMC/IPMI including Remote power on/off on C8000 and rp3410 machines (kernel 4.17 and above)
  - DONE: use CONFIG_COMPAT_BINFMT_ELF and get rid of arch/parisc/kernel/binfmt_elf32.c
  - DONE: Fix kernel writing core file, gdb reporting: BFD: Warning: /tmp/core.http.1655.ls3011.1465554447 is truncated: expected core file size >= 2187264, found: 2121728.
  - DONE: Find way to extract vmlinux out of vmlinuz (Kernel 4.19)
  - DONE: Implement alternative coding for UP & SMP, one debian kernel for UP and SMP, optimizations to avoid cache flushes on cache-less systems and other qemu-related speed-ups to avoid unnecessary syncronization (Kernel 4.20)
  - DONE: IRQ Balancing was broken, http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=f73493eb4aecdd6edd8a749890e6e2dcfa0a7978
  - DONE: Added kgdb and kprobes in kernel 5.2, by Sven Schnelle
  - DONE: report on C8000 one socket with 2 cores
  - DONE: Check Kernel boot code if RAM of machine is big enough (e.g. is booting on 710 machine with 60MB possible?) - https://patchwork.kernel.org/patch/10888283/
  - DONE: convert to SPARSEMEM, instead of DISCONTIGMEM
  - DONE: (NOT POSSIBLE:) Enable huge pages on kernel text for C8000 as well - not possible, will crash due to required equivalent aliasing of caches.
  - DONE: Enable \__ro_after_init
  - DONE: add dynamic ftrace support - provided by Sven in kernel 5.3
  - DONE: Fix fallouts from strace: http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=b7dc5a071ddf69c0350396b203cba32fe5bab510,
  - DONE: implement kexec syscall
  - DONE: Analyze futex hang (CPU soft lockup) situation with test-lock testcase in the libprelude debian package - fixed by `parisc: Add atomic64_set_release() define to avoid CPU soft lockups <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=be6577af0cef934ccb036445314072e8cb9217b9>`__
  - DONE: Add TOC handler, sending TOC via IPMI interface "toc s"
  - DONE: autofs/automounter is broken, patch sent to list, https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=892953
  - DONE: Try enabling DEBUG_VM_PGTABLE in kernel
  - DONE: SLAB breaks, SLUB works (works in kernel v5.17)
  - DONE: implement .con_font_set (consw) in sticon.c to be able to change fonts at runtime (since v5.10)
  - DONE: Implement kernel `VDSO <VDSO>`__ support - `Randolphs patch <https://lore.kernel.org/linux-parisc/4544A34A.6080700@tausq.org/>`__
  - DONE: Fixed PTRACE_GETREGS/PTRACE_SETREGS for compat case, fixes strace errors
  - DONE: add CONFIG_LOCKDEP support (v6.4)
  - DONE: kernel: drop chassis_power_off() and switch to register_sys_off_handler() - https://patchwork.kernel.org/project/linux-parisc/patch/20230812203759.395651-1-deller@gmx.de/
  - DONE: kernel: drop arch/parisc/kernel/pa7300lc.c
  - DONE: improve Unaligned handlers, e.g. emulate_std() -> clean up assembly, use LR on 32-bit...(%1 and %R1 for register #1)
  - DONE: Fix kernel 6.4 boot crash -> https://patchwork.kernel.org/project/linux-parisc/patch/20230803062404.2373480-1-rppt@kernel.org/
  - DONE: matoro - ia64 io_uring: https://lore.kernel.org/io-uring/27b05e18b406621584b29653e5aafd43@matoro.tk/T/#t
  - DONE: mariadb fails with io_uring, testcase https://github.com/axboe/liburing/blob/master/examples/io_uring-test.c fails too. (Jens bought a C3000 workstation and added it to the machine test pool.)
  - DONE: push fbcon-64-bit-sti patches upstream: https://github.com/hdeller/linux/commits/drm-misc-next-with-64bit-console-final
  - DONE: Fix kexec on PA8800/PA8900 CPUs: how to restart the CPUs on these systems?
  - DONE: Implement ARCH_WANT_DEFAULT_TOPDOWN_MMAP_LAYOUT as in https://lwn.net/ml/linux-kernel/20190724055850.6232-14-alex@ghiti.fr/
  - DONE: Add eBPF JIT compiler (in kernel 6.6)
  - DONE: kernel >= 6.1.5 fails to boot on panama, boots works with "no-alternatives" boot option, Reason: flush_dcache_page() re-enabled the IRQs which led to the spinlock hang in aio_complete(), fix: https://lore.kernel.org/linux-parisc/ZGyawdtBhNnvvTv3@shell.armlinux.org.uk/T/#m96bc8e874d76b3b7bab8b40954b68e6cfb81ee2b, fix backported to kernels >= 5.18, bug was in arm and nios2 arches too.
  - DONE: latest kernel: maxcpus=1 works, nr_cpus=1 causes a crash. (patch sent)
  - DONE: can not use register 29 in ASM_EXCEPTIONTABLE_VAR in emulate_ldd on 64bit kernel, `Fix is here
    <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=8b1d72395635af45410b66cc4c4ab37a12c4a831>`__

- Debian installer:

  - DONE: test xorriso (Thomas Schmitt, scdbackup)
  - DONE: Build real debian-installer CD. See: https://wiki.debian.org/Simple-CDD/Howto
  - DONE: we need a kernel debian package (3.10 based or higher)
  - DONE: `palo-installer: fix palo entry "root=/dev/sda5" to become "root=UUID=XXXX" <http://anonscm.debian.org/cgit/d-i/attic/palo-installer.git/>`__
  - DONE: Fix initramfs: Bug#1027915: systemd requires /run to be mounted with a minimum size of 20MB:
    https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1027915, patch sent


- binutils:

  - DONE: Add linker -fpie support, https://sourceware.org/bugzilla/show_bug.cgi?id=18421, debian package iceweasel and others
  - DONE: Using "gcc -o /dev/null" gives "ld: final link failed: File truncated" error: https://sourceware.org/bugzilla/show_bug.cgi?id=19526

- gcc:

  - DONE: Linker reports "XXX referenced in section \`.data.rel.ro.local' of YYY: defined in discarded section \`.text._ZXXX"

    - `binutils fix in bfd/elf32-hppa <https://sourceware.org/git/gitweb.cgi?p=binutils-gdb.git;a=commitdiff;h=da6e19a92645583da12dc2d15f54d2bd240fd3f2;hp=7d8a636c9a234354c9b2ecf01b6e405bece190a1>`__ and `gcc upstream bug report <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=67834>`__ and `Debian binutils bug report <https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=801531>`__

  - DONE: Qt5/KDE signal & slot issue: Pointers to method types need canonicalization before comparison, fixed in gcc: https://gcc.gnu.org/bugzilla/show_bug.cgi?id=68079
  - DONE: Add wiki page about kernel cross-compiling

- glibc:

  - DONE: Problem building libcap-ng...The problem is the call to \__tls_get_addr clobbers the value loaded into %r26 for the printf call. -> http://gcc.gnu.org/bugzilla/show_bug.cgi?id=59777
  - DONE: O_SYNC wrongly defined in glibc (typo in hppa \__O_SYNC define in <bits/fcntl.h>)
  - DONE: eglibc mmap bug with localedef (see debian bugzilla http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=741243)
  - DONE: Wrong \__gmon_start\_\_ symbol, showed in vlc - linker does not drop dependencies to unneeded shared libs, https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=770741. glibc bug report: https://sourceware.org/bugzilla/show_bug.cgi?id=19170
  - DONE: nss - dladdr from glibc returns wrong names on hppa, http://article.gmane.org/gmane.linux.ports.parisc/26290, Solution: https://sourceware.org/bugzilla/show_bug.cgi?id=19415
  - DONE: gcc userspace support for variable CAS (gcc atomic ops, \__sync_XXX), initial support in kernel 3.17, http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=89206491201cbd1571009b36292af781cef74c1b)

    - gcc-4.9 commit: https://gcc.gnu.org/viewcvs/gcc?view=revision&revision=227914
    - gcc-5.0 commit: https://gcc.gnu.org/viewcvs/gcc?view=revision&revision=227913
    - packages needing rebuild: libatomic-ops (gauche and libgc have copies of libatomic-ops included), x265, ceph, lizardfs, firebird2.5, gauche, idba, mapsembler2, padevchooser, filezilla, idba, http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=785654
    - \__sync_val_compare_and_swap_8 was missing in: openvdb, idba, lizardfs, ...

  - DONE: `Misaligned access in res_query.c HEADER struct <https://sourceware.org/bugzilla/show_bug.cgi?id=20243>`__ fixed in `this glibc commit <https://sourceware.org/git/?p=glibc.git;a=commit;h=05dec22d7be722987ff07aebf9690f6078b3c4e9>`__
  - DONE: "exim4(11935): unaligned access to 0xf7ebadcd at ip 0xf5f7e307" -> https://sourceware.org/bugzilla/show_bug.cgi?id=30750 and https://sourceware.org/bugzilla/show_bug.cgi?id=20243, resolved in glibc 2.38

- Debian userspace:

  - DONE: qt4-x11: Improve atomic support on parisc (https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=741190)
  - DONE: haskell (ghc) hangs: futex(0xea00d08e, FUTEX_WAIT_PRIVATE, 2, NULL) = -1 EINVAL (Invalid argument). Solution: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=756260#15
  - DONE: port openmpi package: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=776730
  - DONE: Fix msgpuck build breakage: https://buildd.debian.org/status/package.php?p=msgpuck&suite=sid
  - DONE: Fix sbuild not returning used disc size: /usr/share/perl5/Sbuild/Build.pm line 2786, see https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=923484
  - DONE: Why does mc hangs when exiting with F10? - https://midnight-commander.org/ticket/3887 - endianess bug in mc source code
  - DONE: capnproto - testsuite was wrong: PARISC uses mips-floatingpoint NANs - https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=781787
  - DONE: fix strace in debian: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1030587 -> ask to switch to v6.4 instead
  - DONE: Activate LARGE FILE SUPPORT generally on hppa: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1020335
    - QEMU-user failed to build on 32-bit non-LFS arches: rrdtool, devscripts, ohcount, guile-ssh...


- QEMU:

  - DONE: Add kernel fw_cfg support (drivers/firmware/qemu_fw_cfg.c)
  - DONE: Fix mouse in with HP-UX 10 and 11 - reported by Stefano Repici
  - DONE: Add bigger fonts to STI ROM / SeaBIOS
  - DONE: Implement TOC in qemu (via qemu monitor -> NMI)
  - DONE: Fix X11 cursor emulation in HP-UX with help of Xorg 11.6.6 sources, fixed in qemu v7.1
  - DONE: Fix serial port assignment and pass-through, fixed in qemu v7.1
  - DONE: Check why ldd doesn't work with qemu chroot -> misexecuttion of proberi instruction: https://lists.nongnu.org/archive/html/qemu-devel/2022-08/msg02338.html
  - DONE: Fix glibc iconv issues with qemu: https://sourceware.org/bugzilla/show_bug.cgi?id=29583
  - DONE: bug in kernel fldw unalignment exception handler which lets libime fail to build on real hardware (but builds with qemu!)
  - DONE: Add vDSO trampoline to qemu-user
  - DONE: `qemu-user: trashes argv0 breaking multi-call binaries <https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=970460>`__, leads to build failures in lilypond, fceux, perl, sane-backends ... Fixed, needs `kernel v5.12+ <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=2347961b11d4079deace3c81dceed460c08a8fc1>`__ and `qemu v6.0+ <https://git.qemu.org/?p=qemu.git;a=commit;h=6e1c0d7b951e19c53b8467e8bc4b71ee73a394ea>`__, or alternatively apply `linux-user: handle binfmt-misc P flag as a separate exe name <https://github.com/hdeller/qemu-hppa/commit/540e8fb618e66b4c172cc528c12580bb09e301b6>`__

- DONE: BTLB support in SeaBIOS, QEMU and Kernel

.. _abi_breakages_e.g._dropped_hp_ux_support:

ABI breakages (e.g. dropped HP-UX support)
------------------------------------------

- **Kernel 3.14 (EWOULDBLOCK)**

  - `We dropped HP-UX support in kernel 3.14 by changing EWOULDBLOCK==EAGAIN. <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=f5a408d53edef3af07ac7697b8bc54a755628450>`__ HP-UX could be run in a VM instead or since machines are relatively cheap now, if someone needs hpux, they can buy a machine for it (or use qemu)

- **Kernel 3.16 (SIGRTMIN)**

  - `Changed SIGRTMIN to 32 (from 37) <http://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=1f25df2eff5b25f52c139d3ff31bc883eee9a0ab>`__
  - `debian glibc report <http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=766605>`__ and `debian kernel bug report <http://bugs.debian.org/cgi-bin/bugreport.cgi?bug=766635>`__
  - September 7th 2014: First delivery with vmlinux-3.16.0-4 (aka linux-image-3.16.0-4-parisc64-smp_3.16.7-1_hppa.deb)
  - How to install:

    - Install the new glibc debian package, then install systemd, then install the new kernel package (this will create a new initrd), reboot

  - rebuild of the following packages was needed:

    - unxz-utils: rebuild unxz to fix usage of SIGXFSZ
    - strace and gdb: fix signal names/numbers
    - libgc: gives "Cannot set SIG_THR_RESTART handler" error since SIG_THR_RESTART has been SIGXCPU which is now used for threads
    - (maybe) gcc-4.9 which links against libgc...? (affects objc related code only)
    - dietlibc (patch sent upstream) - https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=769105 TODO: make patch for dietlibc to use LONG_LONG_PAIR for ftruncate64() and then rebuild util-vserver package

- **Kernel 4.19 (ENOTSUP)**

  - `Dropped ENOTSUP (252) constant from kernel with v4.19 <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=93cb8e20d56be40c541475f77b5f565fbb385a4b>`__. Gave problems and parisc is only platform which defined it because of HP-UX, see libuv bug: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=900237. Nevertheless rebuild of libattr, libseliux and fakeroot necessary.

- **Kernel 5.10: Define O_NONBLOCK to become 000200000 (instead of 000200004):**

  - `parisc: Define O_NONBLOCK to become 000200000 <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=75ae04206a4d0e4f541c1d692b7febd1c0fdb814>`__
  - `parisc: Add wrapper syscalls to fix O_NONBLOCK flag usage <http://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=44a4c9e443674e6cd3368d3e642dfe9c429d5525>`__
  - `GLIBC PATCH: Remove obsolete defines for HPUX support from fcntl.h and update O_NONBLOCK. <https://sourceware.org/git?p=glibc.git;a=commit;h=a24f414ba11a7acdf3c5ccec3fb327fc72166556>`__
  - `dietlibc Debian bug report <https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=974690>`__

- **Kernel 6.1 (MADV_WIPEONFORK, MADV_KEEPONFORK, ...)**

  - This patch `parisc: Align parisc MADV_XXX constants with all other architectures <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=71bdea6f798b425bc0003780b13e3fdecb16a010>`__ changed some MADV values to have the same number as other platforms (exception: alpha arch). Even if that's an ABI change, the patch includes a wrapper to translate the values, so that old binaries will continue to work. There were build issues in aws-crt-python, qemu-user, tor browser and boringssl.

- **glibc 2.35 and Kernel < 6.10 (compat recv/recvfrom, sys_fanotify_mark)**

  - Arnd Bergmann noticed that various compat syscalls changed how they were implemented in glibc and kernel. His path series `linux system call fixes <https://lore.kernel.org/lkml/20240620162316.3674955-1-arnd@kernel.org/>`__ fixes this and suggests that people should use Debian bookworm with glibc >= 2.35 and Linux kernels >= 6.10.

- **glibc 2.41 and executable stack in dlopen'ed share libaries**

  - With glibc 2.41, any package with a shared library that may be dlopen'd must be rebuilt with gcc-14 or later.
    Otherwise you may get error messages like: "cannot enable executable stack as shared object requires: Invalid argument".

.. _parisc_linux.org_homepage_and_debian_build_server_infrastructure_work:

parisc-linux.org Homepage and Debian build server infrastructure work
---------------------------------------------------------------------

- DONE: Ask Thibaut to add ftp mirror of http://ftp.parisc-linux.org/debian_ports on http://www.fr.parisc-linux.org/
- DONE: Set up a buildd server for debian-ports/unstable & experimental -> http://unstable.buildd.net/index-hppa.html
- DONE: contact pannonmage@gmail.com (Nemeth Balint, Budapest) to set up buildd server - mail from 04.08.2013
- Offer from Mike Hosken <mike@csystems.co.nz> for A400 system as buildd - mail from 18.09.2014 and 17.05.2015
- DONE: set up buildd server on ESIEE's A500 (Thibaut) - mail from 13.08.2013
- DONE Sep 12 2014: migrate mail forward service for parisc-linux.org to A500 machine
- DONE Sep 12 2014: restore web-based http://ftp.parisc-linux.org service
- DONE Nov 24 2014: Import old hppa mailing list archives to gmane.org - http://dir.gmane.org/gmane.linux.ports.parisc
- Homepage migration:

  - DONE: Migration of parisc-linux.org website to https://parisc.wiki.kernel.org
  - DONE: Migration of wiki.parisc-linux.org website to https://parisc.wiki.kernel.org
  - DONE: Domain owner before: Matt Taggart <matt@lackof.org>, Expiration Date:09-Aug-2014; Since August 2014 is Helge Deller domain owner of parisc-linux.org
  - DONE: DNS takeover by Thibaut Varenet on 11/11/2013 - see: http://article.gmane.org/gmane.linux.ports.parisc/5478
  - DONE: former Server administrator of parisc-linux.org: Dann Frazier - dannf@dannf.org, former technical contact at HP: brett.johnson@hp.com, Since August 2014 administrator/owner: Helge Deller
- DONE: Improve text on https://www.openpa.net (2025)

.. _linus_torvalds_talking_or_working_about_parisc:

Linus Torvalds talking or working about parisc
----------------------------------------------

- https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=19b9ad67310ed2f685062a00aec602bec33835f0
- about atomics in parisc and sparc: https://lore.kernel.org/lkml/CAHk-=wispSt+JezguriGPKnJ0xOUWG_LFDgaM-NVJu6cVa+-xw@mail.gmail.com/
- While debugging a QEMU bug:

  - https://lore.kernel.org/lkml/CAHk-=wiZUidi6Gm_6XFArT621H7vAzhDA63zn2pSGJHdnjRCMA@mail.gmail.com/
  - https://lore.kernel.org/lkml/CAHk-=whWchFofUF9cai6QUixbZNGU=pqDxNUdtZvtBkbFc9QfQ@mail.gmail.com/

.. _testsuites_kernel_and_userspace_testing_tools:

Testsuites, kernel and userspace testing tools
==============================================

- GLIBC issues and todos: https://sourceware.org/glibc/wiki/PortStatus
- https://github.com/linux-test-project/ltp - Linux Test Project
- http://codemonkey.org.uk/projects/trinity/ - Trinity - A Linux System call fuzz tester.

.. _todo_drivers:

TODO Drivers
============

- write Lasi floppy driver
- write Suckyio floppy driver
- write spifi driver (rbrad)
- modify ncr53c8xx driver for Outfield (735 & 755)
- write GSC FDDI driver
- write Timi ASIC (74x) support
- EISA DMA support

.. _review_all_the_todo_entries_below:

Review all the todo entries below!
----------------------------------

- ns87415 dma doesn't work reliably on suckyio-systems
- fix HIL problem: ksoftirqd/0 eats 56% cpu (kernel 2.4 & kernel 2.6) - see FAQ: http://www.parisc-linux.org/faq/index.html and http://article.gmane.org/gmane.linux.ports.parisc/278/match=use+lower+nice+level+ksoftirqd+hil+enabled
