=====================================
Baseboard Management Controller (BMC)
=====================================

Latest generation of servers (e.g. rp3410/rp3440) and workstations (e.g.
C8000) have a BMC (baseboard management controller) that is active when
the system is powered.

.. note::

   Please note that most older parisc machines don't have a BMC, but
   instead a GSP, see :doc:`Guardian Service Processor
   <guardian_service_processor>`.

How to access the C8000 workstation
-----------------------------------

When attached to the serial console, you can press **ESC (** and you
will be able to interact with the BMC::

    ESC (
    cli>help
    CLI Commands:
      C [<passwordstring>]  - Change Password
      CON                   - Display Console selection
      FPL                   - Read Forward Progress Log
      H                     - Help (this text)
      I <ipmi command data> - Send any IPMI message
         req fmt: rsSA netfnlun chk1 rqSWID rqSeq cmd [data] chk2
         resp fmt: rqSWID netfnlun chk3 rsSa rqSeqrsLun cmd ccode [data] chk4
      INFO                  - Display BMC FW Revision
      IPMI <ipmi command data> - Send any IPMI message
         req fmt: netfnlun cmd [data]
         resp fmt: ccode [data]
      P [0,1]               - Power Control
      Q                     - Quit/Logout
      RS  [s]               - Reset System [and switch to sys console]
      SD                    - Read SDR Repository
      SE                    - Read System Event Log
      TOC [s]               - Send an INIT/TOC [and switch to sys console]
    cli>

This most useful for being able to remotely reboot (RS command) and
power on/off (P command) the system. Pressing **ESC )** "reconnects" one
to the system's serial console.

The C8000 supports IPMI, and presumably a lot of these same things can
be done over the built-in e1000 LAN interface once configured.

*Thanks to Matt Turner <mattst88@gmail.com> for above info.*

How to access rp3410/rp3440 iLO
-------------------------------

Use telnet to connect to the BMC, which is called iLO (integrated Lights Out).
You may use this iLO MP (HP Integrity) Advanced License Key:
32Q8Y-XZVGQ-4SGJB-4KY3R-M9ZBN
which allows you to configure SSH and Web-SSL access, sadly only with today
outdated cyphers, which are due to security issues not any longer supported by
todays browsers or ssh clients.


DB25 to 3x DB9 serial console/ups/remote cable
----------------------------------------------

Some HP servers use a 25 pin female connector for BMC. This port
shares three ports: The serial console, a remote port and a UPS
port. Often these cables are missing, so here's the pinout if you
want to make one.

.. list-table::
  :header-rows: 1

  -

     - DB25 Pin
     - DB9 Cable
     - DB9 Pin
     - Name
  -

     - 1
     -
     -
     -

  -
     - 2
     - Console
     - 3
     - TXD
  -
     - 3
     - Console
     - 2
     - RXD
  -
     - 4
     - Console
     - 7
     - RTS
  -
     - 5
     - Console
     - 8
     - CTS
  -
     - 6
     - UPS
     - 6
     - DSR
  -
     - 7
     - Console
     - 5
     - GND
  -
     - 8
     - UPS
     - 1
     - DCD
  -
     - 9
     - Remote
     - 3
     - TXD
  -
     - 10
     - Remote
     - 7
     - RTS
  -
     - 11
     - Remote
     - 4
     - DTR
  -
     - 12
     - Remote
     - 2
     - RXD
  -
     - 13
     - Remote
     - 8
     - CTS
  -
     - 14
     - UPS
     - 3
     - TXD
  -
     - 15
     - UPS
     - 7
     - RTS
  -
     - 16
     - UPS
     - 5
     - GND
  -
     - 17
     - UPS
     - 2
     -  RXD
  -
     - 18
     - UPS
     - 8
     - CTS
  -
     - 19
     -
     -
     -
  -
     - 20
     - UPS
     - 4
     - DTR
  -
     - 21
     - Remote
     - 5
     - GND
  -
     - 22
     - UPS
     - 9
     - RI
  -
     - 23
     - Remote
     - 1
     - DCD
  -
     - 24
     - Remote
     - 6
     - DSR
  -
     - 25
     - Remote
     - 9
     - RI



Reading IPMI data in Linux
--------------------------

When running Linux, you may use the ipmitool command to read IMPI data
out of the BMC::

    root@c8000:~# ipmitool sdr
    Chassis Intrus   | 0x00              | ok
    Chassis Open     | 0x00              | ok
    Power Button     | 0x00              | ok
    Power Button     | 0x00              | ok
    Clear CMOS Jmpr  | 0x00              | ok
    Safe Mode Jmpr   | 0x00              | ok
    Clr Passwd Jmpr  | 0x00              | ok
    Rsvd Jmpr        | Not Readable      | ns
    Rsvd2 Jmpr       | Not Readable      | ns
    Watchdog Timer   | Not Readable      | ns
    Cooling 2 (CPU)  | 0x01              | ok
    Cooling 3 (Mem)  | 0x01              | ok
    Cooling 4 (Disk) | 0x01              | ok
    Wake-on-LAN      | Not Readable      | ns
    Missing Device   | Not Readable      | ns
    Power Supply     | 0x01              | ok
    Power Control    | 0x01              | ok
    Pwr Pod 0 Fault  | 0x00              | ok
    Pwr Pod 1 Fault  | 0x00              | ok
    Battery Voltage  | 0x00              | ok
    Over-Voltage     | 0x00              | ok
    Power Good       | 0x00              | ok
    Ambient Temp     | 17 degrees C      | ok
    Processor 0 Temp | 76 degrees C      | ok
    Processor 1 Temp | 78 degrees C      | ok
    Chassis Fan Tach | disabled          | ns
    CPU0 Fan Tach    | disabled          | ns
    CPU1 Fan Tach    | disabled          | ns
    Memory Fan Tach  | disabled          | ns
    Disk Fan Tach    | disabled          | ns
    Chassis Fan      | 0x00              | ok
    CPU0 Fan         | 0x00              | ok
    CPU1 Fan         | 0x00              | ok
    Memory Fan       | 0x00              | ok
    Disk Fan         | 0x00              | ok
    ACPI State       | 0x01              | ok
    TOC Interrupt    | 0x00              | ok
    SDR Time Set     | 0x00              | ok
    SEL Time Set     | 0x00              | ok
    Intrnl Watchdog  | Not Readable      | ns

In :download:`Lpv00338_rx2600_zx6000.pdf
<media/Lpv00338_rx2600_zx6000.pdf>` you can find a chapter with a
broader description of the BMC.
