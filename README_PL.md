# Program Regulacji Mechaconu PlayStation 2 (PMAP) — 22.12.2015

> 🇬🇧 **English version:** [README.md](README.md)

Program Regulacji Mechaconu PlayStation 2 (PMAP) to narzędzie do serwisowania podsystemu CD/DVD konsoli PlayStation 2.
SONY posiada własne oficjalne narzędzia ułatwiające proces serwisowania swoich konsol. Ten program jest klonem tych narzędzi.

Oferuje on następujące funkcjonalności:
1. Serwisowanie i aktualizacja pamięci EEPROM
2. Regulacja obwodów elektrycznych (ELECT)
3. Regulacja mechaniczna (MECHA / skew)

Funkcje regulacji obwodów elektrycznych i mechaniki oferują pomiar jittera, przy czym pomiar ten jest jedynie wartością szacunkową.
Tylko rzeczywisty sprzęt do pomiaru jittera (jitter meter) może dać dokładny pomiar.

Punkty testowe na płycie głównej PlayStation 2:
-------------------------------------------
```text
A) RS232C:
	TXD (JL610)
	RXD (JL611)
	+3.3V (JL613)
	GND (JL609)
	TEST MODE (Połącz JL617 z GND)
B) Obwód RF-AMP dla miernika jittera DVD:
	RFAC (JL601)
	AGND (JL602, AGND musi być używane razem z RFAC).
C) Inne punkty testowe:
	RFDC		(JL608)
	FE		(JL605)
	TE		(JL607)
	VC_+1.7V	(JL604)
	+8.5V		(JL430, JL431)
	GND		(JL432, JL433)
D) Tryb resetowania EEPROM w modelach Dragon (50k+):
	EEP_CS i EEP_CE przez rezystor R6305
	Jeśli nóżki rezystora R6305 zostaną zwarte,
	linia EEP_CS przejdzie w stan wysoki,
	co uniemożliwi odczyt EEPROM podczas uruchamiania konsoli.
	EEPROM stanie się dostępny dopiero po usunięciu zwarcia.
	Prawidłowa sekwencja:
	- Odłącz wtyczkę zasilania
	- Zewrzyj nóżki rezystora
	- Podłącz wtyczkę zasilania (czerwona dioda się zaświeci)
	- Naciśnij przycisk zasilania (zielona dioda się zaświeci)
	- Rozewrzyj nóżki rezystora
	- Uruchom pmap
	Aby zweryfikować, czy EEPROM nie został odczytany przy starcie, w menu 4 (Show Ident Data) wartość CFC powinna wynosić 00000000.
```

O typach bloków optycznych (OP):
-------------------------------
Istnieją dwa typy bloków optycznych: SONY i SANYO.
Laser SONY posiada oznaczenie SONY, natomiast laser SANYO nie posiada żadnych oznaczeń producenta.
Tylko konsole od chassis F wzwyż mogą obsługiwać laser SANYO.

Jeśli laser zostanie wymieniony na inny typ, konsola musi zostać skonfigurowana do obsługi nowego lasera.

O typach soczewek (Object Lens):
------------------------
Istnieją dwa typy soczewek dla lasera SONY: T487 i T609K.
T609K posiada jasnożółtą osłonę soczewki (pierścień wokół soczewki), natomiast T487 posiada białą osłonę.

Dla chassis D, pierwsze partie KHS-400B z soczewką T609K miały białą osłonę soczewki,
z fioletowym oznaczeniem przy śrubie regulacyjnej z boku podstawy bloku optycznego.
Niektóre soczewki mogą mieć czarne, niebieskie lub zielone oznaczenia, ale to nie oznacza, że jest to nowy typ soczewki.

Nie ma wsparcia dla lasera SANYO z soczewką T609K, więc prawdopodobnie można założyć, że coś takiego nie istnieje.

Jeśli soczewka lub cały laser zostaną wymienione, konsola musi zostać rekonfigurowana do obsługi nowej soczewki.

Układ zegara czasu rzeczywistego (RTC IC):
-----------------------------------------

Na płytach głównych chassis F występują dwa typy układów RTC:
```text
IC405	RS5C384AE2
IC416	BU9861FV-WE2
```

Znaczenie baterii pastylkowej:
---------------------------------

Na płytach głównych chassis F znajduje się nowy Mechacon w obudowie BGA (CXP-103049-xxx).
Bateria musi być obecna, w przeciwnym razie obwód DVD-ROM może nie działać prawidłowo.

Jeśli bateria zostanie wyjęta lub się wyczerpie, dane RTC-ECR w układzie RTC oraz w pamięci EEPROM zostaną wyzerowane (0).
Może to spowodować nieprawidłowe działanie interfejsu i.Link. Dlatego przed przystąpieniem do regulacji zainstaluj sprawną baterię i jej nie wyjmuj.

Zarządzanie pamięcią EEPROM
---------------------------

Konsola PlayStation 2 posiada układ pamięci EEPROM zawierający różne sekcje konfiguracyjne:
1. Disc detect (Wykrywanie płyt)
2. Servo (Serwo)
3. Tilt (Kąt pochylenia - nieużywane w konsolach bez silniczka auto-tilt)
4. PS2ID (nazwa modelu + i.Link ID + Console ID)
5. Tray (Tacka)
6. DVD player (Odtwarzacz DVD)
7. EEGS
8. OSD

To narzędzie pozwala na tworzenie kopii zapasowej i przywracanie pamięci EEPROM (tylko do chassis G), wymazywanie oraz ładowanie ustawień domyślnych.
Można również załadować domyślne ustawienia dla lasera SANYO (chassis F i nowsze), co pozwala na zmianę lasera na SANYO. Domyślne ustawienia MECHACON są przeznaczone dla lasera SONY.

Dostępne jest również aktualizowanie parametrów EEPROM.

Uwaga! Chociaż funkcjonalność ta jest dostępna, nie wymazuj EEPROM ani nie ładuj domyślnych ustawień dla obszaru ID
bez wcześniejszego zaplanowania sposobu przywrócenia tych identyfikatorów! Zobacz sekcję "Zarządzanie ID" poniżej, aby uzyskać instrukcje dotyczące przywracania numeru seryjnego (Serial), Model ID oraz EMCS po załadowaniu domyślnych ustawień ID.

Regulacja obwodów elektrycznych (ELECT)
-----------------------------

Regulacja obwodów elektrycznych odbywa się automatycznie. Wystarczy postępować zgodnie z instrukcjami na ekranie, aby ukończyć procedurę regulacji.
Będziesz potrzebować następujących płyt testowych:
Płyta testowa CD       - SCD-2700 (YEDS-18 dla DTL-T10000)
Płyta testowa DVD-SL   - HX-504
Płyta testowa DVD-DL   - HX-505

W przypadku braku płyt testowych, jako zamienników można użyć zwykłych płyt (CD, DVD-SL i DVD-DL), ale należy włożyć płytę odpowiedniego typu.
Niezastosowanie się do tego wymogu może skutkować nieodwracalnym uszkodzeniem lasera konsoli!
W razie wątpliwości używaj oryginalnych płyt PlayStation 2 w dobrym stanie.

Regulację należy przeprowadzić w przypadku:
1. Wymiany lub demontażu lasera.
2. Wymiany lub demontażu silniczka napędu płyty (spindle motor).
3. Wymiany układu MECHACON.
4. Wymiany, wyczyszczenia lub załadowania ustawień domyślnych do pamięci EEPROM.

Regulacja mechaniczna (skew)
---------------------------

Regulacja mechaniczna składa się z dwóch części:
1. Tangential skew (pochylenie styczne)
2. Radial skew (pochylenie promieniowe - automatyczne dla chassis B)

Należy ją przeprowadzić w przypadku:
1. Wymiany lub demontażu lasera.
2. Wymiany lub demontażu silniczka napędu płyty (spindle motor).

Procedura regulacji mechanicznej (skew):
1. Zdejmij tackę i ustaw mechanizm tacki w pozycji zamkniętej.
2. Umieść płytę (GLD-DR01 lub zwykłą DVD-SL) na silniczku napędu i zabezpiecz ją dociskiem (talerzykiem).
3. Wykonaj inicjalizację za pomocą odpowiedniego polecenia INIT dla danego typu płyty (patrz poniżej).
4. Przesuń czytnik na zewnątrz (SLED OUT). Uwaga! Laser znajduje się teraz na zewnętrznej części płyty!
5. Uruchom odtwarzanie (PLAY 1).
6. Odczytuj poziom jittera (zalecany JITTER 256) podczas dokonywania regulacji.
	Należy powtarzać regulację radial skew i tangential skew aż do uzyskania minimalnej wartości jittera.
6a. Radial skew (pochylenie promieniowe):
	i) Jeśli konsola to chassis B, użyj funkcji regulacji silniczka AUTO TILT:
		*Automatycznie dostosuj radial skew poleceniem ADJ (TILT ADJ).
		*Zatrzymaj odtwarzanie (STOP).
		*Przesuń czytnik do pozycji domowej (SLED HOME) i ponownie uruchom odtwarzanie (PLAY 1).
		*Zapisz nowe ustawienia radial skew (TILT WRITE).
	ii) W przypadku modeli bez silniczka auto-tilt:
		*Reguluj śrubę regulacji radial skew, aż jitter będzie minimalny.
6b. Tangential skew (pochylenie styczne):
	*Przesuń czytnik na zewnątrz (SLED OUT), jeśli wrócił do pozycji domowej.
	*Reguluj śrubę regulacji tangential skew, aż jitter będzie minimalny.
7. Zatrzymaj odtwarzanie (STOP).
8. Przesuń czytnik do pozycji domowej (SLED HOME).
9. Zdejmij płytę i otwórz tackę (TRAY OPEN).
10. Załóż z powrotem tackę i sprawdź, czy wysuwa się i wsuwa prawidłowo.

Pomiar Jittera - tryby INIT i sekwencja:
----------------------------------------------
Przed odczytem jittera należy zainicjalizować napęd z odpowiednim typem płyty za pomocą polecenia INIT.
Różne typy płyt wymagają innych trybów INIT:

  CD (użyj płyty testowej CD SCD-2700 lub zwykłej płyty CD):
    INIT CD
    SLED OUT
    PLAY 1
    JITTER 256

  DVD-SL (użyj płyty testowej DVD HX-504 lub zwykłej płyty jednowarstwowej DVD):
    INIT SKEW        <- używane do regulacji mechanicznej (skew)
    -lub-
    INIT DVD-SL      <- używane do regulacji obwodów elektrycznych (ELECT)
    SLED OUT
    PLAY 1
    JITTER 256

  DVD-DL (użyj płyty testowej DVD HX-505 lub zwykłej płyty dwuwarstwowej DVD):
    INIT DVD-DL
    SLED OUT
    PLAY 1
    JITTER 256       <- odczytywane osobno dla Warstwy 0 (L0) i Warstwy 1 (L1, po PLAY FJ)

Tryby pomiaru jittera:
  JITTER 1   - Pomiar na podstawie pojedynczej próbki (najszybszy, najmniej dokładny)
  JITTER 16  - Średnia z 16 próbek (średnia dokładność)
  JITTER 256 - Średnia z 256 próbek (najwolniejszy, najbardziej dokładny - zalecany do regulacji)

Uwaga: Komenda JITTER odczytuje surową wartość szesnastkową z MECHACONu. Im niższa wartość, tym lepiej.
Docelowe wartości są wymienione w sekcji "Wartości progowe/docelowe" poniżej.

Wartości progowe/docelowe regulacji:
------------------------------
```text
CD:
	a.	CD DET:				SONY OP 600-1600, SANYO OP 750-1800 / SONY OP: 660-1760, SANYO OP: 825-1980
	b.	Wzmocnienie pętli FE (K13):	0x10 - 0x60
	c.	Wzmocnienie pętli TE (K23):	0x10 - 0x60
DVD-SL:
	a.	Wzmocnienie pętli FE (K13):	0x10 - 0x60
	b.	Wzmocnienie pętli TE (K23):	0x10 - 0x60
	c.	Jitter (256):			0 - 0x1B00 (DEX: 0x1000, T10000: 0x1400) / chassis G/H/I: 0x3E00 (DEX: 0x2970)
	d.	Współczynnik błędów (PI+PO CC): 0 - 100
	e.	Współczynnik błędów (PI NCC):    0
DVD-DL:
	a.	DVD-DL DISC DETECT:		0x15 (DVD-DL)
	a.	(L0) Wzmocnienie pętli FE (K13):0x10 - 0x60
	b.	(L0) Wzmocnienie pętli TE (K23):0x10 - 0x60
	c.	(L0) Jitter (256):		0 - 0x2300 (DEX: 0x1200) / chassis G/H/I: 0 - 0x4C00 (DEX: 0x2D00)
	d.	(L1) Wzmocnienie pętli FE (K13):0x10 - 0x60
	e.	(L1) Wzmocnienie pętli TE (K23):0x10 - 0x60
	f.	(L1) Jitter (256):		0 - 0x2300 (DEX: 0x1200) / chassis G/H/I: 0 - 0x4C00 (DEX: 0x2D00)
Stosunek Disc Detect CD/DVD:			>= 1.80 / chassis G/H/I: >=1.73
Suma kontrolna EEPROM:				0
```

Obsługiwane modele chassis:
-------------------------
```text
Chassis A:	SCPH-10000/SCPH-15000
Chassis B:	SCPH-30001
Chassis C:	SCPH-30001/2/3/4
Chassis D:	SCPH-30000/1/2/3/4, SCPH-35000/1/2/3/4, SCPH-30001/1/2/3/4R
Chassis F:	SCPH-30000, SCPH-30001/1/2/3/4/5/6/7R
Chassis G:	SCPH-39000/1/2/3/4/5/6/7/8, SCPH-37000L, SCPH-37000B
Chassis H/I:	SCPH-50000/1/2/3/4/5/6/7/8/9/10
Chassis A DEX:	DTL-H10000
Chassis A TOOL:	DTL-T10000(H)
Chassis B DEX:	DTL-H30001/2
Chassis D DEX:	DTL-H30000
Chassis H DEX:	DTL-H50000/1/2/6/8/9
```

Obsługiwane wersje MD:
----------------------
```text
MD1.36 testmode.193 dla CXP101064-605R, testmode.194 dla CXP101064-602R
MD1.38 testmode.6 dla CXP102064-003R
MD1.38 testmode.19 dla CXP102064-005R
MD1.39 (CXP103049-xxx F/G-chassis)
MD1.40 (CXR706080-xxx H/I-chassis)
```

Zarządzanie ID (flaga kompilacji ID_MANAGEMENT)
-----------------------------------------------

Zarządzanie ID to opcjonalna funkcja, którą należy jawnie włączyć podczas kompilacji.
Po włączeniu dodaje ona opcję 99 (ID management) do menu głównego, a także odblokowuje następujące funkcje zarządzania EEPROM (opcje 4, 5, 14, 15):
  - Erase EEPROM (Wymazywanie EEPROM)
  - Load defaults (All) (Ładowanie domyślnych dla wszystkich sekcji)
  - Load defaults (ID)  (Ładowanie domyślnych dla ID) <- po wyczyszczeniu pyta o nowy Serial, Model ID i EMCS
  - Load defaults (Model Name) (Ładowanie domyślnej nazwy modelu)

Jak skompilować dla Linuksa z włączoną obsługą zarządzania ID:

  cd PMAP-unix
  make clean
  make ID_MANAGEMENT=1

Wynikowy plik binarny będzie znajdować się w `./PMAP-unix/pmap`.

Bez tej flagi (standardowa kompilacja):

  cd PMAP-unix
  make clean
  make

Standardowa kompilacja nadal zapewnia pełną funkcjonalność regulacji EEPROM, ELECT i MECHA.

Menu 99 - opcje zarządzania ID:
---------------------------------
Po skompilowaniu z `ID_MANAGEMENT=1`, w menu głównym pojawi się opcja 99 z następującymi podopcjami:

  1. Write i.Link ID
       Wyświetla bieżący i.Link ID (8 bajtów, hex) i pozwala na wpisanie nowego.
       Format: bajty hex oddzielone spacjami, np.: 00 11 22 33 44 55 66 77

  2. Write console ID
       Wyświetla bieżący Console ID (8 bajtów, hex) i pozwala na wpisanie nowego.
       Format taki sam jak dla i.Link ID.

  3. Write model name (tylko chassis AB i nowsze)
       Wyświetla bieżącą nazwę modelu i pozwala na wpisanie nowej (maks. 16 znaków).
       Przykład: SCPH-30000

  4. Initialize NTSC/PAL defaults (tylko DEX od chassis B i nowsze)
       Ustawia domyślny system wideo OSD na NTSC lub PAL.
       Użyj tej opcji po wymianie układu MECHACON w jednostce DEX.

  5. Initialize MECHACON (tylko modele H/I-chassis / Dragon)
       W pełni inicjalizuje MECHACON dla wybranego regionu i modelu.
       Zapisuje region, znacznik daty/czasu (Shimuke) oraz ładuje wszystkie ustawienia domyślne EEPROM.
       Wymaga ustawienia EEP_CS w stan wysoki (patrz sekcja "Tryb resetowania EEPROM" powyżej).

  6. Quit (Wyjście)

Znane błędy i ograniczenia:
---------------------------
1. i.Link ID i Console ID mogą być zapisywane za pomocą opcji 99 (zarządzanie ID, wymaga kompilacji z ID_MANAGEMENT).
2. Nazwa modelu może być zapisana za pomocą opcji 99 (zarządzanie ID, wymaga kompilacji z ID_MANAGEMENT).
