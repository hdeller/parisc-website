PDC CHASSIS
===========

We are dealing with some kind of PA-RISC specific firmware: the
:doc:`Processor Dependent Code <processor_dependent_code>` (PDC).

Files:

- ``include/asm-parisc/pdc_chassis.h``
- ``arch/parisc/kernel/pdc_chassis.c``
- ``arch/parisc/kernel/firmware.c``

What is PDC CHASSIS
-------------------

The ``CHASSIS`` PDC procedure aims at two things:

#. Informing the hardware of the current state of the operating system.
#. Informing the OS of the current state of the hardware.

The first part consists in sending the PDC some messages reflecting the
current state of the system. Messages can be one of *Off*, *Fault*,
*Test*, *Initialize*, *Shutdown*, *Warning*, *Run* or *All On*. The
second part allows the retrieval of chassis status. It's possible to get
data about failing PSU, Fan or other (encoded in a HVERSION-dependent
way), and to know whether NVRAM battery is getting low, and if
*Temp-Low* and *Temp-Mid* temperature threshold have been exceeded.

On ``PAT PDC`` boxes, featuring a :doc:`Guardian Service Processor
<guardian_service_processor>`, it is possible to send messages to the
:doc:`Virtual Front Panel <_virtual_front_panel>`. This code deals with
that too, see :doc:`PDC Chassis Log <pdc_chassis_log>`.

What are we trying to do?
-------------------------

The code takes care of reflecting the current state of the system (and
its sudden changes) by sending messages to the PDC. The result varies
from some LED state change (eg on E-class), to LCD messages (**INI**
during bootup, **RUN** when booted and running, **SHU** after shutdown,
or **FLT** upon kernel panic, etc) on systems with a LCD display.

It also adds a ``proc`` entry named ``chassis``, giving some info about
the chassis status (essentially battery and temperature threshold, and a
message with an hex string if there is a redundant chassis component
failing). Note that the states recorded by PDC are (seemingly) only
cleared upon reboot. That is to say that if you have eg an overheat,
even if the condition goes back to normal, it won't be noticeable until
you reboot the machine. There's a persistence of warnings.

How do we do it?
----------------

We had to implement the missing abstraction logic in ``firmware.c`` to
access Chassis. We had to add the required interfaces to ``PDC_CHASSIS``
calls:

.. list-table:: **Interfaces (``firmware.c``)**
  :header-rows: 1

  - 

     - Function
     - Role
  - 

     - ``int pdc_chassis_disp(unsigned long disp)``
     - This call sends the state expressed by ``disp`` PDC CHASSIS
  - 

     - ``int pdc_chassis_warn(unsigned long *warn)``
     - This call retrieves the chassis status in ``*warn``

The ``PAT_PDC`` implementation is discussed in :doc:`PDC Chassis Log
<pdc_chassis_log>`.

Linking kernel events to messages
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

According to what we said, we have prepared a set of preformatted
messages that we will use to reflect the current state of the system,
through hooks we have placed in various key points in the kernel::

    arch/parisc/kernel/pdc_chassis.c:95:    pdc_chassis_send_status(PDC_CHASSIS_DIRECT_PANIC);
    arch/parisc/kernel/pdc_chassis.c:115:   pdc_chassis_send_status(PDC_CHASSIS_DIRECT_SHUTDOWN);
    arch/parisc/kernel/process.c:140:       pdc_chassis_send_status(PDC_CHASSIS_DIRECT_SHUTDOWN);
    arch/parisc/kernel/process.c:181:       pdc_chassis_send_status(PDC_CHASSIS_DIRECT_SHUTDOWN);
    arch/parisc/kernel/setup.c:304:         pdc_chassis_send_status(PDC_CHASSIS_DIRECT_BSTART);
    arch/parisc/kernel/traps.c:498:         pdc_chassis_send_status(PDC_CHASSIS_DIRECT_HPMC);
    arch/parisc/kernel/traps.c:519:         pdc_chassis_send_status(PDC_CHASSIS_DIRECT_LPMC);
    arch/parisc/kernel/traps.c:728:         pdc_chassis_send_status(PDC_CHASSIS_DIRECT_PANIC);
    arch/parisc/kernel/traps.c:761:         pdc_chassis_send_status(PDC_CHASSIS_DIRECT_PANIC);
    arch/parisc/mm/init.c:410:              pdc_chassis_send_status(PDC_CHASSIS_DIRECT_BCOMPLETE);
     
We have added panic and reboot notifiers in ``pdc_chassis.c``; and we
have added a few hooks in the places where an important kernel state
change -- that will affect the whole system -- might happen. Through
these, we are able to monitor reliably the state of the system and
reflect it accurately via PDC or Chassis Log.

Kernel parameter
----------------

Last but not least, if enabled at build time in the kernel, this
"driver" -- so to speak -- can be disabled at boot time by passing
``pdcchassis=0`` on the kernel command line. This is especially useful
on :doc:`Guardian Service Processor <guardian_service_processor>`-featuring
machines, when debugging the kernel during the boot sequence, as it
avoids the console to be clobbered with informative messages from the
:doc:`Virtual Front Panel <_virtual_front_panel>`.
