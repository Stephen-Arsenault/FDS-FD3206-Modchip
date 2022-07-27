
# FDS-FD3206-Modchip <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0;" src="https://i.creativecommons.org/l/by-sa/4.0/80x15.png" /></a>
A simple "no-wire" modchip for the Nintendo Famicom Disk System HVC-022 Disk Drive. This modchip reverses copy-protection restrictions applied by the Mitsumi FD3206 chip.

##Installation
 1. Start by disassembling your FDS HVC-022.
    - You can follow existing belt replacement guides.
    - Here's a guide from [famicomdisksystem.com article](https://www.famicomdisksystem.com/tutorials/fds-repair-mod/belt-replacement-adjustment/).
    - Stop once you reach step 5 of the article above, the removal of the PCB.
 2. Upon closer inspection of the PCB, you will find a large chip labeled `FD3206`.
 3. Place the `ATF16V8A-PU15` programmed with my source code with pin 1 overlapping pin 1 of the `FD3206`.
    - the orientation is easily noted by the presence of a semicircle on the far-end of each chip's surface.
    - This is the side with pin 1.
 4. Solder the following `ATF16V8A-15PU` pins:
     - This is the side with pin 1.
 5. It may be necessary to mod your HVC-022 Power Board.
     - You can learn more about this process in [this famicomworld.com article](https://famicomworld.com/workshop/tech/fds-power-board-modifications/).

## WinCUPL PLD Source Code
There are two versions of the WinCUPL PLD Source Code. The first is my version, which is directly analogous to the write-mod documented on [famicomworld.com](https://famicomworld.com/workshop/tech/famicom-disk-system-fd3206-write-mod/). The second was generously contributed by LIV2 and is a handy reference.  Both version are available in the repo directory.
<details>
<summary>My Version</summary>

```
Name     3602 Write Enabler ;
PartNo   00 ;
Date     7/24/2022 ;
Revision 01 ;
Designer Stephen ;
Company  BetterBit ;
Assembly None ;
Location  ;
Device   g16v8ms;

/* *************** INPUT PINS *********************/
PIN   1 = CLK                     ; /* Clock              */ 
PIN   4 = WRTGATE                 ; /* Write Gate         */ 
PIN   5 = WRTPRTCT                ; /* Write Protect      */ 
PIN   6 = WRTDATA                 ; /* Write Data         */ 
PIN  13 = READY                   ; /* Ready              */ 

/* *************** OUTPUT PINS *********************/
PIN  14 = WRTHD1                  ; /* Write Head 1       */ 
PIN  15 = WRTHD2                  ; /* Write Head 2       */ 
PIN  19 = !CLKOUT                 ; /* Clock Invert       */

/* ***************** Braining **********************/
/* - Write Data toggles flip flop.                        */
/* - Flip flop enters either a high or low output state   */
/* - Output state maps to "A" position on 74LS45          */                                           
/* - "Ready" maps to "B" position on 74LS45               */
/* - "Write Protect" maps to "C" position on 74LS45       */
/* - "Write Gate" maps to "D" position on 74LS45          */
/*                                                        */
/*        74LS45 TRUTH TABLE                              */
/*     -------------------------                          */
/*    | A | B | C | D | 01 | 02 |                         */
/*    | L | L | L | L | L  | H  |                         */
/*    | H | L | L | L | H  | L  |                         */
/*     -------------------------                          */

CLKOUT = !CLK ;

WRTHD1.d = CLKOUT &  WRTDATA & !WRTGATE & !WRTPRTCT & !READY ;
WRTHD2.d = CLKOUT & !WRTDATA & !WRTGATE & !WRTPRTCT & !READY ;
```
</details>

<details>
<summary>LIV2's Version</summary>

```
Name     FD3206 ;
PartNo   00 ;
Date     27/07/2022 ;
Revision 01 ;
Designer LIV2 ;
Company  BetterBit ;
Assembly None ;
Location  ;
Device   g16v8ms ;

/* *************** INPUT PINS *********************/
PIN 1 = write_data                ;
PIN 3 = ready                     ;
PIN 4 = write_protect             ;
PIN 5 = write_gate                ;
/* *************** OUTPUT PINS *********************/
PIN 14 = Q                     ;
PIN 12 = !write_head_1         ;
PIN 13 = !write_head_2         ;

Q.d = !Q;

write_head_1 = !Q & !ready & !write_protect & !write_gate;
write_head_2 = Q & !ready & !write_protect & !write_gate;
```
</details>

## Special Thanks
* Early PLD guidance and support was provided by Zane Kaminski ([GitHub](https://github.com/ZaneKaminski))
* A version of the source code was generously provided by LIV2 ([Twitter](https://twitter.com/LIV2)) one brief evening when I asked if anyone was available to review my code.
* Tomy of Tototek for providing closed source mod-chip options (available for purchase at [Tototek](https://www.tototek.com/store/index.php?main_page=product_info&cPath=1_35&products_id=228))


## License
This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.
