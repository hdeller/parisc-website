=====================
C8000 Debug connector
=====================

J13 2x40 pin 1mm pitch connector:

.. list-table::
   :header-rows: 1

   - 

      - Pin
      - Name
      - Pin
      - Name
   - 

      - A1
      - +3.3V
      - B1
      - +5V
   - 

      - A2
      - PDC Flash RP#
      - B2
      - 
   - 

      - A3
      - +3.3V
      - B3
      - 
   - 

      - A4
      - +3.3V
      - B4
      - 
   - 

      - A5
      - SMBUS SCL
      - B5
      - PDC Flash DQ0
   - 

      - A6
      - SMBUS SDA
      - B6
      - PDC Flash DQ8
   - 

      - A7
      - 
      - B7
      - PDC Flash DQ1
   - 

      - A8
      - 
      - B8
      - PDC Flash DQ9
   - 

      - A9
      - SMBUS2 SCL
      - B9
      - PDC Flash DQ2
   - 

      - A10
      - SMBUS2 SDA
      - B10
      - PDC Flash DQ10
   - 

      - A11
      - PDC Flash BYTE#/BMC SRAM LB#
      - B11
      - PDC Flash DQ3
   - 

      - A12
      - BMC SRAM UB#
      - B12
      - PDC Flash DQ11
   - 

      - A13
      - 
      - B13
      - PDC Flash DQ4
   - 

      - A14
      - BMC SRAM CS#
      - B14
      - PDC Flash DQ12
   - 

      - A15
      - 
      - B15
      - PDC Flash DQ5
   - 

      - A16
      - 
      - B16
      - PDC Flash DQ13
   - 

      - A17
      - 
      - B17
      - PDC Flash DQ6
   - 

      - A18
      - 
      - B18
      - PDC Flash DQ14
   - 

      - A19
      - 
      - B19
      - PDC Flash DQ7
   - 

      - A20
      - 
      - B20
      - PDC Flash DQ15
   - 

      - A21
      - 
      - B21
      - PDC Flash OE#
   - 

      - A22
      - 
      - B22
      - PDC Flash WE#
   - 

      - A23
      - 
      - B23
      - PDC Flash A22 [#a22]_
   - 

      - A24
      - PDC Flash A21
      - B24
      - PDC Flash A20
   - 

      - A25
      - PDC Flash A19
      - B25
      - PDC Flash A18
   - 

      - A26
      - PDC Flash A17
      - B26
      - PDC Flash A16
   - 

      - A27
      - PDC Flash A15
      - B27
      - PDC Flash A14
   - 

      - A28
      - PDC Flash A13
      - B28
      - PDC Flash A12
   - 

      - A29
      - PDC Flash A11
      - B29
      - PDC Flash A10
   - 

      - A30
      - PDC Flash A9
      - B30
      - PDC Flash A8
   - 

      - A31
      - PDC Flash A7
      - B31
      - PDC Flash A6
   - 

      - A32
      - PDC Flash A5
      - B32
      - PDC Flash A4
   - 

      - A33
      - PDC Flash A3
      - B33
      - PDC Flash A2
   - 

      - A34
      - PDC Flash A1
      - B34
      - PDC Flash A0
   - 

      - A35
      - CLK 25MHZ
      - B35
      - 
   - 

      - A36
      - 
      - B36
      - 
   - 

      - A37
      - 
      - B37
      - 
   - 

      - A38
      - 
      - B38
      - 
   - 

      - A39
      - 
      - B39
      - 
   - 

      - A40
      - +5V
      - B40
      - +3.3V

.. [#a22] Only if an 8MiB PDC flash chip is installed, and resistors R422 and R423 (located next to the Spartan FPGA) are set appropriately - install R422 for 8MiB, R423 for 4MiB.
