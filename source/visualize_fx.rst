===============
HP Visualize FX
===============

The Visualize FX is a workstation graphics card manufactured by HP, and
commonly found in HP PA-RISC workstations. It comes in PCI variants for
PA-RISC, and an AGP variant for use in some x86 HP workstations.
Unfortunately, no documentation is available for the card, so we're
working more-or-less blind.

A GPL driver is available for the x86-AGP variant, unfortunately it
simply sets up DRI in order to allow a binary X driver to card
initialization, etc. However, the register list it provides has been
useful.

KyleMcMartin has been working on reverse engineering the card, but
there's no working code yet.

Physical Characteristics
------------------------

- Visualize FX 2, 4, 6 all have EVC connectors, which require an adapter
  to go to standard HD-15 VGA monitors.

- Visualize FX 2, 4, 6 all have a small white connector for attaching
  peripherals such as the "Video Out Board" (A4556A).

- Visualize FX 4, 6 also have a long thin connector for connecting
  texture memory.

- Visualize FX 6 has a connector on the back to add the geometry
  accelerator card which is interesting in and of itself as it has
  RJ45-looking ports labelled "COVE mode" on it's geometry accelerator.

- Visualize FX 5, 10 have no connectors onboard, but are still full
  length cards.

- Visualize FXe has neither of these connectors, is much smaller (only
  one IC) and has HD-15 VGA output.

Chipset Info
------------

.. list-table::
   :header-rows: 1

   - 

      - Card
      - Family
      - GAs
      - TMUs
      - RASTs
   - 

      - Visualize FX2
      - Summit
      - TODO
      - TODO
      - TODO
   - 

      - Visualize FX4
      - Summit
      - TODO
      - TODO
      - TODO
   - 

      - Visualize FX6
      - Summit
      - TODO
      - TODO
      - TODO
   - 

      - Visualize FX5
      - Lego
      - TODO
      - 
      - TODO
   - 

      - Visualize FX10
      - Lego
      - TODO
      - 
      - TODO
   - 

      - Visualize FXe
      - Pinnacle
      - TODO
      - TODO
      - TODO

.. note::

   Visualize FX5 and FX10 apparently combined the texture and rasterizer
   units into one unit.

Video Out Board (A4556A)
------------------------

One input port, and three output ports. Input port labelled Genlock for
synching to an external clock. Output ports labelled S-Video, CVBS, and
Beta SP.

Interrupts
----------

Visualize FX {2,4,5,6,10} do not get issued a PCI INT# line. Instead,
they interrupt by writing to eim_addr.

Visualize FXe does get issued a PCI INT# line.

STI Graphics IDs
----------------
::

    id 2fc1066b-9a02587, conforms to spec rev. 8.09 <- Visualize FX2 in B180
    id 2fc1066b-9a02587, conforms to spec rev. 8.09 <- Visualize FX4 in C3000
    id 2fc1066b-9a02587, conforms to spec rev. 8.09 <- Visualize FX6 in C3000
    id 35acda30-9a02587, conforms to spec rev. 8.0d <- Visualize FX5 in J6000, A1299 (Fx10pro?) (4 chips), A1262A -> FX5 (3 chips)
    id 35acda16-9a02587, conforms to spec rev. 8.0c <- Visualize FXe in J6000, A4982B

lspci output
------------

- **Visualize FXe in J6000**
  ::

    0000:01:01.0 3D controller: Hewlett-Packard Company Visualize FXe (rev 02)
    Subsystem: Hewlett-Packard Company: Unknown device 108c
    Control: I/O- Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr+
             Stepping- SERR- FastB2B-
    Status: Cap- 66MHz+ UDF- FastB2B+ ParErr- DEVSEL=medium >TAbort-
             <TAbort- <MAbort- >SERR- <PERR-
    Latency: 255
    Interrupt: pin A routed to IRQ 22
    Region 0: Memory at fb000000 (64-bit, prefetchable) [size=16M]
    Region 2: Memory at fa000000 (64-bit, non-prefetchable) [size=16M]
    Expansion ROM at f5000000 [disabled] [size=128K]

- **Visualize FX5 in J6000**
  ::

    0000:01:01.0 Display controller: Hewlett-Packard Company Visualize FX4 (rev 02)
    Control: I/O- Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr+
             Stepping- SERR+ FastB2B-
    Status: Cap- 66MHz+ UDF- FastB2B+ ParErr- DEVSEL=medium >TAbort-
             <TAbort- <MAbort- >SERR- <PERR-
    Latency: 255
    Region 0: Memory at fa000000 (64-bit, non-prefetchable) [size=32M]
    Expansion ROM at f5000000 [disabled] [size=2M]

- **Visualize FX2 in B180** - I suppose Interrupt is bogosity, as this FX2 also interrupts using IO_EIM.
  ::

    0000:00:01.0 Display controller: Hewlett-Packard Company Visualize FX4 (rev 02)
    Control: I/O- Mem+ BusMaster- SpecCycle- MemWINV- VGASnoop- ParErr+
             Stepping- SERR+ FastB2B-
    Status: Cap- 66MHz+ UDF- FastB2B+ ParErr- DEVSEL=medium >TAbort- <TAbort-
             <MAbort- >SERR- <PERR-
    Interrupt: pin ? routed to IRQ 22
    Region 0: Memory at f4000000 (64-bit, non-prefetchable) [size=32M]
    Expansion ROM at f0c00000 [disabled] [size=2M]

- **Visualize FX4 in C3000**
  ::

    0000:03:02.0 Display controller: Hewlett-Packard Company Visualize FX4 (rev 02)
    Control: I/O- Mem+ BusMaster+ SpecCycle- MemWINV- VGASnoop- ParErr+ Stepping-
             SERR+ FastB2B-
    Status: Cap- 66MHz+ UDF- FastB2B+ ParErr- DEVSEL=medium >TAbort- <TAbort-
            <MAbort- >SERR- <PERR-
    Latency: 255
    Region 0: Memory at fa000000 (64-bit, non-prefetchable) [size=32M]
    Expansion ROM at f7000000 [disabled] [size=2M]

.. note::

   Annoyingly, it appears all Lego/Summit cards get the same PCI Id
   (103c:1008). The database should probably be updated someday to
   account for this. However, the STI graphics id will be different for
   each card family, and the number of GA/TMU for each card.

Modus Operandi
--------------

HP-UX has interesting behaviour with these cards, observable by using
the kernel debugger. FX4 at least hits functions in libgraf.a named such
things as summit_init_graph, etc. With my FX5, I cannot hit breakpoints
set on these functions, so I would assume that the STI ROM on board the
FX4 is buggy, and instead of using the onboard, a software one is used.
(TODO: check to see if FX5/HP-UX hits some kind of internal STI call
that FX4 doesn't.)

It's worth mentioning that while experimenting, I noticed that my HP-UX
11v1 Dec 2003 release does not appear to support the FXe. With graphical
console configured, the installer won't even load, complaining during
the ITE initialization and then rebooting more quickly than I can read.
(Is this a buggy card, or an unsupported card?) If you swap in an
installed HP-UX disk, it seems to hang after ISL when booting with a FXe
installed (maybe it's still running, just console-less?). Don't yet know
what the deal with this is.

Spaces
------

Registers can be addressed in a number of ways, via different paths,
depending on what the hardware has mapped at any given time. Names and
addresses come from lego_address.h in the hpgraphics source rpm.

.. list-table::
   :header-rows: 1

   - 

      - Base Addresses
      - Address
      - Notes
   - 

      - SUMMIT_BASE
      - 0x0000'0000
      - ((SAM_BASE << 25) & 0xfe000000)
   - 

      - CTL_BASE
      - 0x0000'0000
      - SUMMIT_BASE + 0x0000'0000
   - 

      - MISC_CTL_BASE
      - 0x0000'0000
      - CTL_BASE + 0x0000'0000
   - 

      - STI_BASE
      - 0x0000'0000
      - MISC_CTL_BASE + 0x0000'0000
   - 

      - UNBUF_PRIV_BASE
      - 0x0020'0000
      - MISC_CTL_BASE + 0x0020'0000
   - 

      - BUF_PRIV_BASE
      - 0x0028'0000
      - MISC_CTL_BASE + 0x0028'0000
   - 

      - PRIV_CD_DIRECT
      - 0x0030'0000
      - MISC_CTL_BASE + 0x0030'0000
   - 

      - DIRECT_CD_BASE
      - 0x0038'0000
      - MISC_CTL_BASE + 0x0038'0000
   - 

      - UNBUF_CTL_BASE
      - 0x0040'0000
      - CTL_BASE + 0x0040'0000
   - 

      - BUF2D_CTL_BASE
      - 0x0080'0000
      - CTL_BASE + 0x0080'0000
   - 

      - BUF3D_CTL_BASE
      - 0x00c0'0000
      - CTL_BASE + 0x00c0'0000
   - 

      - FB_BASE
      - 0x0100'0000
      - SUMMIT_BASE + 0x0100'0000
   - 

      - FB1_BASE
      - 0x0200'0000
      - SUMMIT_BASE + 0x0200'0000
   - 

      - FB2_BASE
      - 0x0300'0000
      - SUMMIT_BASE + 0x0300'0000

The following category selectors give register position inside each base
address.

.. list-table::
   :header-rows: 1

   - 

      - Category
      - Selector
      - Notes
   - 

      - SAM_VDP
      - 0x00
      - Video Display Processor
   - 

      - SAM_VIDEO_OUT
      - 0x01
      - Video Out
   - 

      - SAM_GA_A
      - 0x04
      - Geometry Accelerator A
   - 

      - SAM_GA_B
      - 0x05
      - Geometry Accelerator B
   - 

      - SAM_FTF
      - 0x06
      - Float to Fix Conversion
   - 

      - SAM_TM
      - 0x07
      - Texture Mapping
   - 

      - SAM_RASTER
      - 0x08
      - Rasterizer
   - 

      - SAM_FBC
      - 0x09
      - Frame Buffer Controller
   - 

      - SAM_IMAGE
      - 0x0c
      - Imaging
   - 

      - SAM_VOLUME
      - 0x0d
      - Volumetric
   - 

      - SAM_RETARG
      - 0x0e
      - Retargeter
   - 

      - SAM_SHARED
      - 0x10
      - Shared
   - 

      - SAM_HOSTIF
      - 0x12
      - Host Interface
   - 

      - SAM_BINC
      - 0x13
      - Summit Binc
   - 

      - SAM_DMA
      - 0x15
      - DMA Controller
   - 

      - SAM_OFU
      - 0x16
      - Object Function Unit
   - 

      - SAM_OGL_ST
      - 0x17
      - OpenGL State
   - 

      - SAM_MFU
      - 0x18
      - Macro Function Unit
   - 

      - SAM_MFU_REMAP
      - 0x19
      - Macro Function Remapped

Unbuffered privileged base addresses, **(UNBUF_PRIV_BASE \| (SAM\_\* <<
14))**

.. csv-table::
   :header-rows: 1

   UP_BASE,Address,Notes
   UP_VDP_BASE,0x0020'0000,
   UP_VIDEO_OUT_BASE,0x0020'4000,
   UP_FTF_BASE,0x0021'8000,
   UP_TM_BASE,0x0021'c000,
   UP_RASTER_BASE,0x0022'0000,
   UP_FBC_BASE,0x0022'4000,
   UP_IMAGE_BASE,0x0023'0000,
   UP_VOLUME_BASE,0x0023'4000,
   UP_RETARG_BASE,0x0023'8000,
   UP_SHARED_BASE,0x0024'0000,
   UP_HOSTIF_BASE,0x0024'8000,
   UP_BINC_BASE,0x0024'c000,
   UP_DMA_BASE,0x0025'4000,
   UP_OFU_BASE,0x0025'8000,
   UP_OGL_ST_BASE,0x0025'c000,
   UP_MFU_BASE,0x0026'0000,
   UP_MFU_REMAP_BASE,0x0026'4000,

Buffered privileged base addresses, **(BUF_PRIV_BASE \| (SAM\_\* << 14))**

.. csv-table::
   :header-rows: 1

   BP_BASE,Address,Notes
   BP_VDP_BASE,0x0028'0000,
   BP_VIDEO_OUT_BASE,0x0028'4000,
   BP_FTF_BASE,0x0029'8000,
   BP_TM_BASE,0x0029'c000,
   BP_RASTER_BASE,0x002a'0000,
   BP_FBC_BASE,0x002a'4000,
   BP_IMAGE_BASE,0x002b'0000,
   BP_VOLUME_BASE,0x002b'4000,
   BP_RETARG_BASE,0x002b'8000,
   BP_SHARED_BASE,0x002c'0000,
   BP_HOSTIF_BASE,0x002c'8000,
   BP_BINC_BASE,0x002c'c000,
   BP_DMA_BASE,0x002d'4000,
   BP_OFU_BASE,0x002d'8000,
   BP_OGL_ST_BASE,0x002d'c000,
   BP_MFU_BASE,0x002e'0000,
   BP_MFU_REMAP_BASE,0x002e'4000,

Unbuffered base addresses, **(UNBUF_CTL_BASE \| (SAM\_\* << 17))**

.. csv-table::
   :header-rows: 1

   UB_BASE,Address,Notes
   UB_VDP_BASE,0x0040'0000,
   UB_VIDEO_OUT_BASE,0x0042'0000,
   UB_TM_BASE,0x004e'0000,
   UB_RASTER_BASE,0x0050'0000,
   UB_FBC_BASE,0x0052'0000,
   UB_IMAGE_BASE,0x0058'0000,
   UB_VOLUME_BASE,0x005a'0000,
   UB_RETARG_BASE,0x005c'0000,
   UB_SHARED_BASE,0x0060'0000,
   UB_HOSTIF_BASE,0x0064'0000,
   UB_BINC_BASE,0x0066'0000,
   UB_DMA_BASE,0x006a'0000,
   UB_OGL_ST_BASE,0x006e'0000,

2d buffered base addresses, **(BUF2D_CTL_BASE \| (SAM\_\* << 17))**

.. csv-table::
   :header-rows: 1

   B2_BASE,Address,Notes 
   B2_VDP_BASE,0x0080'0000,
   B2_VIDEO_OUT_BASE,0x0082'0000,
   B2_FTF_BASE,0x008c'0000,
   B2_TM_BASE,0x008e'0000,
   B2_RASTER_BASE,0x0090'0000,
   B2_FBC_BASE,0x0092'0000,
   B2_IMAGE_BASE,0x0098'0000,
   B2_VOLUME_BASE,0x009a'0000,
   B2_RETARG_BASE,0x009c'0000,
   B2_SHARED_BASE,0x00a0'0000,
   B2_HOSTIF_BASE,0x00a4'0000,
   B2_BINC_BASE,0x00a6'0000,
   B2_DMA_BASE,0x00aa'0000,
   B2_OFU_BASE,0x00ac'0000,
   B2_OGL_ST_BASE,0x00ae'0000 ,
   B2_MFU_BASE,0x00b0'0000,
   B2_MFU_REMAP_BASE,0x00b2'0000,

3d buffered base addresses, **(BUF3D_CTL_BASE \| (SAM\_\* << 17))**

.. csv-table::
   :header-rows: 1

   B3_BASE,Address,Notes 
   B3_GA_A_BASE,0x00c8'0000,
   B3_GA_B_BASE,0x00ca'0000,
   B3_FTF_BASE,0x00cc'0000,
   B3_TM_BASE,0x00ce'0000,
   B3_RASTER_BASE,0x00d0'0000,
   B3_IMAGE_BASE,0x00d8'0000,
   B3_VOLUME_BASE,0x00da'0000,
   B3_RETARG_BASE,0x00dc'0000,
   B3_SHARED_BASE,0x00e0'0000,
   B3_HOSTIF_BASE,0x00e4'0000,
   B3_DMA_BASE,0x00ea'0000,
   B3_OGL_ST_BASE,0x00ee'0000,

CD Buffers
----------
::

    <lamont> concept is that you spew an address, indication of 
             whether or not to increment as you go, and a number of 
             data words to write (starting) at that address
    <lamont> followed by the data
    <lamont> the hardware then turns that into the actual writes.
    <lamont> the specific design issue that it is dealing with is: 
             weakly orderd I/O space writes.
    <lamont> since the CD buffer space is a nice contiguous block of 
             I/O space, and is written sequentially, you can (1) 
             have the hardware notice what registers haven't been 
             written yet, and not process past that, and (2) you can 
             DMA the CD buffer to the card, rather than using PIO.

The following comes from hpgraphics/headers/lego_hw_macros.h:316 from
the hpgraphics DRI driver.

.. code-block:: cpp

    /*
     * First we want a macro that will create a CD packet header. This header has
     * three fields:
     *
     *   o bits 27-31 : The number of data words in this packet
     *   o bit  25    : Flag telling us whether to increment the address every time
     *                  we write the packet data. 0 means increment.
     *   o bit  24    : Flag telling us to wrap the hardware CD buffer pointer back to
     *                  the top of the CD buffer. This needs to be done every time we
     *                  wrap the software CD buffer pointer.
     *   o bits 0-23  : The starting address where we will write the packet data
     *
     * For more information, refer to section 3.5.1 in the Lego96 ERS
     */
    #define CREATE_CD_PACKET_HEADER(_count, _noCount, _wrap, _addr) \
        (((_count) << 27) | ((_noCount) << 25) | ((_wrap) << 24) | (_addr))


So basically we build a packet saying how much data we are going to
write, what 24-bit address on the card we want to write to, and whether
to increment the address, and write all of it to CD Buffer space on the
card.

Thanks to LaMontJones for clarifying this.

Registers
---------

- **TODO**: Check endianness

UP_CONTROL (0x0024'9000)
~~~~~~~~~~~~~~~~~~~~~~~~
.. code-block:: cpp

    typedef union {
            struct {
                    unsigned res0   :1;     /* <31> reserved */
                    unsigned him    :1;     /* <30> ??? */
                    unsigned hwie   :1;     /* <29> ? HWC interrupt enable */
                    unsigned lwie   :1;     /* <28> ? LWC interrupt enable */
                    unsigned tmie   :1;     /* <27> ? TMU interrupt enable */
                    unsigned vbie   :1;     /* <26> ? VIDBUS interrupt enable */
                    unsigned udce   :1;     /* <25> ??? */
                    unsigned bdce   :1;     /* <24> ??? */
                    unsigned res1   :1;     /* <23> reserved */
                    unsigned hic    :1;     /* <22> ??? */
                    unsigned hwic   :1;     /* <21> ??? */
                    unsigned lwic   :1;     /* <20> ??? */
                    unsigned tmic   :1;     /* <19> ??? */
                    unsigned vbic   :1;     /* <18> ??? */
                    unsigned udcc   :1;     /* <17> ??? */
                    unsigned bdcc   :1;     /* <16> ??? */
                    unsigned udpc   :1;     /* <15> ??? */
                    unsigned bdpc   :1;     /* <14> ??? */
                    unsigned to     :1;     /* <13> Time Out prevented, must RST */
                    unsigned tce    :1;     /* <12> Timeout Circuit Enable */
                    unsigned udpe   :1;     /* <11> ??? */
                    unsigned bdpe   :1;     /* <10> ??? */
                    unsigned wfc    :1;     /* <9> Write Fifo Control */
                    unsigned rst    :1;     /* <8> Soft RST */
                    unsigned scratch:8;     /* <7:0> scratch */
            } bits;
            unsigned all;
    } control_t;

UP_INT_ADDR (0x0024'9200)
~~~~~~~~~~~~~~~~~~~~~~~~~

CPU Address interrupts will be targeted at. Use txn_alloc_irq(5) to
generate a suitable address. The register is 32-bits wide, so clearly
some piece hardware will F-extend before driving the address onto the
system bus.

UP_INT_DATA (0x0024'9240)
~~~~~~~~~~~~~~~~~~~~~~~~~

Payload of write to CPU Interrupt space @ address INT_ADDR.

UP_PSTI_SCRATCH1 (0x0024'9180)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UP_PSTI_SCRATCH2 (0x0024'91a0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UP_PSTI_SCRATCH3 (0x0024'91c0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UP_PSTI_SCRATCH4 (0x0024'91e0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UB_PSTI_SCRATCH1 (0x0064'1180)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UB_PSTI_SCRATCH2 (0x0064'11a0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UB_PSTI_SCRATCH3 (0x0064'11c0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UB_PSTI_SCRATCH4 (0x0064'11e0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UB_STI_SCRATCH1 (0x0064'1580) also UB_CLIP_PLANE_STAMP (Lego only?)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UB_STI_SCRATCH2 (0x0064'15a0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UB_STI_SCRATCH3 (0x0064'15c0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

UB_STI_SCRATCH4 (0x0064'15e0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

These appear to be temporary registers used to set flags that can be
passed between routines.

UB_STATUS (0x0064'1400)
~~~~~~~~~~~~~~~~~~~~~~~

from hpgraphics/headers/lego_hw.h:87:

.. code-block:: cpp

    typedef union {
        struct {
            BITFIELD_18(
            Uint32	UBDRdy 	: 2,	/* <31:30> UnBuffered Data Ready */
            Uint32	BDRdy  	: 2,	/* <29:28> Buffered Data Ready */
            Uint32	Fault  	: 1,	/* <27> Fault Detected */
            Uint32	Ubuff  	: 1,	/* <26> Unbuffered Pipe Busy */
            Uint32	IMODE  	: 1,	/* <25> Input Model (1=>CD Buffer) */
            Uint32	WPNE  	: 1,	/* <24> Write Pipe Not Empty */
            Uint32	HIS  	: 1,	/* <23> Host Interrupt Sent */
            Uint32	res22  	: 1,	/* <22> reserved (was FIR) */
            Uint32	HWIR  	: 1,	/* <21> High Water Interrupt Req. */
            Uint32	LWIR  	: 1,	/* <20> Low Water Interrupt Req. */
            Uint32	TMIR  	: 1,	/* <19> Texture Map Interrupt Req. */
            Uint32	VBIR  	: 1,	/* <18> Vertical Blank Interrupt Req. */
            Uint32	UDCR  	: 1,	/* <17> Unbuffered DMA Complete Req. */
            Uint32	BDCR  	: 1,	/* <16> Buffered DMA Complete Req. */
            Uint32	UDPR  	: 1,	/* <15> Unbuff Priv DMA Complete Req.*/
            Uint32	BDPR  	: 1,	/* <14> Buff Priv  DMA Complete Req. */
            Uint32	res12  	: 2,	/* <13:12> reserved */
            Uint32	ffc12 	: 12	/* <11:0> FIFO Free Count (LSBs) */
            )
        }	fields;
        Uint32 bits;
    } STATUS_Type;

Valid bits for UBDRdy/BDRdy, this means the READ_DATA registers has
either a requested single or double word waiting in it::

    #define STATUS_WordRdy      (1)
    #define STATUS_DoubleRdy    (3)

UB_IRC (0x0064'3030)
~~~~~~~~~~~~~~~~~~~~

UB_UIRC (0x0064'3034)
~~~~~~~~~~~~~~~~~~~~~

B2_IRC (0x00A4'3030)
~~~~~~~~~~~~~~~~~~~~

B3_IRC (0x00E4'3030)
~~~~~~~~~~~~~~~~~~~~

Buffered/Unbuffered Indirect Read Control registers.

.. code-block:: cpp

    /* PDU IRC (indirect Read Control) */
    typedef union {
        struct {
            BITFIELD_3(
            Uint32	unused1	    	: 29,
            Uint32	op		:  2,	/* opcode for setting WTR function    */
            Uint32	WTR		:  1	/* Write To Read state flag           */
            )
        } fields;
        Uint32  bits;
    } IRC_Type;

Valid IRC_type.fields.op functions:

.. code-block:: cpp

    #define PDU_IRC_NOOP	0   /* write is ignored */
    #define PDU_IRC_IREAD	1   /* indirect read of IRC register */
    #define PDU_IRC_CLEAR	2   /* clear WTR function (disable it) */
    #define PDU_IRC_SET	3   /* set WTR function (enable it) */

B2_OTR - Overlay Transparency Register (0x0092'1148)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- ??? It seems 3 is written to this during initialization in summit_init_graph.
- It's also written about the time the texture mapping system is setup with some other value.

B2_WORG (0x00a0'0818)
~~~~~~~~~~~~~~~~~~~~~

Zero is always stored to this inside the kernel, apparently.

UB_UREAD_DATA (0x0064'14c0)
~~~~~~~~~~~~~~~~~~~~~~~~~~~

B2_READ_DATA (0x00a4'1480)
~~~~~~~~~~~~~~~~~~~~~~~~~~

B3_READ_DATA (0x00e4'1480)
~~~~~~~~~~~~~~~~~~~~~~~~~~

These registers contain the result of the latest indirect
(write-to-read) read request. Check for validity with UBDRdy/BDRdy in
the STATUS registers. Typically code sequences look like write -1 to a
register, call wait_buf_data, check it's return, and then read the
appropriate READ_DATA register.

Glossary
--------

- Antero - Summit texture engine.
- B7 - Geometry accelerator on Lego cards.
- Blitzen - Geometry accelerator on Summit cards.
- CD buffer - Command-Data buffers, can be host side (DMA), or card side (CD Space).
- Concorde - Command processor on Summit cards.
- Donner - Codename of the Visualize FX6.
- Durango - Codename of the Visualize FX5?
- Eolus - Rasterizer on Summit cards.
- Heathrow - Command processor on Lego cards.
- ITE - Internal Terminal Emulator. The thingy that provides console on HP-UX.
- Lego - Architecture of later VisFX (e/5/10).
- Pinnacle - Codename of the Visualize FXe, comes in two flavours, Pinnacle I, and Pinnacle II.
- Rockwood - Codename of Lego? Rockwood/Silverton/Durango are all related to railways in southwest Colorado.
- RT - Raster/Texture engine on Lego cards.
- Silverton - Codename of the Visualize FX10?
- Summit - Architecture of early VisFX (2/4/6).
- WarpDrive - Codename of the Visualize FX (WarpDrive24 == FX2/ WarpDrive48 == FX4).
