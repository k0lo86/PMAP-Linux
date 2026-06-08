# PlayStation 2 Mechacon Adjustment Program (PMAP) — 2015/12/22

> 🇵🇱 **Wersja polska:** [README_PL.md](README_PL.md)

The PlayStation 2 Mechacon Adjustment Program (PMAP) is a tool for maintaining the PlayStation 2 CD/DVD subsystem.
SONY has its own official tools that aid in the maintenance process of its consoles. This program is a clone of their tools.

It offers the following functionality:
1. EEPROM maintenance and updating
2. Electrical circuit adjustment
3. Mechanism (skew) adjustment

The electrical circuit and mechanism adjustment functions offer jitter measurement, the jitter measurement is only a rough value.
Only real jitter measuring equipment can give an accurate measurement of jitter.

Test points on the PlayStation 2 mainboard:
-------------------------------------------
```text
A) RS232C:
	TXD (JL610)
	RXD (JL611)
	+3.3V (JL613)
	GND (JL609)
	TEST MODE (Connect JL617 to GND)
B) RF-AMP circuit for DVD jitter meter
	RFAC (JL601)
	AGND (JL602, AGND must be used with RFAC).
C) Other test points:
	RFDC		(JL608)
	FE		(JL605)
	TE		(JL607)
	VC_+1.7V	(JL604)
	+8.5V		(JL430, JL431)
	GND		(JL432, JL433)
D) EEPROM Reset Mode on Dragon Models (50k+):
	EEP_CS and EEP_CE via resistor R6305
	If the legs of resistor R6305 are shorted,
	the EEP_CS line will go high,
	preventing the EEPROM from being read during the console's power-up.
	The EEPROM will only become accessible after the short is removed.
	Correct sequence:
	- Disconnect the power plug
	- Short the resistor legs
	- Reconnect the power plug (red light will turn on)
	- Press the power button (green light will turn on)
	- Unshort the resistor legs
	- Run pmap
	To verify that the EEPROM wasnt read on boot in Menu 4 (Show Ident Data), the CFC should display 00000000
```

About Optical Block (OP) types:
-------------------------------
There are two types of optical blocks: SONY and SANYO.
The SONY OP has SONY branding on it, while the SANYO OP has no branding.
Only consoles starting from the F-chassis can support the SANYO OP.

If the OP block is changed, the console must be reconfigured to support the new OP block.

About Object Lens types:
------------------------
There are two types of lenses for the SONY OP: T487 and T609K.
The T609K has a light yellow object lens protector (a ring around the object lens), while the T487 has a white protector.

For the D-chassis, the first lots of the KHS-400B with the T609K lens had a white object lens protector,
with a violet marking at the adjustment screw on the side of the base of the optical block.
Some lenses may have black, blue or green markings, but that does not mean that it is a new lens type.

There is no support for a SANYO OP with a T609K lens, so it's probably safe to assume that such a thing does not exist.

If the lens/OP block is swapped, the console must be reconfigured to support the new lens.

Real-Time Clock (RTC) IC:
-------------------------

There are two types of RTC ICs on the F-chassis:
```text
IC405	RS5C384AE2
IC416	BU9861FV-WE2
```

Importance of the button battery:
---------------------------------

There is a new BGA-based MechaCon on the F-chassis mainboard (CXP-103049-xxx).
The battery must be present, or the DVD-ROM circuit may not function correctly.

If the battery is removed or has run out, the RTC-ECR data on the RTC IC and EEPROM will be erased to 0.
This may result in the i.Link interface is not working properly. Therefore, install a good battery before adjustment and do not remove it.

EEPROM management
-----------------

The PlayStation 2 has an EEPROM chip that contains various configuration segments:
1. Disc detect
2. Servo
3. Tilt (unused on all consoles that do not have an auto-tilt motor)
4. PS2ID (model name + i.Link ID + Console ID)
5. Tray
6. DVD player
7. EEGS
8. OSD

This tool allows the EEPROM to be backed up and restored (up to G-chassis only), erased and for the defaults to be loaded.
The defaults for the SANYO OP (F-chassis and later) can also be loaded, allowing the OP to be changed to a SANYO OP. The MECHACON defaults are for a SONY OP.

Updates to the EEPROM parameters are also provided.

Warning! although the functionality is provided, do not erase the EEPROM or load the defaults for the ID region
without first planning how to restore the IDs! See the ID management section below for instructions on restoring
Serial, Model ID and EMCS after loading ID defaults.

Electrical circuit adjustment
-----------------------------

Adjustment of the electrical circuit is done automatically. Simply follow the on-screen instructions to complete the adjustment procedure.
You will need the following discs:
CD test disc 		- SCD-2700 (YEDS-18 for DTL-T10000)
DVD-SL test disc	- HX-504
DVD-DL test disc	- HX-505

If unavailable, regular discs (CD, DVD-SL and DVD-DL) can be used as a substitute for these discs, but the correct type of disc must be inserted.
Failing which, irreparable damage to the console's optical block may result!
If unsure, please use the corresponding PlayStation 2 discs. They have to be in a good condition.

You have to do this if you:
1. Changed or removed the OP block.
2. Changed or removed the spindle motor.
3. Changed the MECHACON IC.
4. Changed, erased or loaded the defaults to the EEPROM IC.

Mechanism (skew) adjustment
---------------------------

The mechanism adjustment involves two parts:
1. Tangential skew
2. Radial skew (automatic for B-chassis)

You need to do this if you:
1. Changed or removed the OP block
2. Changed or removed the spindle motor

Procedure for mechanism (skew) adjustment:
1. Remove the tray and move the tray mechanism to the close position.
2. Put a disc (GLD-DR01, or a DVD-SL disc) on the spindle motor with a chuck (clamp).
3. Do initialization using the appropriate INIT command for your disc type (see below).
4. Move the sled out (SLED OUT). Warning! the laser is now at the outer part of the disc!
5. Enter play mode (PLAY 1).
6. Read jitter (JITTER 256 recommended) as you make adjustments.
	You will have to repeat both radial and tangential skew adjustments until jitter is minimal (i.e. the sweet spot is found).
6a. Radial skew:
	i) If the console is a B-chassis, use the AUTO TILT motor adjustment function:
		*Automatically adjust radial skew with the ADJ command (TILT ADJ).
		*Stop play mode (STOP).
		*Move the sled back to the HOME position (SLED HOME) and enter PLAY mode again (PLAY 1).
		*Write the new radial-skew settings (TILT WRITE)
	ii) If the console is a non-auto-tilt motor model:
		*Adjust the radial skew adjustment screw until jitter is minimal.
6b. Tangential skew:
	*Move the sled out (SLED OUT), if it was moved back to the home position.
	*Adjust the tangential skew adjustment screw until jitter is minimal.
7. Stop play mode (STOP).
8. Move the sled back to the home position (SLED HOME).
9. Take the disc off and open the tray (TRAY OPEN).
10. Put the tray back on, and check that it can eject and retract properly.

Jitter measurement - INIT modes and sequence:
----------------------------------------------
Before reading jitter you must initialize the drive with the correct disc type using the INIT command.
Different disc types require different INIT modes:

  CD (use a CD test disc SCD-2700, or a regular CD disc):
    INIT CD
    SLED OUT
    PLAY 1
    JITTER 256

  DVD-SL (use a DVD test disc HX-504, or a single-layer DVD disc):
    INIT SKEW        <- use for skew (mechanism) adjustment
    -or-
    INIT DVD-SL      <- use for electrical circuit adjustment
    SLED OUT
    PLAY 1
    JITTER 256

  DVD-DL (use a DVD test disc HX-505, or a dual-layer DVD disc):
    INIT DVD-DL
    SLED OUT
    PLAY 1
    JITTER 256       <- read separately for Layer 0 (L0) and Layer 1 (L1, after PLAY FJ)

Jitter measurement modes:
  JITTER 1   - Single-sample jitter measurement (fastest, least accurate)
  JITTER 16  - Average of 16 samples (moderate accuracy)
  JITTER 256 - Average of 256 samples (slowest, most accurate - recommended for adjustment)

Note: JITTER reads the raw hex value from the MECHACON. Lower is better.
Target values are listed in the Adjustment thresholds section below.

Adjustment thresholds/targets:
------------------------------
```text
CD:
	a.	CD DET:				SONY OP 600-1600, SANYO OP 750-1800 /  SONY OP: 660-1760, SANYO OP: 825-1980
	b.	FE Loop gain (K13):		0x10 - 0x60
	c.	TE Loop gain (K23):		0x10 - 0x60
DVD-SL:
	a.	FE Loop gain (K13):		0x10 - 0x60
	b.	TE Loop gain (K23):		0x10 - 0x60
	c.	Jitter (256):			0 - 0x1B00 (DEX: 0x1000, T10000: 0x1400) / G/H/I-chassis: 0x3E00 (DEX: 0x2970)
	d.	Error rate (PI+PO CC):		0 - 100
	e.	Error rate (PI NCC):		0
DVD-DL:
	a.	DVD-DL DISC DETECT:		0x15 (DVD-DL)
	a.	(L0) FE Loop gain (K13):	0x10 - 0x60
	b.	(L0) TE Loop gain (K23):	0x10 - 0x60
	c.	(L0) Jitter (256):		0 - 0x2300 (DEX: 0x1200) / G/H/I-chassis: 0 - 0x4C00 (DEX: 0x2D00)
	d.	(L1) FE Loop gain (K13):	0x10 - 0x60
	e.	(L1) TE Loop gain (K23):	0x10 - 0x60
	f.	(L1) Jitter (256):		0 - 0x2300 (DEX: 0x1200) / G/H/I-chassis: 0 - 0x4C00 (DEX: 0x2D00)
Disc Detect CD/DVD Ratio:			>= 1.80 / G/H/I-chassis: >=1.73
EEPROM Checksum:				0
```

Supported chassis models:
-------------------------
```text
A-chassis:	SCPH-10000/SCPH-15000
B-chassis:	SCPH-30001
C-chassis:	SCPH-30001/2/3/4
D-chassis:	SCPH-30000/1/2/3/4, SCPH-35000/1/2/3/4, SCPH-30001/1/2/3/4R
F-chassis:	SCPH-30000, SCPH-30001/1/2/3/4/5/6/7R
G-chassis:	SCPH-39000/1/2/3/4/5/6/7/8,SCPH-37000L,SCPH-37000B
H/I-chassis:	SCPH-50000/1/2/3/4/5/6/7/8/9/10
A-chassis DEX:	DTL-H10000
A-chassis TOOL:	DTL-T10000(H)
B-chassis DEX:	DTL-H30001/2
D-chassis DEX:	DTL-H30000
H-chassis DEX:	DTL-H50000/1/2/6/8/9
```

Supported MD versions:
----------------------
```text
MD1.36 testmode.193 for CXP101064-605R, testmode.194 for CXP101064-602R
MD1.38 testmode.6 for CXP102064-003R
MD1.38 testmode.19 for CXP102064-005R
MD1.39 (CXP103049-xxx F/G-chassis)
MD1.40 (CXR706080-xxx H/I-chassis)
```

ID management (ID_MANAGEMENT build flag)
-----------------------------------------

ID management is an optional feature that must be explicitly enabled at compile time.
When enabled, it adds option 99 (ID management) to the main menu and also unlocks the
following EEPROM management functions (options 4, 5, 14, 15):
  - Erase EEPROM
  - Load defaults (All)
  - Load defaults (ID)    <- also prompts for new Serial, Model ID and EMCS after clearing
  - Load defaults (Model Name)

How to compile for Linux with ID management enabled:

  cd PMAP-unix
  make clean
  make ID_MANAGEMENT=1

The resulting binary will be ./PMAP-unix/pmap.

Without this flag (default build):

  cd PMAP-unix
  make clean
  make

The standard build still provides full EEPROM, ELECT and MECHA adjustment functionality.

Menu 99 - ID management options:
---------------------------------
When compiled with ID_MANAGEMENT=1, option 99 appears in the main menu with the following sub-options:

  1. Write i.Link ID
       Displays the current i.Link ID (8 bytes, hex) and prompts for a new one.
       Format: space-separated hex bytes, e.g.: 00 11 22 33 44 55 66 77

  2. Write console ID
       Displays the current Console ID (8 bytes, hex) and prompts for a new one.
       Same format as i.Link ID.

  3. Write model name  (AB-chassis and later only)
       Displays the current model name and prompts for a new one (max. 16 characters).
       Example: SCPH-30000

  4. Initialize NTSC/PAL defaults  (B-chassis DEX and later only)
       Sets the OSD default video system to NTSC or PAL.
       Use this after replacing the MECHACON IC on a DEX unit.

  5. Initialize MECHACON  (H/I-chassis / Dragon models only)
       Fully initializes the MECHACON for a selected region and model.
       Writes region, date/time stamp (Shimuke), and loads all EEPROM defaults.
       Requires EEP_CS to be in high mode (see EEPROM Reset Mode section above).

  6. Quit

Known bugs and limitations:
---------------------------
1. The i.Link ID and Console ID can be written via option 99 (ID management, requires ID_MANAGEMENT build).
2. The model name can be written via option 99 (ID management, requires ID_MANAGEMENT build).
