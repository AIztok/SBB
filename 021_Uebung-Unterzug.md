# 2. Übung - Unterzug

![Unterzug.png](/docs/assets/images/Unterzug.png)

Der komplette SOFiSTIK Code der unten Abschnittsweise gegeben ist, kann komplett im .dat Format [hier](https://aiztok.github.io/SBB/docs/FH_SBB_Unterzug_v01.dat) heruntergeladen werden.
## Inhalt
[Erstellen des Projekts](#erstellen-des-projekts)

[Projektinfo](#projektinfo)

[Materialangabe](#materialangabe)

[Querschnittsdefinition](#querschnittsdefinition)

[Modelleingabe](#modelleingabe)

[Definition der Einwirkungen](#definition-der-einwirkungen)

[Definition der Lasten](#definition-der-lasten)

[Berechnung](#berechnung)

[Was passiert im Hintergrund beim Plattenbalken?](#was-passiert-im-hintergrund-beim-plattenbalken?)

[Überlagerung der Ergebnisse - Erstellung der Einwirkungskombinationen](#überlagerung-der-ergebnisse)

[Bemessung der Stabelemente](#bemessung-der-stabelemente)

[Ergebnisausgabe](#ergebnisausgabe)

## Erstellen des Projekts
Das neue Projekt wird anhand einer Anwendervorlage erstellt.
Die Anwendervorlage kann [hier](https://aiztok.github.io/SBB/docs/FH_SBB_SSD-Vorlage_V01.sofistix) heruntergeladen werden.
Somit ist die Norm und andere Systeminformationen (2D oder 3D, Z-Richtung, Gruppendivisor) bereits vor eingestellt. 

## Materialangabe
Die Materialangabe kann von [[011_Einfuehrungsbeispiel]] übernommen werden, jedoch werden diesmal nur die Materiale genommen die auch tatsächlich Anwendung finden.
Es wird auch ein Verbundquerschnitt (Stahl-Beton-Verbund) später erstellt, deswegen wird noch ein zusätzlicher Beton definiert - wäre für dieses Beispiel nicht notwendig, aber es ist sinnvoll zu trennen (würde man Bauphasen berücksichtigen und verschiedene Einbauzeiten).

```
+prog aqua
head 'Materialangabe'
$----------------------------------------------------------------------------------
!*! Angabe der Norm
NORM OEN en199X-200X unit 0 !
$----------------------------------------------------------------------------------
!*! Umfang der Ausgabe
echo full extr
$----------------------------------------------------------------------------------
!*! Beton
CONC NO 1  TYPE c 30N           titl 'Beton C30/37 Normalerhärtend'
CONC NO 2  TYPE c 30N           titl 'Beton C30/37 Verbund-QS'
!
$----------------------------------------------------------------------------------
!*! Bewehrung
STEE NO 101 TYPE b 550b titl 'Bewehrung B550B'
$----------------------------------------------------------------------------------
!*! Konstruktionsstahl
STEE NO 301 TYPE S 355 titl 'S355'

end   
```
## Querschnittsdefinition

```
+prog aqua
head 'Definition des Querschnitts'
page unii 0 unio 0
ctrl rest 2 ! Steuerung, dass die Angaben des vorherigen AQUA nicht überschrieben werden

$----------------------------------------------------------------------------------
!*! Querschnitt Plattenbalken - einfach
SREC 1 H 0.60 B 0.3 HO 0.28 BO 3.7 MNO 1 MRF 101 SO 0.04[m] titl 'T-beam simple'

$----------------------------------------------------------------------------------
!*! Querschnitt Plattenbalken - mit polygon punkten
SECT NO  2  MNO  1 MRF 101 titl 'T-beam Poly' ! QS-Definition über Polygonpunkte

let#h  0.60[m] ! Lokale Variable für die Querschnittshöhe
let#b  0.30[m] ! Lokale Variable für die Stegbreite
let#ho 0.28[m] ! Obergurtdicke
let#bo 3.70[m] ! Lokale Variable für die Obergurtbreite

!*! Spannungspunkte - wo wir die Spannungen ermitteln wollen
$ Oberkante
SPT OKL  Y -#bo/2 Z  0 MNO 1
SPT OKM  Y  0.00  Z  0 MNO 1
SPT OKR  Y +#bo/2 Z  0 MNO 1
$ Unterkante
SPT UKL  Y -#b/2  Z #H MNO 1
SPT UK   Y  0.00  Z #H MNO 1
SPT UKR  Y +#b/2  Z #H MNO 1

POLY TYPE O   MNO  1
    VERT   1  Y    0     Z   0       REFP OKL
    VERT   2  Y    0     Z   0       REFP OKM
    VERT   3  Y    0     Z   0       REFP OKR
    VERT   4  Y    0     Z   #ho     REFP OKR
    VERT   5  Y  #b/2    Z   #ho     REFP OKM
    VERT  11  Y    0     Z   0       REFP UKR
    VERT  12  Y    0     Z   0       REFP UK
    VERT  13  Y    0     Z   0       REFP UKL
    VERT  15  Y -#b/2    Z   #ho     REFP OKM
    VERT  14  Y    0     Z   #ho     REFP OKL

! Schubschnitte
CUT 'Csc' ZB 'S' ! Schubschnitt durch den Schwerpunkt des QS, z.B.
CUT 'Steg' ZB #ho+0.01 ! Schubschnitt durch den Schwerpunkt des QS, z.B.

$----------------------------------------------------------------------------------
!*! Verbundquerschnitt
SECT NO 3 MNO 301 TITL 'SBV QS'
PROF NO TYPE     Z1    ZM          DTYP    REF  MNO=301
     1  heb     500    280[mm]     S       UM
POLY O MNO 2
VERT NO   Y           Z         TYPE
     1    3700/2[mm]  0[mm]     O
     2   -3700/2[mm]  0[mm]     O
     3   -3700/2[mm]  280[mm]   O
     4    3700/2[mm]  280[mm]   O
! Schubschnitt zur Bemessung der Verbunddübel
! weil mit Dickwandigen-QS gearbeitet, muss der Schubschnitt ca. um die Verbunddübel herum geführt werden
CUT 1 YB +100[mm] ZB 280[mm] YE +100[mm] ZE 125[mm]  MNO 2 MRF 101
    1 YB +100[mm] ZB 125[mm] YE -100[mm] ZE 125[mm]  MNO 2
    1 YB -100[mm] ZB 125[mm] YE -100[mm] ZE 280[mm]  MNO 2 MRF 101
      
end 
```
## Modelleingabe
In folgenden wird das Modell über die eingaben von:
- Strukturpunkte (SPT)
- Strukturlinien (SLN): verbinden die SPTs
- Strukturflächen (SAR): Umrandung definiert durch SPTs oder SLN 

```
+prog sofimshc
head 'Model'
page unii 0 unio 0 ! Input and Output units

$----------------------------------------------------------------------------------
!*!Label FE-Netz
ctrl hmin 1.0      ! Mesh size in [m]
ctrl mesh 1        ! Mesh type (1 = beams, 2 = shells, 3 = volume)

$----------------------------------------------------------------------------------
!*!Label System
syst 3D gdiv 1000 gdir posz ! wir wählen in 3D Modell, weil wir auch die Torsion im Träger berücksichtigen (exzentrische Belastung zur Trägerachse)

$----------------------------------------------------------------------------------
!*!Label Punkte
! Trägeranfang
spt  1 x   0.0      y  0 z 0 fix pp sx 1 0 0   ! Auflager Träger
! Trägerende
spt  2 x  10.0      y  0 z 0 ref pt nref 1 fix xp  ! Auflager Träger
! Randpunkte rechts
spt 11 x   0.0      y  7 z 0 ref pt nref 1
spt 12 x   0.0      y  7 z 0 ref pt nref 2
! Randpunkte links
spt 21 x   0.0      y -7 z 0 ref pt nref 1
spt 22 x   0.0      y -7 z 0 ref pt nref 2

$----------------------------------------------------------------------------------
!*! Linien - Träger
! Simple QS-Definition Plattenbalken
sln 1  1  2 sno 1 grp 1 styp 'b.e' ! Strukturlinie No.1 von Punkt 9 bis 10, Querschnittnummer 1, gruppennummer 1.
! Plattenbalken Polygonal
!sln 1  1  2 sno 1 grp 1 styp 'b.e' ! Strukturlinie No.1 von Punkt 9 bis 10, Querschnittnummer 1, gruppennummer 1.
! Verbundquerschnitt
!sln 1  1  2 sno 1 grp 1 styp 'b.e' ! Strukturlinie No.1 von Punkt 9 bis 10, Querschnittnummer 1, gruppennummer 1.
!*! Randlinien - Platte
sln  2  1 11
sln  3 11 12  fix pz ! mit Auflager in z-Richtung
sln  4 12  2

sln 12  1 21
sln 13 21 22  fix pz ! mit Auflager in z-Richtung
sln 14 22  2

$----------------------------------------------------------------------------------
!*! Flächendefinition
sar 1 grp 10 mno 1 mrf 101 t 0.28[m] mctl regm qref cent titl 'Decke'
sarb out nl 1,2,3,4

sar 2 grp 10 mno 1 mrf 101 t 0.28[m] mctl regm qref cent titl 'Decke'
sarb out na  1 ne  2
sarb out na  2 ne 22
sarb out na 22 ne 21
sarb out na 21 ne  1

end  
```
## Definition der Einwirkungen
Übernehmen wir von dem Einführungsbeispiel (Wind kann entfernt werden, da nicht verwendet):
```
+prog sofiload
head 'Definition der Einwirkungen'
echo full extr
!Ständige Lasten
    ACT G   
    ACT G_1 
    ACT G_2 
! Nutzlast
    ACT Q   
! Schneelast
    ACT S  
end  
```

## Definition der Lasten
Es werden die Flächenlasten mit drei verschiedenen Methoden definiert:

```
+prog sofiload
head 'Definition der Lasten'

!*!Label EGW
lc 1 facd 1.0 type G_1 titl 'EGW'

!*!Label Ständige
lc 2 type G_2 titl 'Ausbaulasten'
    ! Flächenlast kann über Bezug auf Gruppennummer definiert werden
    quad grp 10 type pg p 2.5

!*!Label Nutzlasten
! wir erstellen eine Schleife um zwei getrennte Lastfälle für das linke bzw. rechte Feld zu erstellen
loop#i 2
    lc 10+#i type Q titl 'Nutzlast'
    ! oder über Bezug auf Flächennummer
    area sar #i+1 type pg p1 3
endloop


!*!Label Schneelast
lc 20 type S titl 'Schneelast'
    ! und die dritte Möglichkeit ist über Koordinaten, z.B. eine Trapezförmige Last
    area sar type pg $$
        p1 3 x1  0 y1  7 z1 0 $$
        p2 1 x2 10 y2  7 z2 0 $$
        p3 2 x3 10 y3 -7 z3 0 $$
        p4 0 x4  0 y4 -7 z4 0
    ! $$ steht für umbruch der Zeile obwohl gelesen vom Programm als eine

end   
```

## Berechnung
Bei der Berechnung ist es wesentlich die Option für Plattenbalken (T-Beam auf Englisch) zu aktivieren mittels `TBEX`.
Dadurch wird die Software den Plattenbalken automatisch erkennen und entsprechend berücksichtigen:
- es wird nicht die doppelte Steifigkeit berücksichtigt (von der Stabsteifigkeit wird die Steifigkeit der Plattenelemente abgezogen),
- es wird nicht das doppelte Eigengewicht berücksichtigt,
- die Ergebnisse des Stabes sind tatsächlich die Schnittgrößen des Stabes plus die Ergebnisse der Plattenelemente (Knotenwert am Anschluss) multipliziert mit der Mitwirkendenbreite.

```
+prog ase
head Berechnung
echo full extr
! Lineare Berechnung
syst prob line

! Einschalten der Plattenbalkenfunktion
TBEX AUTO

! Alle Gruppen sind eingeschaltet
grp -
! Alle Lastfälle werden berechnet
lc all

end
```

## Was passiert im Hintergrund beim Plattenbalken?

Um zu verstehen was die Software mit der Definition `TBEX` berechnet, wird der Stab 1006 (Stabanfang x=0 liegt in Feldmitte) betrachtet.
Es ist wichtig zu betonen, das diese einfachste Ansatz zur Berücksichtigung der mitwirkenden Plattenbreite unter der folgenden Annahme funktioniert:
- der Schwerpunkt des Stabelements und des Plattenelements müssen in der gleichen Ebene liegen
Somit wird sichergestellt, dass sich keine Zug- und Druckkomponente zum Abtrag der Last im Stab- bzw. Plattenelementen bildet.

Die Software macht die folgenden Schritte:
- Ermittlung der Biegesteifigkeit (und Schubsteifigkeit) der Plattenelemente in der *Beff* Breite (Definition von `BO` in `SREC` im Modul `AQUA` )
- die ermittelte Biegesteifigkeit wird von der Steifigkeit des Stabes abgezogen, damit im Modell nicht die doppelte Steifigkeit (es wird auch das Gewicht der überschneidenden Flächen abgezogen im Lastfall EGW)
- mit der FE Berechnung werden Schnittgrößen im Stab und in den Plattenelementen ermittelt
- die Schnittgrößen *mxx* und *vx* (wenn das lokale Koordinaten System x in der Richtung des Stabes) werden über die *Beff* Breite summiert und den Schnittgrößen des Stabes hinzugerechnet
- die Bemessung des Plattenbalkenquerschnitts erfolgt danach mit diesen summierten Schnittgrößen

Nach der Berechnung mit ASE, können die Ergebnisse tabellarisch im `Report Browser` angesehen werden:

![021_Report_browser.png](/docs/assets/images/021_Report_browser.png)

Unter:
- Stabschnittgrößen Lastfall 1 EGW
- Stabschnittgrößen ohne PLABA Plattenbalkenanteil
- Statistik Stab- Zusatzschnittgrößen Plattenbalken
können die einzelnen Anteile geprüft werden.

Stabschnittgrößen Lastfall 1 EGW:

 | Grp | Nummer |      x    |    N   |    Vy   |    Vz     |   Mt     |   My    |    Mz|
 |---|---|---|---|---|---|---|---|---|
 | | | [m]   |  [kN]  |   [kN]  |   [kN]  |   [kNm]   |  [kNm] |    [kNm] |
 |  1    |1006 |  0.00    |  0.00  |   0.00 |   -4.49   |   0.00 |  342.71 |     0.00|

Stabschnittgrößen ohne PLABA Plattenbalkenanteil:

 | Grp | Nummer |      x    |    N   |    Vy   |    Vz     |   Mt     |   My    |    Mz|
 |---|---|---|---|---|---|---|---|---|
 | | | [m]   |  [kN]  |   [kN]  |   [kN]  |   [kNm]   |  [kNm] |    [kNm] |
 |  1    |1006 |  0.000    |  0.00  |   0.00 |   -3.12   |   0.00 |  193.48 |     0.00|

Statistik Stab- Zusatzschnittgrößen Plattenbalken:

 | Qnr | bm |     N   |    Vz     |   My   |    N   |    Vz     |   My   |
 |---|---|---|---|---|---|---|---|
 | | | [m]   |  [kN]  |   [kN]  |   [kN]  |   [kNm]   |  [kNm] |    [kNm] |
 |  1    |3.70 |  0.000    |  126.11  |   342.71 |   0.00   |   25.77 |  149.23 |

Summiert = Stab ohne PLABA + Plattenanteil
```math
342.7 kNm = 193.5 kNm + 149.2 kNm
```

## Überlagerung der Ergebnisse
Wir werden nur die GZT Einwirkungskombination erstellen. Es wäre aber natürlich möglich, wie im Einführungsbeispiel, alle Einwirkungskombinationen im gleichen Sinne zu ermitteln.
Wir schalten diesmal auch die `QUAD` Ergebnisse ein.

```code
! Block definition der Ergebnisse die überlagert werden sollen
! mit #define / #enddef können text blöcke definiert werden, die immer wieder verwendet werden
! in diesem Fall wählen wir aus einer List aus, für welche Ergebnisse wollen wir, dass die Software
! eine Ergebniskombination erstellt / ermittelt.

#define results
$      'spri' P   #lf+1
$      'spri' M   #lf+3

      'NODE' PX  #lf+1
      'NODE' PY  #lf+3
      'NODE' PZ  #lf+5
$      'NODE' MX  #lf+7
$      'NODE' MY  #lf+9
      'NODE' MZ  #lf+11
      'NODE' UX   #lf+13
      'NODE' UY   #lf+15
      'NODE' UZ   #lf+17
      'NODE' URX  #lf+19
      'NODE' URY  #lf+21
      'NODE' URZ  #lf+23

      'beam' N   #lf+1
      'beam' VY  #lf+3
      'beam' VZ  #lf+5
      'beam' MT  #lf+7
      'beam' MY  #lf+9
      'beam' MZ  #lf+11
$      'BEAM' PTY #lf+13
$      'BEAM' PTZ #lf+15

      'QUAD' mxx #lf+1
      'QUAD' myy #lf+3
      'QUAD' mxy #lf+5
      'QUAD' vx  #lf+7
      'QUAD' vy  #lf+9
      'QUAD' nxx #lf+11
      'QUAD' nyy #lf+13
      'QUAD' nxy #lf+15

$      'QBED' p   #lf+1
$      'QBED' pt  #lf+3
#enddef
#include results 

+prog maxima
head 'Ergebniskombination'
! Output control
echo chck val full

COMB 1 rare BASE - $ kombination charakteristisch
    ACT G_1 ! EGW
    ACT G_2 ! Ständige Lasten
    ACT Q ! Nutzlast
    ACT S ! Schnee

let#lf 1120
SUPP ETYP TYPE   LC   FROM  TITL EXTR=MAMI COMB=1

COMB 4 perm BASE - $ kombination quasi-ständig
    ACT G_1 ! EGW
    ACT G_2 ! Ständige Lasten
    ACT Q ! Nutzlast
    ACT S ! Schnee

let#lf 1420
SUPP ETYP TYPE   LC   FROM  TITL EXTR=MAMI COMB=4
#include results

COMB 10 desi BASE -  $ kombination GZT
    ACT G_1 ! EGW
    ACT G_2 ! Ständige Lasten
    ACT Q ! Nutzlast
    ACT S ! Schnee

let#lf 2120
SUPP ETYP TYPE   LC   FROM  TITL EXTR=MAMI COMB=10
#include results

end   
```

## Bemessung der Stabelemente
Die Eingabe ist gleich wie beim Einführungsbeispiel 

```code
+prog aqb
head 'GZT Bemessung Stäbe'
echo full extr ! Komplette textliche Ausgabe der Ergebnisse
page lano 0

! Wichtige Eingabe REIN
! Mit MOD wird gesteuert ob die ermittelte Bewehrung
! nur im Bemessungsschnitt eingelegt wird ("sect")
! oder über den gesamte Stabdefinition ("beam")
! Mit RMOD wird gesteurt ob diese Bewehrung akkumuliert wird ("accu")
! und in welchen Speicherfall LCR wird die gespeichert.
rein mod sect rmod accu lcr 1

beam - ! wir können auswählen welche Gruppe soll bemessen werden

! Auswahl der Lastfälle für die Bemessung
$lc 21??
lc (d) ! alle gespeicherten Lastfälle

desi ulti ksv uld ksb uld

end

+prog aqb urs:13
head 'GZG Bemessung Stäbe'
echo full extr
page lano 0

rein mod sect rmod accu lcr 1 ! wir nehmen die Ergebnisse aus GZT und falls mehr Bew. erforderlich (Rissbreite) wird es akkumuliert.

beam - ! wir können auswählen welche Gruppe soll bemessen werden

! Auswahl der Lastfälle für die Bemessung
$lc 14??
lc (p) ! alle gespeicherten Lastfälle

nstr serv ksb sl ksv sl crac yes cw 0.3 ! Rissbreitennachweis

end
```
## Bemessung der Flächenelemente
Die Flächenelemente werden mit dem Modul `BEMESS` bemessen, hier unterteilen wir die einzelnen Definitionen in mehrere `BEMESS` Module:
- Definition der geometrischen Parameter (Lage der Bewehrung) und der Bemessungsparameter (Rissbreite, Ausgangsbewehrung)
- Bemessung im Grenzzustand der Tragfähigkeit
- Bemessung im Grenzzustand der Gebrauchstauglichkeit - Rissweite

Definition der Parameter:
```code
+prog bemess
head 'Definition der Bewehrungsparameter'
echo full extr

! Definition der Abstände der Bewehrungslagen
! ha/hb ... Abstand von Bauteilrand zur oberen/unteren Hauptbewehrung
! dha/dhb ... Abstand zwischen der oberen/unteren Querbewehrung zu Hauptbewehrung
geom - ha 25+10/2 dha 10 hb 25+10/2 dhb 10

! Winkel der Bewehrung zu der lokalen Achse der Platten
dire upp 0 low 0

! Eingabe der Parameter für die Berechnung
! du ... [mm] Durchmesser der Bewehrung - Eingangsparameter für die Risbreitenberechnung
! wku/wkl ... [mm] Rissweite
! asu/as
! Beispiel AQ 50 matte (DM5 alle 10cm in beiden Richtungen)
para - du 10 wku 0.3 wkl 0.3 $$
             asu 0.2*100/10 $$
             asu2 0.2*100/10 $$
             asl2 0.2*100/10 $$
             asl 0.2*100/10
! mit doppeldollar-Zeichen $$ kann einie Zeile in mehr Zeilen unterteilt werden (bessere Übersicht),
! wird aber als eine von Programm gelesen

end   
```

GZT Bemessung:
```code
+PROG BEMESS
HEAD 'GZT Bemessung'

ECHO full extr ! komplette Ausgabe im Bericht

! Steuerung das GZT (ULTI) Bemessung durchzuführen ist
! Bewehrung wird gespeicher in den Bewehrungsspeicherlastfall 1
CTRL ULTI RMOD SAVE  LCR 1  LCRI 0

grp 10 ! Welche Gruppe der Flächenelemente zu bemessen ist

! Eingabe der Lastfälle / Ergebniskombinationen
lc (d) ! alle als GZT gespeicherten Lastfälle
!LC (2121 2136 1) ! explizite Auswahl der LFs

END  
```

GZG Bemessung:
```code
+prog bemess
head 'SLS Rissbreitenachweis'
ECHO full extr ! komplette Ausgabe im Bericht

! Es soll GZG Nachweis der Rissbreite geführt werden
! Die Ergebnisse der Bemessung sind in Bewehungsspeicherlastfall 2 zu speichern
! Ausgangs Bewehrung ist die Bewehrung aus Bewehrungspeicherlastfall 1
! und die soll überlagert werden
CTRL crac RMOD supe LCR 2 LCRI 1

CRAC WK PARA ! Rissweite soll von vorherigen Bemess, PARA übernommen werden

grp 10 ! Welche Gruppe der Flächenelemente zu bemessen ist

! Eingabe der Lastfälle / Ergebniskombinationen
lc (p) ! alle als GZG Quasi-ständig gespeicherten Lastfälle
!LC (1421 1436 1) ! explizite Auswahl der LFs


end   
```
## Ergebnisausgabe

Ergebnisse Stabelemente - hier übernehmen wir praktisch alles aus dem Einführungsbeispiel, passen nur die Lastfallnummern an:
```code
+PROG WING urs:14
HEAD 'Stabelemente'

PAGE LANO 0                       ! Ausgabe in Deutsch
SIZE 4 SC 100 FORM URS SPLI '1*1' ! A4 Blatt Vertikal, Maßstab 1:50, 3 Abbildungen über die Blatthöhe

CTRL MEAS 2D

LET#lf 1,10,11,20  ! wir definieren eine lokale Variable (bzw. Vektor mit 4 Werten), und zwar die Lastfallnummern

! In Sofistik können Schleifen definiert werden, anbei eine einfache Schleife, die von dem Vektor "#lf" die Anzahl der einträge übernimmt (4, also die Schleife läuft 4x)
! und der interne Zähler der Schleife ist die Variable "#i" mit der wir in jedem Schleifenlauf den nächsten Wert des Vektors "#lf" lesen
! Achtung: LET ist eine lokale Variable die nur in diesem +prog WING modul gespeichert ist
! STO wäre eine globale Variable, die über die ganze Berechnung in allen Modulen zu verfügung stehen würde.
! Es ist sinnvoll darauf zu achten die STO nur dann zu verwenden, wenn man diese Funktion benötigt und sonst mit LET zu arbeiten.

loop#i lf
    ! Biegemoment My, Schriftgröße 0.3cm
    LC   NO #lf(#i) ; BEAM TYPE MY UNIT DEFA SCHH 0.3 STYP BEAM FILL NO REPR DLIN
    ! Querkraft Vz, Schriftgröße 0.3cm
    LC   NO #lf(#i) ; BEAM TYPE Vz UNIT DEFA SCHH 0.3 STYP BEAM FILL NO REPR DLIN
    ! Normalkraft N
    LC   NO #lf(#i) ; BEAM TYPE N UNIT DEFA SCHH 0.3 STYP BEAM FILL NO REPR DLIN
endloop

! mit einer neuen Definition SIZE, wird sichergestellt, das die folgenden Ergebnisse am neuen Blatt gedruckt werden
SIZE 4 SC 100 FORM URS SPLI '1*1' ! A4 Blatt Vertikal, Maßstab 1:50, 3 Abbildungen über die Blatthöhe

! Wir können aber natürlich auch einzeln die Ergebnisse darstellen, z.B. für GZT

! Biegemomente in separaten Bildern
! max Biegemoment My, Schriftgröße 0.3cm
LC   NO 2129 ; BEAM TYPE MY UNIT DEFA SCHH 0.3 STYP BEAM FILL NO REPR DLIN
! min Biegemoment My
LC   NO 2130 ; BEAM TYPE MY UNIT DEFA SCHH 0.3 STYP BEAM FILL NO REPR DLIN

! Oder mit der AND funktion überlagert am Blatt
! max Biegemoment My, Schriftgröße 0.3cm
LC   NO 2129 ; BEAM TYPE MY UNIT DEFA SCHH 0.3 STYP BEAM FILL NO REPR DLIN
and
! min Biegemoment My
LC   NO 2130 ; BEAM TYPE MY UNIT DEFA SCHH 0.3 STYP BEAM FILL NO REPR DLIN


HEAD 'Auflagerkräfte'

PAGE LANO 0
SIZE 4 SC 100 FORM URS SPLI '1*1'

! Auflagerkraft in X Richtung global
LC   NO 2125 ; NODE TYPE SZ UNIT DEFA SCHH 0.3 FILL NO
and
LC   NO 2126 ; NODE TYPE SZ UNIT DEFA SCHH 0.3 FILL NO


HEAD 'Verschiebungen und Verdrehungen'

PAGE LANO 0
SIZE 4 SC 100 FORM URS SPLI '1*1'

! Verschiebungen GZG Quas-Ständig in Z Richtung
LC   NO 1420+17 ; NODE TYPE UZ UNIT DEFA SCHH 0.3 FILL NO
LC   NO 1420+18 ; NODE TYPE UZ UNIT DEFA SCHH 0.3 FILL NO

end     
```

Ergebnisse Bemessung Stäbe:
```code
+prog wing
HEAD 'Bemessung Stäbe'
UNIT 0 SET IN
SIZE -4 SC 75 FORM URS SPLI '3*1'

COL2 C5 -2071 C25 -3017 -3017 ! Auflagerdarstellung ausgeschaltet

VIEW TYPE DIRE X 0 Y -1 Z 0 AXIS POSZ ROTA 0
! Untere Bewehrung
LC   DESI 1 ; BEAM TYPE AS1 UNIT DEFA SCHH 0.3 STYP BEAM FILL NO REPR DLIN
! Obere Bewehrung
LC   DESI 1 ; BEAM TYPE AS2 UNIT DEFA SCHH 0.3 STYP BEAM FILL NO REPR DLIN
! Schubbewehrung
LC   DESI 1 ; BEAM TYPE ASU1 UNIT DEFA SCHH 0.3 STYP BEAM FILL NO REPR DLIN

END  
```

Ergebnisse Flächenelemente:
```code
+PROG WING urs:10
HEAD 'Ergebnisse Flächenelemente'
ctrl meas 2d ! Steuerung, dass alle Ergebnisse für 2D Darstellung angepasst werden

SIZE TYPE -URS SC 100 SPLI '1*1'

COL2 C5 -2071 C25 -3017 -3017 ! Auflagerdarstellung ausgeschaltet

! Blick von oben (Z = 1 -> Normal zur unserer Ebene)
VIEW TYPE DIRE X 0 Y 0 Z 1 AXIS POSY ROTA 0

grp 10 ! Auswahl welche Elementgruppe dargestellt werden soll

! Definition für die Schriftgröße der Ergebnisse
let#sch 0.3

! Definition für den zusätzlichen Text am Bild mit MOVE
! Schriftgröße SCHH 0.5 cm
!
#define text_prop
        move del; move 1. 92. unit pr alig left schh 0.5 cb 0 c 1001; move del;
#enddef

!*! Eigengewicht
        #include text_prop
        move del; text 'Plattenschnittgr./ EGW / Mxx '
        LC   NO 1 ; QUAD TYPE MX UNIT defa SCHH #sch STYP E2N REPR DISO

        #include text_prop
        move del; text 'Plattenschnittgr./ EGW / Myy '
        LC   NO 1 ; QUAD TYPE MY UNIT defa SCHH #sch STYP E2N REPR DISO

        #include text_prop
        move del; text 'Plattenschnittgr./ EGW / sqr(Vx^2+Vy^2) '
        LC   NO 1 ; QUAD TYPE Vr UNIT defa SCHH #sch STYP E2N REPR DISO

        #include text_prop
        move del; text 'PLattenschnittgr./ EGW / Nx '
        LC   NO 1 ; QUAD TYPE NX UNIT defa SCHH #sch STYP E2N REPR DISO

!*! GZT
SIZE TYPE URS SC 150 SPLI '1*2'
        #include text_prop
        move del; text 'Plattenschnittgr./ GZT / max Mxx '
        LC   NO 2121 ; QUAD TYPE MX UNIT defa SCHH #sch STYP E2N REPR DISO
        #include text_prop
        move del; text 'Plattenschnittgr./ GZT / min Mxx '
        LC   NO 2122 ; QUAD TYPE MX UNIT defa SCHH #sch STYP E2N REPR DISO

        #include text_prop
        move del; text 'Plattenschnittgr./ GZT / max Myy '
        LC   NO 2123 ; QUAD TYPE MY UNIT defa SCHH #sch STYP E2N REPR DISO
        #include text_prop
        move del; text 'Plattenschnittgr./ GZT / min Myy '
        LC   NO 2124 ; QUAD TYPE MY UNIT defa SCHH #sch STYP E2N REPR DISO

        #include text_prop
        move del; text 'Plattenschnittgr./ GZT / sqr(Vx^2+Vy^2) '
        LC   NO 2127 ; QUAD TYPE Vr UNIT defa SCHH #sch STYP E2N REPR DISO

!*! GZG Quasiständig
SIZE TYPE URS SC 150 SPLI '1*2'
        #include text_prop
        move del; text 'Plattenschnittgr./ GZG Quasiständig / max Mxx '
        LC   NO 1421 ; QUAD TYPE MX UNIT defa SCHH #sch STYP E2N REPR DISO
        #include text_prop
        move del; text 'Plattenschnittgr./ GZG Quasiständig / min Mxx '
        LC   NO 1422 ; QUAD TYPE MX UNIT defa SCHH #sch STYP E2N REPR DISO

        #include text_prop
        move del; text 'Plattenschnittgr./ GZG Quasiständig / max Myy '
        LC   NO 1423 ; QUAD TYPE MY UNIT defa SCHH #sch STYP E2N REPR DISO
        #include text_prop
        move del; text 'Plattenschnittgr./ GZG Quasiständig / min Myy '
        LC   NO 1424 ; QUAD TYPE MY UNIT defa SCHH #sch STYP E2N REPR DISO

end
```

Ergebnisse Bemessung Flächenelemente:
```code
+PROG WING
HEAD 'Bewehrung Flächenelemente'

ctrl meas 2d ! Steuerung, dass alle Ergebnisse für 2D Darstellung angepasst werden

SIZE TYPE -URS SC 100 SPLI '1*1'

COL2 C5 -2071 C25 -3017 -3017 ! Auflagerdarstellung ausgeschaltet

! Blick von oben (Z = 1 -> Normal zur unserer Ebene)
VIEW TYPE DIRE X 0 Y 0 Z 1 AXIS POSY ROTA 0

grp 10 ! Auswahl welche Elementgruppe dargestellt werden soll

! Untere (Lower - L) Längsbewehrung
LC DESI 2 ; QUAD TYPE ASLH UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! Untere Querbewehrung
LC DESI 2 ; QUAD TYPE ASLQ UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! Obere (Upper - U) Längsbewehrung
LC DESI 2 ; QUAD TYPE ASUH UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! Obere Querbewehrung
LC DESI 2 ; QUAD TYPE ASUQ UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! Rissbreite unten
LC DESI 2 ; QUAD TYPE WKLM UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! mit semi-colon ; können mehrere Zeilen in einer Zeile geschrieben werden
END
           
```

