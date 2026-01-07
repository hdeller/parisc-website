==================================================
Hidden B/C/J Class Manufacturer and DEBUG commands
==================================================

According to some PDC docs, there seems to be a "mode byte" in NVM
storage. Values are probably:

- Normal mode = ``0x4E`` (ASCII N)
- MFG mode = ``0x4D``  (ASCII M)
- Debug mode = ``0x44`` (ASCII D)

After repairing my J5000 (EEPROM was corrupted due to PDC experiments),
the Bootloader showed some additional Entries::

    Mfg Menu: Enter command > debug

    ----- Debug Menu ------------------------------------------------------------

          Command                           Description
          -------                           -----------
          CLearScreen                       Clear the screen
          EepromFlag [<flag> [ON|OFF]]      Display or set EEPROM Flag values
          Graphics_INfo                     Display graphics information
          HPMC                              Generate an HPMC
          IODC <addr> ... <arg7>            Execute IODC procedure
          MODel                             Display model string
          Path_INfo [<path>]                Display mnemonic path information
          PCI_info                          Display PCI information
          PDCE [Narrow] <arg0>...<arg7>     Execute PDC procedure
          SideLoad [DIsplay]                Copy firmware from Sabre to Flash Rom
          SPeed                             Measure various speeds
          Sys_Info_Map                      Display SYS_INFO_MAP
          TOC                               Soft boot the system

          BOot [PRI|ALT|<path>]             Boot from specified path
          DIsplay                           Redisplay the current menu
          HElp [<menu>|<command>]           Display help for menu or command
          RESET                             Restart the system
          MAin                              Return to Main menu
    -----
    Debug Menu: Enter command >

::

    Debug Menu: Enter command > mfg

    ----- Manufacturing Menu ----------------------------------------------------

          Command                           Description
          -------                           -----------
          DefaultAll                        Set all Stable Store to defined values
          DimmRead [<slot>]                 Read serial EEPROM on DIMM/s
          EepromRead [<addr> [<len>]]       Read EEPROM locations
          EepromWrite <addr> <data> ...     Write EEPROM locations
          FAn [Domain1|Domain2              Display/change fan speed settings
              <lo> <md> <hi>]
          LanAddress [<addr>]               Display or set system LAN address
          LIcense [ON|OFF]                  Display or set diagnostic licensing
          MemRead <addr> [<len>] [<type>]   Read memory locations
          MemWrite <addr> <data> ...        Write memory locations
          MfgConsole [<path>]               Display or modify default console path
          MoDe [MFG|DeBug|NOrmal]           Display or change system mode
          OVerride [ON|OFF]                 Override fan failure power down
          ScRoll [ON|OFF]                   Display or change scrolling ability
          SerialNum [<serial number>]       Read or write serial number
          STable [AR|UN] <addr> ...         Read or write Stable Storage locations
          VErsion                           Display PDC internal version number

          BOot [PRI|ALT|<path>]             Boot from specified path
          DIsplay                           Redisplay the current menu
          HElp [<menu>|<command>]           Display help for menu or command
          RESET                             Restart the system
          MAin                              Return to Main menu
    -----
    Mfg Menu: Enter command >

::

    Debug Menu: Enter command > eepromflag
         0  Use eeprom LDB checks         OFF
         1  Use EEPROM test control       OFF
         2  Update EEPROM fwd progress    OFF
         3  Allow HPMC PIM overwrite      OFF
         4  LDB_DEBUG_TRAP_IGNORE_BIT     OFF
         5  No cycle codes in halt CPU    OFF
         6  USE_SPEC_TOC_VECTOR_BIT       OFF
         7  Disable BCH scroll control    OFF
         8  INIT_EEPROM_CONFIG_BIT        OFF
         9  DISABLE_AIO_BIT               OFF
        10  Don't do a PDC ROM checksum   OFF
        11  Enable Serial1 remote power   OFF
        12  DISABLE_CCP_CHASSIS           OFF
        13  DISABLE_CCP_BIT               OFF
        14  ENABLE_LDB_TRIG_AT_LAUNCH     OFF
        15  Skip full calc of MEM_10MSEC  OFF
        16  Secure mode                   OFF
        17  Honor auto flags in MFG mode  OFF
        18  Skip graphics init            OFF
        19  Memory init only              OFF
        20  Skip late self tests          OFF
        21  Skip early self tests         OFF
        22  Disable all FP units          OFF
        23  Loop on early selftests       OFF
        24  Loop on late selftests        OFF
        25  No auto boot if uncleared PIM OFF
        26  Error handler invokes LDB     OFF
        27  Error handler dumps state     OFF
        28  DISABLE_XHOST_MSG             OFF
        29  Enable proc call trace        OFF
        30  Always emit chassis codes     OFF
        31  Init mem system from ROM tableOFF



====================================================
Hidden rp3440/rp3410 Manufacturer and DEBUG commands
====================================================

The rp34x0 series has a "mode byte" as well:

::

 *** Manufacturing permissions ON ***
    System is hp server series

::

 Service Menu: Enter command > ss_update

 ---- SS_update Menu ---------------------------------------------------

     Command                           Description
     -------                           -----------
     SERialNumb                        Change or update serial number
     SYSMOdel                          Upgrade/downgrade system model
     DIsplay                           Display the ss_update menu
     MAin                              Display the main menu
     HElp [<command>]                  Display help for specified command
 -----
 SS_update Menu: Enter command > sysmodel
  
 Choose a model for this system.
 Type a number to choose model or the word "none".
      1:  9000/800/rp3440 PA8800 2 sockets 4 cores (used if >800Mhz)
      2:  9000/800/rp3440 PA8800 2 sockets 4 cores
      3:  9000/800/rp3410 PA8800 1 socket  2 cores restricted speed DC-
      4:  9000/800/rp3410 PA8800 1 socket  1 core restricted speed DC--
      5:  9000/800/rp3440#1 PA8800 1 sockets 1 core
      6:  9000/800/rp3440 PA8900 2 sockets 4 cores (used if >800Mhz)
      7:  9000/800/rp3440 PA8900 2 sockets 4 cores
      8:  9000/800/rp3410 PA8900 1 socket  2 cores restricted speed DC-
      9:  9000/800/rp3410 PA8900 1 socket  1 core restricted speed DC--
      10:  9000/800/rp3440#1 PA8900 1 sockets 1 core
   none:  don't change it

::

 DeBug Menu: Enter command > EepromFlag
     0  Use NVM copy of all flags  OFF    32  Spare                      OFF
     1  Spare                      OFF    33  Spare                      OFF
     2  Spare                      OFF    34  Skip Mem Quick SPD         OFF
     3  Spare                      OFF    35  Bypass CPU Mismatch Check  OFF
     4  No ROM Checksum            OFF    36  Spare                      OFF
     5  Disable Early Self Tests   OFF    37  Ignore HALT_BOOT/allow bootOFF
     6  Spare                      OFF    38  Spare                      OFF
     7  Disable Late Self Tests    OFF    39  Spare                      OFF
     8  DC Special Upgrade         OFF    40  Spare                      OFF
     9  Spare                      OFF    41  Disable PdcDebugOut        OFF
    10  Spare                      OFF    42  Spare                      OFF
    11  Spare                      OFF    43  Disable BCH scroll control ON
    12  Spare                      OFF    44  Init nvm config            OFF
    13  Spare                      OFF    45  Spare                      OFF
    14  Spare                      OFF    46  Spare                      OFF
    15  Spare                      OFF    47  Spare                      OFF
    16  Spare                      OFF    48  Spare                      OFF
    17  Spare                      OFF    49  Spare                      OFF
    18  Spare                      OFF    50  Spare                      OFF
    19  Spare                      OFF    51  Spare                      OFF
    20  Spare                      OFF    52  Bypass CPU order check     OFF
    21  Spare                      OFF    53  Bypass CPU dual core deconfOFF
    22  Spare                      OFF    54  Spare                      OFF
    23  Spare                      OFF    55  Spare                      OFF
    24  Spare                      OFF    56  Spare                      OFF
    25  Spare                      OFF    57  Spare                      OFF
    26  Spare                      OFF    58  Spare                      OFF
    27  Spare                      OFF    59  Enable CEC tests           OFF
    28  Spare                      OFF    60  Enable PDH tests           OFF
    29  Spare                      OFF    61  Enable Dest mem tests      OFF
    30  Spare                      OFF    62  Enable early CPU stests    OFF
    31  Spare                      OFF    63  Enable late CPU stests     OFF

