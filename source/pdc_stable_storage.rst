PDC Stable Storage
==================

We are dealing here with a part of the :doc:`Booting System
<_booting_system>`, namely the :doc:`Processor Dependent Code
<processor_dependent_code>` (PDC).

Files:

- ``drivers/parisc/pdc_stable.c``
- ``arch/parisc/kernel/firmware.c``
- ``arch/parisc/kernel/drivers.c``

Brief version history
---------------------

This driver was first released in kernel 2.6.11-rc1-pa2. A major update
(v0.22) was released in kernel 2.6.15-pa3. Code to access OS Dependent
data was added in v0.30 released in kernel 2.6.17-rc3-pa3.

What is Stable Storage?
-----------------------

According to
http://ftp.parisc-linux.org/docs/arch/pdc20-v1.0-Ch4-procs.pdf page
4-84, all PA-RISC architectures must implement a permanent storage area
of at least 96 bytes, called Stable Storage. It is a system wide storage
used to store some system variables such as Autoboot and Autosearch
flags, Primary/Alternative Boot Paths, Console and Keyboard Paths, the
amount of RAM to be tested during boot sequence, and other data.

What are we trying to do?
-------------------------

The main idea underlying this piece of code is to provide an interface
between the kernel and that firmware storage area, in order to:

- Get an interface to know what the current settings are.
- Get an interface to be able to change the settings at will while Linux
  is running.

The "settings" being Autoboot, Autosearch, Paths, etc.

How do we do it?
----------------

Accessing the PDC
~~~~~~~~~~~~~~~~~

First, we had to implement the missing abstraction logic in
``firmware.c`` to access the Stable Storage. We had to code all the
required interfaces to PDC_STABLE calls:

.. list-table:: Interfaces (``firmware.c``)
  :header-rows: 1

  - 

     - Function
     - Role
  - 

     - ``int pdc_stable_read(unsigned long staddr, void *memaddr, unsigned long count)``
     - This call *reads* ``count`` bytes from address ``staddr`` and writes the result in preallocated ``memaddr`` space
  - 

     - ``int pdc_stable_write(unsigned long staddr, void *memaddr, unsigned long count)``
     - This call *writes* ``count`` bytes at address ``staddr`` from address ``memaddr``
  - 

     - ``int pdc_stable_get_size(unsigned long *size)``
     - This call gets the size of the Stable Storage area
  - 

     - ``int pdc_stable_verify_contents(void)``
     - This call checks the integrity of the contents of Stable Storage
  - 

     - ``int pdc_stable_initialize(void)``
     - This call initialize Stable Storage to zero

Now a few details: as it is noted in the above mentioned document,
``staddr`` and ``memaddr`` must be word-aligned. ``count`` must be
multiple of 4. The Stable Storage address space starts at ``0x0``, and
both read and write calls will fail if ``staddr + count > size``.

Everything is now setup to get and write useful data from Stable
Storage. Thanks to the documentation, we know where the various Paths
and other variables are stored. There is a trick, though. Paths are **BC
Paths**, also known as *hardware paths* in the PA-RISC terminology. That
means that when reading a path, we have to convert it to the actual
device and map it to a human readable device name, and when writing it,
we will have to provide a *human writeable* way of designing a device,
and then convert it back to its hardware path.

Converting Paths
~~~~~~~~~~~~~~~~

The first thing that comes to mind is that we will need to convert
hardware path to devices and devices back to their hardware path. Let
see how we do that.

Anatomy Of Paths
^^^^^^^^^^^^^^^^

The paths described by Stable Storage are made of two separate entities.
First, they begin with a *flag* byte, which is only used in **Primary
Boot Path** to store the values of **Autoboot**, **Autosearch** and the
**Timer**. Then comes a regular BC path, made of 6 *BC fields* (bytes
which values specify the fixed field of the bus converter port's HPA),
and a *MOD field* (byte which is the fixed field of the specified
module). Following these 8 bytes is an array of 6 longs (32 bits),
called *layers*, used to describe the portion of the path to a device
that is beyond the module and/or to contain device-dependent
information. Typically, for a given SCSI device, ``layer[0]`` will be
the SCSI ID, and ``layer[1]`` the SCSI LUN; or for a RS232 device, the
layers will contains the serial port being used.

The Algorithms
^^^^^^^^^^^^^^

These are implemented in ``arch/parisc/kernel/drivers.c``.

Matching an existing hwpath back to its original device
'''''''''''''''''''''''''''''''''''''''''''''''''''''''

That's what we want when *reading* the Stable Storage configuration.

When trying to convert a hardware path to the matching device, a first
problem occured: we cannot use ``find_parisc_device()``, for the actual
device may not necessarily be a ``parisc_device`` (in particular, it's
likely that it will be a ``pci_dev`` for a SCSI device).

What we did was first to write clones of ``create_parisc_device()`` and
``alloc_tree_node()``, changing their logic a little.
``hwpath_to_device()`` deals with generic devices instead of parisc
ones. ``parse_tree_node()`` no longer allocates anything, but instead
check each device of a given level to match it against the current
hardware path (depending on its type: ``parisc_device``, ``pci_dev``,
etc), when a matching device is found, it's returned back to the caller
(``hwpath_to_device()`` in the present case) which will use it back into
a recursive call. Thanks to Matthew Wilcox who rewrote the parisc device
layer to use generic devices, this code could be made much easier.

If it is a ``parisc_device`` (``bus_type == &parisc_bus_type``), then it
tries to match the current device ``hw_path`` with the expected one.

If it is a ``pci_dev`` (``bus_type == &pci_bus_type``), then it parses
the remaining tree to look either for a PCI bridge, or for a PCI device.
In the case of a PCI device, we have to go down one level more in the
hardware path, since PCI devices occupy the last BC field **and** the
MOD field.

If it can't find a matching device, then ``hwpath_to_device()`` simply
returns a ``NULL`` pointer.

Matching an existing device to its corresponding hardware path
''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

We need that when *writing* the new configuration to Stable Storage.

Fortunately this is alot easier to do, the logic is already existing
(``get_node_path()``). We just wrote a wrapper for that function, which
we called ``device_to_hwpath()`` for obvious consistency reasons. That
wrapper takes a ``struct device`` pointer and a preallocated ``struct
hardware_path`` pointer as arguments, and will fill the later with the
path corresponding to the former, doing appropriate stuff depending on
the type of the device (be it a PCI or PA-RISC device, for instance).

Kernel Interface
~~~~~~~~~~~~~~~~

Now that we have the logic in place, we have to think of a proper way
for the kernel to expose the contents of Stable Storage. A first
approach was to use the *proc interface*, but it quickly showed
intrinsic limitations, and eventually **SysFS** seemed to be a far
better choice.

Here is the tree we are presenting in SysFS::

    /sys/firmware/stable
    /sys/firmware/stable/paths
    /sys/firmware/stable/paths/keyboard
    /sys/firmware/stable/paths/keyboard/device
    /sys/firmware/stable/paths/keyboard/layer
    /sys/firmware/stable/paths/keyboard/hwpath
    /sys/firmware/stable/paths/console
    /sys/firmware/stable/paths/console/layer
    /sys/firmware/stable/paths/console/hwpath
    /sys/firmware/stable/paths/alternative
    /sys/firmware/stable/paths/alternative/device
    /sys/firmware/stable/paths/alternative/layer
    /sys/firmware/stable/paths/alternative/hwpath
    /sys/firmware/stable/paths/primary
    /sys/firmware/stable/paths/primary/device
    /sys/firmware/stable/paths/primary/layer
    /sys/firmware/stable/paths/primary/hwpath
    /sys/firmware/stable/osdep2
    /sys/firmware/stable/fastsize
    /sys/firmware/stable/diagnostic
    /sys/firmware/stable/osdep1
    /sys/firmware/stable/osid
    /sys/firmware/stable/timer
    /sys/firmware/stable/autosearch
    /sys/firmware/stable/autoboot
    /sys/firmware/stable/size
     
Roughly put, all global data (such as the boot flags, the amount of RAM
checked during boot, etc) is shown in the various files placed at the
root of the ``stable`` directory, while we are creating in the
``paths/`` subfolder 4 more - whenever possible - sub directories (one
per boot path: Primary, Alternative, Console and Keyboard), each
containing 2 files and (usually) one symlink:

- ``device`` is the symlink to the actual device folder in SysFS.

- ``layer`` contains the hardware layer, in the form ``x y z``, where
  ``x,y,z`` are unsigned decimals.

- ``hwpath`` contains the hardware path, in the form ``x/y/z``, where
  ``x,y,z`` are unsigned decimals.

In order for the kernel to change the boot paths values, ``layer`` and
``hwpath`` can be modified by writing to them (as super user) data in
the following form:

- ``x/y/z`` for ``hwpath``, where ``x,y,z`` are unsigned decimals, to
  change the hardware path.

- ``x.y.z`` for ``layer``, where ``x,y,z`` are unsigned decimals, to
  change ``layer[0]``->\ ``layer[n]``, ``n`` < 6.

For instance, one can do the following::

    # echo -n "0/0/2/1" > /sys/firmware/stable/paths/alternative/hwpath
    PDC Stable Storage: changed "alternative" path to "0/0/2/1"
    # echo -n "15" > /sys/firmware/stable/paths/alternative/layer
    PDC Stable Storage: changed "alternative" layers to "15"

The ``PDC Stable Storage:`` messages are printed to **INFO** loglevel
upon successful completion. If the user attempts to set an unmappable
``hwpath``, an error message is sent to the **WARNING** loglevel.

In fact, minimal integrity checks are performed (see bellow for more
information):

- For ``hwpath``, the code makes sure it can map the provided path to an
  existing device, and fails otherwise.

- For ``layer``, the code simply checks arguments are integers.

Finally, one can toggle **Autoboot** and **Autosearch** flags by writing
to the ``autoboot`` and ``autosearch`` files, as follows::

    # echo 1 > /sys/firmware/stable/autoboot
    PDC Stable Storage: changed "autoboot" to "On"
    # echo 0 > /sys/firmware/stable/autosearch
    PDC Stable Storage: changed "autosearch" to "Off"
     
It pretty much speaks for itself. There again, messages are sent to
**INFO** loglevel upon successful completion, and to **WARNING** on
failure. The result can be checked by looking at the content of these
files afterwards::

    # cat /sys/firmware/stable/autoboot
    On
    # cat /sys/firmware/stable/autosearch
    Off
     
The content of the ``diagnostic`` file are pretty cryptic. It's not
documented, so the file reflects the raw hex value.

OS-Dependent Data
^^^^^^^^^^^^^^^^^

Two files are of particular interest: ``osdep1`` and ``osdep2``. These
two files are read/write accessible, as they contain OS-Dependent data,
that is, data that can be modified by the host's operating system. The
first file should always be available, according to the PDC specs. The
second one will always be there, but may not necessary contain anything,
as the size of the second OS-Dependent area is ``HVERSION``-dependent.
Most of the time it's 32 bytes. If there's no data available, reading
will return **-ENODATA**, and writing to the file will return
**-ENOSYS**.

The read/write access is pretty basic:

- Reading the file will display raw hex values, 32 bits per line.
- Writing is made on a byte-by-byte fashion, 0-padded.

Here's an example to illustrate::

    # cat /sys/firmware/stable/osdep1
    0x00000000
    0x00000000
    0x00000000
    0x00000000
    # echo -n "123" > /sys/firmware/stable/osdep1
    # cat /sys/firmware/stable/osdep1
    0x31323300
    0x00000000
    0x00000000
    0x00000000
     
``0x31`` is ascii "1", ``0x32`` is ascii "2", and ``0x33`` is ascii "3".

Another example::

    # echo -e "\0001\0002\0003" > /sys/firmware/stable/osdep1
    # cat /sys/firmware/stable/osdep1
    0x0102030a
    0x00000000
    0x00000000
    0x00000000
     

``0x0a`` is ascii "``\n``".

If you try to write more than the available storage size, you will get
**-EMSGSIZE**.

.. note::

  reading/writing from/to ``osdep2`` is much more expensive than from/to
  ``osdep1`` (see the source for details).

Of course, the key interest of these files is the fact that their
content is preserved accross reboots (it's *Stable* Storage, remember?
;) )

File Permissions
^^^^^^^^^^^^^^^^

The general policy toward file access shall respect the following rules:

#. Write permission granted to ``root`` only
#. Read permission to files that trigger PDC calls granted to ``root`` only
#. Read permission to other files granted to everyone

In particular, the following files are ``root`` accessible only::

    /sys/firmware/stable/diagnostic
    /sys/firmware/stable/fastsize
    /sys/firmware/stable/osdep1
    /sys/firmware/stable/osdep2

BUGS & TODO
-----------

Design Choices & Known Features
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Currently the code is unable to deal properly with some kinds of
devices, especially **SuckyIO** ones and **EISA** ones. In particular,
it is impossible for the underlying mechanism to map a path pointing to
a SuckyIO or EISA device to the corresponding generic \`device\`
pointer. This has two main consequences:

- When such a boot path is encountered, no symlink is created in the
  corresponding SysFS folder.

- It is impossible to set any hwpath to such a device, the code will
  complaint that it can't map the path provided to an existing device.

On the other hand, that kind of check is barely enough: a PCI bridge, or
even worse, a CPU, would yield a valid device and thus nothing prevents
anyone to setup their CPU as primary boot path. Keep in mind that this
driver aims at being a *facility*, providing a syntax to be used.
Thorough checking is left to userspace. What's more, only the super user
can change the settings...

Another thing to bear in mind is the high dependency of the code on the
PDC calls reliability. Truth told, should one of this call fail
(especially the write call), the system would most likely end-up in a
rather bad shape upon next reboot. The same applies in case of a sudden
power failure while the content of the Stable Storage is being modified,
but heh, *it's not supposed to happen anyway, right?* ;)

In fact, we do not care that much about that issue since should a **Bad
Thing™** occur, first, you'll be warned (error message sent to **ERR**
loglevel), and it'll be easily fixable through the PDC boot console when
the system powers up. In other word, there is no way that code would
leave the system in such a bad shape it'd be impossible to fix and would
need some HP techie to repair it! Relieved, heh? :)

TODO List
~~~~~~~~~

Something that would be nice to have is a userland tool that would help
setting the path in an easy way, providing some user-friendly glue
between the mechanism in /sys and the way a normal user would designate
a device (eg: */dev/sda4*, */dev/ttyS0*, etc).

The hooks for such a tool to work are mostly there (in particular since
the code can be passed a ``struct device`` pointer, it'll be able to
deal with it fine), so my guess is that it's a matter of finding the
proper glue (that is probably not trivial) and eventually add a
``sysctl``, if one wants not to use the SysFS interface.
