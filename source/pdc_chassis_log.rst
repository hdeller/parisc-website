PDC Chassis Log
===============

What is PDC Chassis Log
-----------------------

The Chassis Log is a feature of some specific type of PDC: *PAT PDC*
equipped with a :doc:`Guardian Service Processor
<guardian_service_processor>` (GSP), which can be found on recent
systems (in particular high-end PA8X00 servers). This feature has two
hardware "expression": the :doc:`Virtual Front Panel
<_virtual_front_panel>` (VFP) and the GSP Logs. The former is the LED
panel found on the front bezel of some machines, with **Run**, **Attn**,
**Fault**, **Power** and **Remote** LEDs; while the latter is about
logging special events to the GuardianServiceProcessor logs.

What are we trying to do?
-------------------------

The code takes care of reflecting the current state of the system (and
its sudden changes) on the VFP and GSP Logs. That is to say for instance
that upon system boot, a message is sent to the PDC to notify it of the
boot progress; or if the system is taken down by a kernel panic, to
reflect that failure on the front panel and log it on the GSP.

How do we do it?
----------------

Accessing the PDC
~~~~~~~~~~~~~~~~~

First, we had to implement the missing abstraction logic in
``firmware.c`` to access the Chassis Log. We had to add the required
interfaces to PDC_CHASSIS_LOG calls:

.. list-table:: Interfaces (``firmware.c``)
  :header-rows: 1

  - 

     - Function
     - Role
  - 

     - ``int pdc_pat_chassis_send_log(unsigned long state, unsigned long data)``
     - This call sends the message expressed by \`state\` and \`data\` to the PAT PDC logs

We are now ready to *send* data to the Chassis Log.

Anatomy of the Log messages
~~~~~~~~~~~~~~~~~~~~~~~~~~~

The log messages are sent using two ``unsigned long`` numbers: ``state``
and ``data``. ``state`` will actually contain an encoded version of the
GSP log message, while ``data`` will reflect an encoded Front Panel LED
status.

These two words are encoded using the logic found in ``pdc_chassis.h``,
bearing in mind that they have to be consistent with what the PDC
*knows* as a valid log message/led status. That is to say that you
cannot *invent* a message, you have to respect existing ones that will
be understood by the PDC and the GSP. Hence the **preformatted
messages** section in ``pdc_chassis.h``.
