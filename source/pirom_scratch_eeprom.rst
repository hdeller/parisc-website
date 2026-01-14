PIROM/SCRATCH EEPROM on PA-8900
===============================
PA-8800 and PA-8900 contain two EEPROM (24C01), where each contains 128
bytes of data. They are used to identify the CPU model, serial and frequency
information by PDC.

The PIROM follows the Itanium PIROM specification. See "Intel Itanium 2 Processor Datasheet",
publication number 250945-005, page 82ff.

The SCRATCH EEPROM is PA-RISC specific. It contains an ASCII name of the
processor, "MAKO-XXXX", where X is the maximum CPU clock. Different formats
exist - the PA-8900/800Mhz on my rp3440 have "MAKO-0800", while the PA8900
1.1Ghz on my C8000 contain "MAKO-41-1100".

Besides that, FSB and CPU speed are also encoded in some ASCII string. An example
is "2C3200C8". The third to fifth digit is the hex representation of the CPU speed -
0x320 = 800 Mhz. The last two digits are likely the FSB - 0xc8 = 200Mhz.

The last byte is the checksum of the scratchrom by adding all the bytes. It can be calculated by:

.. code-block:: cpp

   int csum = 0;
   for (unsigned int i = 0; i < sizeof(eeprom) - 1; i++)
           csum += eeprom[i];
   printf("csum %x\n", (uint8_t)(-csum)+1);

A few example of PI-ROMs / SCRATCH EEPROMs:

Examples
--------

800Mhz PA-8900 SCRATCH EEPROM:
::

   00: 01 01 00 05  00 00 00 f9   01 00 00 00  00 00 00 00  ................
   10: 00 00 00 00  00 00 00 00   00 00 00 00  00 00 00 00  ................
   20: 00 00 00 00  00 00 00 00   01 0b 00 00  00 00 00 d0  ................
   30: 4d 41 4b 4f  2d 30 38 30   30 20 20 20  20 20 20 20  MAKO-0800       
   40: cc 41 35 36  31 30 34 31   31 32 30 36  36 cb 41 42  .A56104112066.AB
   50: 35 33 34 2d  36 32 30 30   31 81 22 00  c4 34 34 34  534-62001."..444
   60: 33 c2 41 34  d0 32 43 33   32 30 30 43  38 20 40 35  3.A4.2C3200C8 @5
   70: 41 20 20 20  20 02 00 00   c1 00 00 00  00 00 00 cf  A    ...........

800Mhz PA-8900 PIROM:
::

   00: a2 80 00 0e  17 28 37 3e   63 67 00 00  00 52 20 39  .....(7>cg...R 9
   10: 2e 31 33 43  03 00 cf 7f   f0 58 32 00  00 00 03 84  .13C.....X2.....
   20: 00 c8 05 14  41 41 00 1d   00 00 00 01  00 00 04 00  ....AA..........
   30: 09 f6 7f 7f  01 00 fd 41   33 20 20 00  00 4c 50 41  .......A3  ..LPA
   40: 38 39 30 30  20 45 95 80   65 94 3f 04  09 50 31 00  8900 E..e.?..P1.
   50: 3e be af 00  00 00 00 00   00 00 00 00  00 00 00 00  >...............
   60: 00 00 b3 71  00 00 8f 00   04 00 40 00  00 00 00 00  ...q......@.....
   70: 00 00 00 00  00 00 7f 01   00 3c 00 00  00 00 00 00  .........<......

Machine specific notes
----------------------

C8000 seems to obey the PIROM maximum frequency setting in PIROM and caps frequency
on that limit. If the 4 bytes starting at 0x2a in the PIROM, it assumes the CPU
can do 1.1 Ghz, otherwise the maximum is 1GHz.

On rp3440, the cpu frequency seems to be taken only from the SCRATCH EEPROM and
the maximum limit is 1.5Ghz. As there is no PA-RISC CPU with 1.5Ghz, maybe the
engineers expected faster CPUs or the existing CPUs are actually faster but where
limited in speed to give Itanium an advantage.
