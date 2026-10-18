=================================
Graphics cards with PA-RISC Linux
=================================

Most graphics card have to be initialized by firmware before the OS
driver can use the card: this may consist of wakeup sequences, setting
up a proper color map, clearing the video memory, selecting the proper
video frequency, output interface and much more. Usually, the graphics
card contains the initialization firmware in the form of

- BIOS (on older ix86/PCs),
- EFI (new IA32 and IA64),
- **PDC (on PA-RISC)**, or
- OpenFirmware (on Sun and MACs).

Unfortunately each flavor of firmware is not compatible on other
computers. For example, PA-RISC PDC cannot initialize a PC graphics
card. Therefore the graphics card vendors may produce respective
versions for each architecture it wishes to sell a particular graphics
card. E.g. ATI produced different 'Fire GL' graphics cards for PC, IA64,
PA-RISC, and Sun/MAC. And HP offered "PC" variants of FX/5 and FX/10.

At bootup of PA-RISC machines the firmware will call the PDC ROM of
PA-RISC graphic card to initialize a text mode for the IPL boot menu. On
most graphic cards you may press the **TAB key during bootup** to cycle
through various graphics resolutions (e.g. 800x600, 1024x768 or
1600x1200) to choose the one which suits your monitor. **Please note,
that if no keyboard is connected most PA-RISC machines will fall back to
serial console on serial port 1.**

On PA-RISC Linux two different console drivers are available:

- STI console (sticon) supports text-mode only
- framebuffer-based text console (fbcon)

The **sticon** is a Linux driver which uses the PA-RISC graphics card
PDC ROM functions to bring up a **text-only console on native PA-RISC
graphic cards only.**

The **fbcon Linux driver is a generic driver which renders a text
console on any framebuffer device** for which Linux has a working
framebuffer (fbdev or DRM) driver, e.g. any non-PARISC Matrox or
Permedia based graphics cards.

Usually you want to prefer the fbcon driver, as this will allow you to
use X11 and other graphics programs too. If Linux detects a framebuffer
driver it will prefer the fbcon driver over the sticon driver
automatically.

HP graphics cards
-----------------

All original HP graphics cards which can bring up a boot (IPL) screen
**are supported by Linux in text-mode** with the STI console (sticon).

Framebuffer graphics support for X11 is depend on the graphics card:

AGP cards
~~~~~~~~~

The C8000 workstation is the only machine with AGP slots.

- ATI Fire GL-X1/X3 graphics are supported in un-accelerated mode with
  Linux DRM driver. There were various attempts to fix Fire GL-X acceleration:

  - `in 2016 by Simone Mannori <https://marc.info/?a=145922974200003&r=1&w=2>`__
  - `in 2017 by Tom Bogendoerfer <https://marc.info/?t=150679093500001&r=1&w=2>`__ and
  - `in 2019 by Sven Schnelle and Tom with a quickfix for the "ring test failed" issue <https://marc.info/?t=156971879800064&r=1&w=2>`__

- ATI Fire GL T2-128 has not been tested yet.

PCI cards
~~~~~~~~~

- Visualize-EG cards (8-bit colors) are fully supported. Graphic is
  un-accelerated.

- The Visualize-FX/E, FX/2, FX/4, FX/5, FX/6 and FX/10 PCI cards are not
  yet supported. See :doc:`Visualize-FX <visualize_fx>` for further
  progress.

- The Fire GL-UX graphics card (e.g. Diamond IBM GXT6000P) needs the
  Linux gxt4500 fbdev kernel driver. The card seems to be initialized
  and reported to be OK, but the monitor stays black (tested by Dave
  Land in 2023).

GSC cards
~~~~~~~~~

- Most HP GSC-based graphics cards are supported, with 8bpp and 24bpp
  (if supported by the card).

- The available 2D and 3D HW acceleration features are not supported.
  See :doc:`NGLE <ngle>` for accelerated graphics progress.

- The following GSC cards are not yet supported, as they need patching
  from the Linux operating system, which is not yet implemented:

  - **Hyperdrive/Hyperbowl (A4071A)** graphics card series::

      ID = 0x2BCB015A (Version 8.04/8)
      ID = 0x2BCB015A (Version 8.04/11)

  - **Thunder 1 VISUALIZE 48** card::

      ID = 0x2F23E5FC (Version 8.05/9)

  - **Thunder 2 VISUALIZE 48 XP** card::

      ID = 0x2F8D570E (Version 8.05/12)

  - **Some Hyperion and ThunderHawk GSC cards**

Which non-HP PCI graphics cards work with PA-RISC Linux?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

This depends on which graphics card you are using.

PA-RISC PDC doesn't know how to use a non-HP graphics card, e.g. one can
not use the boot menu with a non-HP graphics card. Unfortunately, no
work-around is available for this problem, since this would require
patching the PDC firmware. Possible workarounds are:

#. use serial console to access the PDC boot menu

#. use two graphic cards, one original HP graphics card to boot the
   machine and a newer/faster one for X11

#. on later PA-RISC machines, like C8000, which has a built-in x86
   emulator, the PDC may be able to POST PC graphics cards

#. You have to initialize a non-HP graphics card somehow before you can
   use it. Unfortunately there is no generic way to do this, the
   initialization method is different for every type of graphics card.

Some graphics cards can be initialized by the **Linux framebuffer or DRM
drivers** directly, and might work on PA-RISC Linux. In combination with
the fbdev X-server from XFree86/X.org you should able to have a nice
(albeit unaccelerated) X11 desktop.

This is an **incomplete list of cards** which have been:

- reported to work with PA-RISC Linux:

  - ATI RV280 GL [FireMV 2200 PCI] (PCI card in C8000, radeondrmfb).
    Note: AGP cards have issues, see below.

  - Permedia 2 cards, e.g. ELSA Gloria Synergy, Tech-Source Raptor GFX

  - 3dfx Voodoo and Voodoo2

  - NVIDIA Corporation NV18GL [Quadro NVS 280 SD] (AGP card in C8000,
    nvidiafb and nouveau-drm driver)

  - Matrox Millenium I/II

  - Matrox MGA G200 (works when the matrox framebuffer driver is
    compiled into the kernel and
    ``video=matroxfb:init,vesa:0x1BB,mem:4m`` is given on the kernel
    command boot line)

- cards with issues:

  - Matrox Millennium G450 Dual Head PCI (matroxfb: cannot determine
    memory size), must physically disconnect the computer from the power
    line before (re)booting, as it is not sufficient to just press the
    power-off button.

- untested cards:

  - ATI Mach64 (eg. Rage XL)
  - S3
  - ancient framebuffer cards (eg. TGA, P9000)


Useful Linux command line tools when running framebuffer drivers (fbdev and DRM)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Switch text mode screen to another graphics card
  ::

     con2fbmap 1 2

  switches tty1 to /dev/fb2  (first login screen to third graphic card, fb0 is the first graphics card)

- Use the fbset tool to switch to another graphic mode (see
  /etc/fb.modes for possible values), e.g.
  ::

      fbset -fb /dev/fb1 1280x1024-60 -depth 8
      fbset -fb /dev/fb1 -i                    # show current graphics resolution for framebuffer fb1

- `fbtest tool from Geert Uytterhoeven <https://git.kernel.org/pub/scm/linux/kernel/git/geert/fbtest.git/>`__::

    fbtest -f /dev/fb0

- Switch between consoles, see https://docs.kernel.org/fb/fbcon.html and https://github.com/torvalds/linux/blob/master/Documentation/driver-api/console.rst:

  - attach framebuffer console to console layer::

      echo 1 > /sys/class/vtconsole/vtcon1/bind

  - detach framebuffer console from console layer::

      echo 0 > /sys/class/vtconsole/vtcon1/bind

  - blank/unblank console (sticon, vgacon,..)::

      setterm --blank force          # blank
      setterm --blank poke           # unblank
      setterm --powersave powerdown  # set VESA DPMS level 3

Configuring Xorg for HP graphics cards
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Make sure you have the Xorg server package **server-xorg-video-fbdev**
and **xserver-xorg-input-all** installed.

Here is an example xorg.conf file which is needed when you run on a
graphics card which supports only 8 bits per pixel. Put that contents
into the (new) file **/etc/X11/xorg.conf.d/xorg.conf**::

    Section "Screen"
           Identifier "Screen0"
           Device      "fbX"    # comment out if you want to use fb0
           DefaultDepth 8       # comment out if you want 24/32bpp
           # Modes	"1280x1024" # "1024x768" or "1280x1024", entry not needed but if enabled only one entry is allowed
    EndSection

    Section "Device"
       Identifier  "fbX"
       Driver      "fbdev"
       Screen      0
       Option      "fbdev" "/dev/fb1" # change to any fb[0-20] you want
    EndSection


See also this posting regarding X endianess
https://lwn.net/Articles/921196/ if you have problems connecting
remotely with X protocol.

Troubleshooting
~~~~~~~~~~~~~~~

Check dmesg output right after booting for this part::

   sticore: STI GSC/PCI core graphics driver Version 0.9c
   sti 0000:01:04.0: enabling SERR and PARITY (0002 -> 0142)
   sticore: STI PCI graphic ROM found at f4800000 (64 kB), fb at f8000000 (32 MB)
   sticore: STI word mode ROM supports 32 bit firmware functions.
   sticore:   id 2d08c0a7-9a02587, conforms to spec rev. 8.0a
   sticore:     built-in font #1: size 8x16, chars 0-255, bpc 16
   sticore:     built-in font #2: size 6x13, chars 0-255, bpc 13
   sticore:     built-in font #3: size 10x20, chars 0-255, bpc 40
   sticore:     using 8x16 framebuffer font VGA8x16
   sticore:     graphics card name: PCI_GRAFFITIX1280
   sticore:     located at [10/1/4/0]
   sticore: STI PCI graphic ROM found at f7000000 (2048 kB), fb at fa000000 (32 MB)
   sticore: STI word mode ROM supports 32 and 64 bit firmware functions.
   sticore:   id 35acda30-9a02587, conforms to spec rev. 8.0d
   sticore:     built-in font #1: size 10x20, chars 0-255, bpc 40
   sticore:     built-in font #2: size 8x16, chars 0-255, bpc 16
   sticore:     using 8x16 framebuffer font VGA8x16
   sticore:     graphics card name: A1299B
   sticore:     located at [10/6/2/0]
   fbcon: stifb (fb0) is primary device
   Console: switching to colour frame buffer device 160x64
   fb0: stifb 1280x1024-8 frame buffer device, PCI_GRAFFITIX1280, id: 2d08c0a7, mmio: 0xf8100000

You should see one penguin (or more for SMP) in the top left corner at
boot time as well if the kernel is correctly configured.

Use ``fbset -i`` to determine the supported color depth if it's not clear
from the STIFB driver output. Help on configuring mouse and keyboard is
available in the :doc:`FAQ <faq>`.
