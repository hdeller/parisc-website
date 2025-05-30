================
Hardware support
================

Memory requirements
-------------------

Linux requires at least 32 MB RAM to be able to boot up and decompress
it's kernel. For a functional system with some applications you should
better have 64 MB or more.

Workstations
------------

PA-RISC Linux supports all HP PA-RISC workstations. Not everything is
perfectly functional - but nearly all of it works. The unlettered
700-series workstations (eg 712/715/735) and the B/C/J class machines
(e.g. B132L, Tadpole B160L laptop, C180L, C200, B1000, B2000, C3000,
J5000, C8000) systems are supported. Fast-Wide Differential SCSI does
not work on 735 or 755 (but does on other machiines).

Most standard HP graphic cards work (Visualize-EG, Artist, HCRX, ...) in
8 and 24bpp modes. Support for the Vis-FX series is still missing and
will probably not be supported.

PA-RISC VME boards are based on workstation designs. PA-RISC Linux does
boot on a 743.

Search the :doc:`mailing lists <mailing_lists>` to see if your
workstation (or server) has any known problems. Please submit bugs (or
patches!) for missing/broken functionality to the parisc-linux :doc:`mailing
list <mailing_lists>`.

Servers
-------

The earliest PA-RISC servers have proprietary HP devices attached to
proprietary HP bus architectures. Documentation on these busses and
devices will not become available soon since no one in HP is finding,
"scrubbing", and releasing the docs. Machines in this category are the
F, G, H, I class (aka Nova) and T500 series (Emerald) machines as well
as some earlier, unlettered servers.

The T600 is unlikely to ever be supported. Though T600 uses some of the
same GSC devices (eg card-mode Dino 100BT, Zalon FWD SCSI) as K-class,
T600 has the Java I/O MMU, instead of U2, which is NOT I/O coherent.
Linux PCI drivers require I/O coherency.

Support for the E class is being pursued. Similar to K class in some
ways, it requires the Mux driver for console and does not yet have SCSI
or Centronics support. Network support is the same as 712. GSC add-in
cards should work, HP-PB cards will not yet.

The D, K and R class machines are at least partially supported. These
machines are architecturally similar to the older B, C and J class
workstations and share most of the I/O devices, busses (GSC) and
processors. The console on K class and the optional console card on D/R
class are now supported. Most of the other HP-PB (aka NIO) devices
aren't supported due to lack of documentation -- the same documentation
which makes the Nova/Emerald machines unsupportable.

The A180, L1000/2000/3000, N4000, A400/A500 (rp24xx) and rp3410/rp3440
servers are supported.

Support for Superdome might someday follow but requires some additional
VM work. Superdomes have a cellboard based architecture. There was never
enough documentation released that the according memory management could
be programmed in Linux. So probably Superdomes may not work. Maybe on an
physical partition level, there might be a chance, since the single
cellboard is pretty similar to the L-Class. One idea is to try to get
the partition management running and install one partition HP-UX and
then try to work with linux on an additional partition.

People ask about V-class. V-class has a very different architecture from
other HP machines. See the `Server Architecture Reference
<http://docs.hp.com/hpux/hw/>`__ for details. One major known problem
with supporting V-class is the complexity of its PCI bus adapter (EPIC
and SAGA). Support for the V-class isn't going to happen until some
really masochistic person who has access to a V-class is, uh, challenged
by this.

Emulators
---------

QEMU support is progressing quite fast. Check the :doc:`Qemu <qemu>` page
for more information.

SATA Controllers
----------------

.. note::

   PARISC machines boot from SCSI or built-in IDE drives only.

The following SATA PCI controllers have been reported to work on Linux,
after the kernel was booted:

- Silicon Image SiI 3112 PCI Serial ATA Controller (rev 02) (reported by
  Helge in C8000), does not support SATA-TRIM, may need
  "sata_sil.slow_down=1" kernel module option

- Silicon Image SiI 3124 PCI-X Serial ATA Controller (rev 02) (reported
  by Dave in rp3440, reported by Sven in C8000) - RAID bus controller:
  Silicon Image, Inc. SiI 3512 [SATALink/SATARaid] Serial ATA Controller
  (rev 01), in C8000 (phantom) - VIA Technologies VT6421 IDE/SATA
  Controller (rev 50) (reported by Rolf Eike Beer in C3600 and C8000) -
  Adaptec 1210SA, Silicon Image Sil3112 (reported by Carlo Pisani
  <carlojpisani@gmail.com> in C3600) - SYBA SY-PCX40009 PCI-X SATA II
  Controller (SiL3124) (reported by Carlo Pisani
  <carlojpisani@gmail.com> in C3600) - HighPoint RocketRAID 2224
  (reported by Carlo Pisani <carlojpisani@gmail.com> in C3600) -
  Highpoint RocketRAID 1640 (reported by Carlo Pisani
  <carlojpisani@gmail.com> in C3600)

PCI/PCI-E Bridges with SSD/NVME drives
--------------------------------------

.. note::

   PARISC machines boot from SCSI or built-in IDE drives only.

Dave reported this combination as working:

- StarTech.com PCI to PCI Express Adapter Card - PCIe x1 (5V) to PCI (5V
  & 3.3V) slot adapter - Low Profile - PCI1PEX1

- GLOTRENDS PA09-X1 M.2 NVMe to PCIe X1 Adapter for 2230/2242/2260/2280
  M.2 NVMe SSD, PCIe X1 Installation

- Crucial P3 Plus 1TB PCIe Gen4 3D NAND NVMe M.2 SSD, up to 5000MB/s -
  CT1000P3PSSD8

Booting from internal ATA controller (SiI 0649 Ultra ATA/100 PCI to ATA Host Controller)
----------------------------------------------------------------------------------------

You may attach an ATA-to-SD-card or ATA-to-CF-card adapter, e.g.:

- SD To 3.5" 40Pin Male IDE Hard Disk Drive HDD Converter Card Adapter

Brackets for adding SATA drives (e.g. in rp3440 machines)
---------------------------------------------------------

https://www.thingiverse.com/thing:946543

External Resources
------------------

- http://www.openpa.net/systems/index.html - OpenPA.net Overview of
  PA-RISC Computers
