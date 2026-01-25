Linux on PA-RISC
================

.. image:: media/parisc-powered.png
   :width: 100px
   :align: right

- The PA-RISC project provides a *native* port of Linux to the PA-RISC
  architecture.

- Linux on PA-RISC is stable and runs on most PA-RISC machines and in
  a virtual :doc:`Qemu <qemu>` machine.

- Check the :doc:`PARISC FAQ <parisc_faq>` and :doc:`Hardware support
  <hardware_support>` if you have trouble installing Linux.

Linux distributions for PA-RISC machines
----------------------------------------

.. image:: media/Debian_logo.png
   :width: 100
   :align: right

Debian Linux
~~~~~~~~~~~~

- PA-RISC is a non-release architecture in the `Debian Ports
  <http://www.debian-ports.org>`__ project with more than
  12,000 Debian packages available.
- You may download the latest installation CD-ROM ISO image
  `which has been tested from the PA-RISC FTP server <http://ftp.parisc-linux.org/debian-cd/debian-12/debian-2025-01-hppa-CD.iso>`__.
  or `untested images from the debian CD image server <https://cdimage.debian.org/cdimage/ports/snapshots/>`__

.. image:: media/Gentoo-logo.png
   :width: 100
   :align: right

Gentoo Linux
~~~~~~~~~~~~

- PA-RISC is a fully supported architecture of Gentoo Linux.

- The `Gentoo hppa team <https://wiki.gentoo.org/wiki/Project:HPPA>`__
  provides `Gentoo Linux installation ISOs available for download
  <http://www.gentoo.org/main/en/where.xml>`__ and a
  `Handbook on how to install Gentoo Linux for PA-RISC
  <http://www.gentoo.org/doc/en/handbook/handbook-hppa.xml>`__.

.. image:: media/t2logo.png
   :width: 100
   :align: right

T2 System Development Environment
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- The `T2 System Development Environment <https://t2sde.org/>`__ Linux
  distribution provides a port to PA-RISC which you may download `here.
  <http://dl.t2sde.org/binary/>`__

Our sponsors
------------

- `OSUOSL - Oregon State University Open Source Lab <http://osuosl.org>`__

  - Hosting and support for the physical parisc servers "parisc" (since 2014)
    and "panama" (since 2017)

  - Hosting of a x86 virtual machine for the qemu-user based parisc build
    server "pasta" (since Jan 2021)

  - Hosting of this website and documentation, Linux kernel and palo git trees and
    parisc mailing lists via kernel.org domain (since Jan 2014)

- `Cypress Technology Inc <http://www.cypress-tech.com>`__ (`Jesse
  Dougherty <mailto:jesse@cypress-tech.com>`__) sponsored a `HP J6700 workstation
  <https://www.openpa.net/systems/hp-visualize_j6000_j6700.html>`__ with
  2 x 750MHz PA8700 CPUs, 4GB RAM and a 72GB disc which is used as
  `Debian buildd and Porterbox machine "parisc"
  <https://db.debian.org/machines.cgi?sortby=purpose&sortorder=dsc>`__ (Oct 2022 - Dec 2025)


- `Roberto C. Sánchez <mailto:roberto@debian.org>`__ sponsored a `HP rp3410 server
  <http://www.openpa.net/systems/hp-9000_rp3410_rp3440.html>`__ with one
  800 MHz PA8900 CPU which is used as `Debian buildd and Porterbox machine "panama"
  <https://db.debian.org/machines.cgi?sortby=purpose&sortorder=dsc>`__ (May 2017)


- `GALL EDV-Systeme GmbH <http://www.gall.de/>`__ sponsored
  `HP Visualize FX-2, FX-4 and FX-6 grahics cards
  <https://www.openpa.net/pa-risc_graphics.html#visfx>`__ (June 2023)

.. note::

   If you want to sponsor HP physical machines, graphics-cards or other
   hardware, or hosting services for virtual (x86) or physical (parisc)
   machines, please contact `me <mailto:deller@gmx.de>`__

PA-RISC Linux NEWS
------------------

Jan 2026
~~~~~~~~~
- Dave continues to make good progress on fixing 64-bit linker issues, e.g. this patch: `hppa64: Improve relocation handling for local symbols and add gc_section support <https://sourceware.org/git/?p=binutils-gdb.git;a=commit;h=b2c74dcb465ccec1289dde0e1c8d299d85bb34f8>`__
- Activated second CPU core in the panama build server (rp3410) and updated PDC, MP and BMC firmware.
- Sven added some :doc:`PA8800/PA8900 PIROM/SCRATCH eeprom documentation <pirom_scratch_eeprom>`.

Dec 2025
~~~~~~~~~
- `Michael Lorenz <mailto:macallan1888@gmail.com>`__ started on a :doc:`manual for programming the HCRX and PCI Visualize EG graphic cards <ngle_programming>`
- `Qemu 10.2 was released. <https://wiki.qemu.org/ChangeLog/10.2>`__
- The HP J6700 workstation which was sponsored by Cypress Technology Inc (Jesse Dougherty) and which was used as Debian buildd server "parisc" suddently broke after 3 years.  We are now looking for a new sponsor to replace that machine.

Oct 2025
~~~~~~~~~
- `Peter Maydell <mailto:peter.maydell@linaro.org>`__ posted a `patch for QEMU to fix floating point exception flags <https://patchew.org/QEMU/20251017085350.895681-1-peter.maydell@linaro.org>`__.
- `Soumyajyotii Ssarkar <mailto:soumyajyotisarkar23@gmail.com>`__ finished his `Google Summer of Code 2025 project and developed QEMU drivers for the LASI network and SCSI controller <https://gist.github.com/RemZapCypher/5b64559a8112c6f11e3bcf9668ebfa57>`__. His code will be merged soon upstream.
- Sven Schnelle and Dave Anglin started fixing gcc, binutils and the Linux kernel to allow building 64-bit shared applications.
- `Gabriel Brookman <mailto:brookmangabriel@gmail.com>`__ posted a `patch for QEMU to fix wrong emulation of fmpyadd <https://patchew.org/QEMU/20251009-hppa-correct-fmpyadd-size-bit-decoding-v1-1-f63bb6c3290c@gmail.com/>`__.
- `Peter Maydell <mailto:peter.maydell@linaro.org>`__ provided a `fix for the emulation of the FP registers <https://patchew.org/QEMU/20251017085350.895681-1-peter.maydell@linaro.org/>`__ in QEMU.
- `SeaBIOS-hppa v19 with support for 715 machine released <https://github.com/hdeller/seabios-hppa/releases/tag/seabios-hppa-v19>`__.

Sep 2025
~~~~~~~~~
- Maintenance update of `palo version 2.28 <https://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__
  released.

July 2025
~~~~~~~~~
- `Dave fixed a kernel bug which allowed the kernel to access read-protected memory <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=0905809b38bda1fa0b206986c44d846e46f13c1d>`__. This bug was found due to a testcase failure in the libunwind package.

May 2025
~~~~~~~~
- Dave was able to `fix some bugs in libunwind <https://github.com/libunwind/libunwind/issues/874>`__
- Helge `fixed a kernel bug which led to SIGFPE crashes <https://git.kernel.org/pub/scm/linux/kernel/git/torvalds/linux.git/commit/?id=de3629baf5a33af1919dec7136d643b0662e85ef>`__
  and the `relevant parts in QEMU <https://gitlab.com/qemu-project/qemu/-/commit/bd02070353f84354c03e3d16287e9e80e0875ec1>`__

Apr 2025
~~~~~~~~
- `Qemu v10 was released <https://wiki.qemu.org/ChangeLog/10.0>`__
- PA-RISC Linux project participates in the Google Summer of Code (GSOC) 2025 project with one
  QEMU project aiming to fully support the LASI network card and NCR 710 SCSI
  controller: https://wiki.qemu.org/Google_Summer_of_Code_2025

Mar 2025
~~~~~~~~
- With glibc 2.41, any package with a shared library that may be dlopen'd must be rebuilt with gcc-14 or later.
  Otherwise you may get error messages like: "cannot enable executable stack as shared object requires: Invalid argument".

Feb 2025
~~~~~~~~
- Dave fixed another long-standing glibc floating-point bug: `Miscompilation of sysdeps/ieee754/dbl-64/s_fma.c
  <https://gcc.gnu.org/bugzilla/show_bug.cgi?id=111709>`__
- The PA-RISC project participates in the GSOC 2025: `Implement LASI network or SCSI controller in qemu 
  <https://wiki.qemu.org/Google_Summer_of_Code_2025#Implement_LASI_network_card_and/or_NCR_710_SCSI_controller_device_models>`__

Jan 2025
~~~~~~~~
- A new `Debian installation CD-ROM image is available
  <http://ftp.parisc-linux.org/debian-cd/debian-12/debian-2025-01-hppa-CD.iso>`__.
  Please `read HPPA specific installation notes <http://ftp.parisc-linux.org/debian-cd/debian-12/README-hppa>`__.
- Maintenance update of `palo version 2.26 <https://git.kernel.org/pub/scm/linux/kernel/git/deller/palo.git/>`__
  released.
- Dave fixed a glibc bug: `pthread_attr_getstacksize/pthread_attr_getstack return incorrect
  main stack size <https://sourceware.org/bugzilla/show_bug.cgi?id=32574>`__
- Various updates to the debian-installer

1998-2024
~~~~~~~~~
- See :doc:`PA-RISC_Linux_Project_History <pa-risc_linux_project_history>` for older news.

Resources
---------

.. toctree::
   :maxdepth: 2

   faq
   documentation
   tool_chain

External Resources
------------------

- http://www.debian.org/ports/hppa/ - Debian HPPA port page
- http://www.gentoo.org/doc/en/handbook/handbook-hppa.xml - Gentoo Linux Installation
- http://www.openpa.net/index.html - The OpenPA Project
- http://www.wikiwand.com/en/HP_9000 and http://www.wikiwand.com/en/PA-RISC - Good overview of PA-RISC, HP-UX, CDE, ...
- http://web.archive.org/web/20040202003152/http://www.cpus.hp.com/technical_references/parisc.shtml - Historic PA-RISC Documentation from HP.com (2004)
- https://www.hpl.hp.com/hpjournal/pdfs/IssuePDFs/1987-03.pdf - technical documentattion of first PA-RISC processors
- http://www.3kranger.com/HP3000/mpeix/hard.shtm#PA-RISC - PA-RISC arch & HP3000 docs
- http://www.gentoo.org/doc/en/handbook/handbook-hppa.xml - Gentoo HPPA Handbook
- http://www.hpmuseum.net/collection_document.php - HP Computer Museum
- http://computermuseum.informatik.uni-stuttgart.de/dev/hp9000_840/ - Uni Stuttgart Computermuseum - HP 9000/840 (first PA-RISC machine)
- http://www.robelle.com/library/smugbook/welcome.html - The HP Server Encyclopedia: Robelle's tips for users of HP 3000 (MPE) and HP-UX servers
- http://tenox.pdp-11.ru/hpux/ - HP/UX ressources
- https://fsck.technology/software/HP/ - HP/UX ressources
- http://psg.skinforum.org/hpux.html - Tin Ho's "Sys Admin Pocket Survival Guide - HP-UX"
- http://www.mach-linux.org/ - OSF Mach-Linux
- http://www.unixnerd.demon.co.uk/hp_unix.html - UnixNerds/HPUX
- https://github.com/larsbrinkhoff/awesome-cpus - All CPUs documented
- http://git.kernel.org/cgit/linux/kernel/git/deller/parisc-linux.git - Helge's PARISC Linux Kernel git tree
- https://patchwork.kernel.org/project/linux-parisc/list - PARISC Linux Patchwork
- http://git.kernel.org/cgit/linux/kernel/git/deller/palo.git - PALO boot loader source code
- http://hpe.com/info/hpuxservermatrix - HP-UX Server and OS Support matrix. Includes support end dates and information which servers can run which HP-UX version.

Archived historical webpages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- https://web.archive.org/web/20240917210505/https://parisc.wiki.kernel.org/index.php/Main_Page - Snapshot of former PA-RISC Wiki on kernel.org (2014-2024)
- http://www.parisc-linux.org/index.html - The former and now historical PA-RISC/Linux website (2004 until 2014)
- http://pateam.parisc-linux.org - The PA/Linux ESIEE Team (former www.pateam.org webpage until 2014)
