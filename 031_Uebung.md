# Keller

In der Übung wird ein Keller modelliert. Dabei werden folgende Bauteile explizit abgebildet:
- Fundamentplatte
- Wände
- Stütze
- Auflager die die Decke (Festhaltung am Wandkopf / Stützenkopf) abbildet

Insbesondere wird bei der Übung auf folgende Punkte eingegangen:
- Bettung von Flächenelementen - Fundamentplatte
- Linienauflager - Wandkopf
- Einlesen von Daten aus Textdateien - Knoten
- Erstellen von Öffnungen in Flächenelementen
- Erddrucklasten
- Bemessung Flächenelemente inkl. Durchstanzen

3D Modell des Kellers der in Zuge der Übung erstellt wird:

![031_Keller_3D.png](/docs/assets/images/031_Keller_3D.png)

Grundriss des Kellers mit den Hauptabmessungen:

![031_Keller_Grundriss.png](/docs/assets/images/031_Keller_Grundriss.png)

Der komplette SOFiSTIK Code der unten Abschnittsweise gegeben ist, kann komplett im .dat Format [hier](https://aiztok.github.io/SBB/docs/FH_SBB_Keller_v01.dat) heruntergeladen werden.

## Inhalt
[Erstellen des Projekts](#erstellen-des-projekts)

[Projektinfo](#projektinfo)

[Materialangabe](#materialangabe)

[Querschnittsdefinition](#querschnittsdefinition)

[Modelleingabe](#modelleingabe)

[Definition der Einwirkungen](#definition-der-einwirkungen)

[Definition der Lasten](#definition-der-lasten)

[Berechnung](#berechnung)

[Überlagerung der Ergebnisse - Erstellung der Einwirkungskombinationen](#überlagerung-der-ergebnisse)

[Bemessung der Flächenelemente](#bemessung-der-flächenelemente)

[Ergebnisausgabe](#ergebnisausgabe)

## Erstellen des Projekts
Das neue Projekt wird anhand einer Anwendervorlage erstellt.
Die Anwendervorlage kann [hier](https://aiztok.github.io/SBB/docs/FH_SBB_SSD-Vorlage_V01.sofistix) heruntergeladen werden.
Somit ist die Norm und andere Systeminformationen (Z-Richtung, Gruppendivisor) bereits vor eingestellt. 

## Materialangabe
Die Materialangabe kann von [[011_Einfuehrungsbeispiel]] übernommen werden, jedoch werden diesmal nur die Materiale genommen die auch tatsächlich Anwendung finden.

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
!
$----------------------------------------------------------------------------------
!*! Bewehrung
STEE NO 101 TYPE b 550b titl 'Bewehrung B550B'

end   
```
## Querschnittsdefinition

```
+prog aqua
head 'Definition des Querschnitts'
page unii 0 unio 0
ctrl rest 2 ! Steuerung, dass die Angaben des vorherigen AQUA nicht überschrieben werden

!*! Querschnitt Stütze
SREC   1 H 0.30[m] B 0.30[m]  MNO  1 MRF 101 titl 'Stütze B/H=30/30cm'
  
end 
```
## Modelleingabe
In folgenden wird das Modell über die eingaben von:
- Strukturpunkte (SPT)
- Strukturlinien (SLN): verbinden die SPTs
- Strukturflächen (SAR): Umrandung definiert durch SPTs

Die Knoten werden aus Textdateien abgerufen, dafür sollten die folgenden Dateien im gleichen Ordner wie die Sofistik-datei abgelegt sein:
- [Knoten Fundamentplatte](https://aiztok.github.io/SBB/docs/Knoten_Fundamentpl.dat)
- [Knoten Wände](https://aiztok.github.io/SBB/docs/Knoten_Waende.dat)
- [Knoten Stütze](https://aiztok.github.io/SBB/docs/Knoten_Stuetze.dat)

Der Inhalt der Datei mit den Knoten der Fundamentplatte ist wie folgend:
```
$No   X       Y       Z      NREF
1    0.000   0.000   0.000  -
2    0.000   9.400   0.000  1
3    9.260   9.400   0.000  1
4    9.260   -0.860  0.000  1
5    4.600   -0.860  0.000  1
6    4.600   0.000   0.000  1      
```
wo:
- 1. Spalte: Knotennummer (no)
- 2. Spalte: X-Koordinate in [m] (x)
- 3. Spalte: Y-Koordinate in [m] (y)
- 4. Spalte: Z-Koordinate in [m] (z)
- 5. Spalte: Nummer des Referenzknotens (nref)

Eingabe Code für das Modell:
```
+prog sofimshc
head 'Model'
page unii 0 unio 0 ! Input and Output units

$----------------------------------------------------------------------------------
!*!Label FE-Netz
ctrl hmin 0.5      ! Mesh size in [m]
ctrl mesh 2        ! Mesh type (1 = beams, 2 = shells, 3 = volume)

$----------------------------------------------------------------------------------
!*!Label System
syst 3D gdiv 1000 gdir posz ! wir wählen in 3D Modell

$----------------------------------------------------------------------------------
!*!Label Punkte
! Die Strukturpunkte werden erzeugt durch das Einlesen einer Textdatei wo folgende Werte angegeben sind:
! 1. Spalte: Knotennummer (no)
! 2. Spalte: X-Koordinate in [m] (x)
! 3. Spalte: Y-Koordinate in [m] (y)
! 4. Spalte: Z-Koordinate in [m] (z)
! 5. Spalte: Nummer des Referenzknotens (nref)
! Durch die Vorgabe von "=" z.B. ref=pt oder sx=1 wird der Wert für alle folgenden Reihen definiert
! Mit sx,sy und sz wird die Richtung der lokalen Koordinaten System des Knotens definiert -
! dies ist wichtig für die Knoten dies sich auf diesen Knoten beziehen (ref),
! weil der x,y,z auf das lokale k.s. des referenzierten Knotens sich beziehen
!
spt no     x     y  z  nref ref=pt sx=1 sy=0 sz=0
! Fundamentplatte
#include knoten_fundamentpl.dat
! Wände
#include knoten_waende.dat
! Stütze
#include knoten_stuetze.dat

$----------------------------------------------------------------------------------
!*!Label Flächen
! Fundamentplatte
sar 1 grp 10 mno 1 mrf 101 t 0.30[m] mctl regm cb 30000 ct 2/3*30000 qref belo
    sarb out na  1 ne  2
    sarb out na  2 ne  3
    sarb out na  3 ne  4
    sarb out na  4 ne  5
    sarb out na  5 ne  6
    sarb out na  6 ne  1

! Wände
! durch die sinnvolle Nummerierung der Wandknoten können die über eine Schleife erzeugt werden
loop#i 7
    sar 20+#i grp 20 mno 1 mrf 101 t 0.2[m] mctl regm qref cent
        sarb out na  11+#i ne  12+#i
        sarb out na  12+#i ne  32+#i
        sarb out na  32+#i ne  31+#i
        sarb out na  31+#i ne  11+#i
endloop
! die letzte Wand wird separat erstellt (Nummerierungfolge anders als beim Rest)
sar 28 grp 20 mno 1 mrf 101 t 0.2[m] mctl regm qref cent
    sarb out na  16 ne  11
    sarb out na  11 ne  31
    sarb out na  31 ne  36
    sarb out na  36 ne  16

! Öffnungen
! Es wird noch die Öffnung in der Wand erstellt
sar void
    sarb out na  50 ne  51
    sarb out na  51 ne  52
    sarb out na  52 ne  53
    sarb out na  53 ne  50

! Stütze
sln 1 npa 20 npe 21 grp 30 styp 'b.e' sno 1

! Auflager
! Stützenkopf - durch ZP werden die X und Y Richtung gehalten, Z kann sich frei verschieben
! mit dem Minus Vorzeichen wird der existierende Knoten 21 bearbeitet - fix definiert
spt -21 fix zp

! Wandkopf
! wir erstellen neue Linien am Wandkopf,
! Nummerierung ist egal, deswegen unter NO einfach "-"
sln - npa 31 npe 32 fix zp
sln - npa 32 npe 33 fix zp
sln - npa 33 npe 34 fix zp
sln - npa 34 npe 35 fix zp
sln - npa 36 npe 31 fix zp
sln - npa 36 npe 37 fix zp
sln - npa 37 npe 38 fix zp

end  
```

## Definition der Einwirkungen
Übernehmen wir von dem Einführungsbeispiel (Wind kann entfernt werden, da nicht verwendet) und wir ergänzen G_3 für den ständigen Erdruhedruck:
```
+prog sofiload
head 'Definition der Einwirkungen'
echo full extr
!Ständige Lasten
    ACT G   
    ACT G_1 
    ACT G_2 
    ACT G_3
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

! Weil sich die Eingabe der Wand- und Stützenkopflasten mehrmals wiederholt,
! wird ein Codeblock erstellt, wo die Wand- und Stützenkopflast als eine Variable
! definiert werden
! später wird dann immer nur der Codeblock abgerufen
#define kopflasten
    line auto type pg p1 #pwnd x1 spt (31 37 1) x2 spt (32 1)
    line auto type pg p1 #pwnd x1 spt 36 x2 spt 31
    ! Stütze
    poin auto type pg p #pst x spt 21
#enddef

!*!Label Ständige Lasten
lc 2 type G_1 titl 'EGW EG'
    let#pwnd 30 ! [kN/m] Wandkopflast
    let#pst 200 ! [kN] Stützenkopflast
    #include kopflasten

lc 3 type G_2 titl 'Ausbaulasten'
    area sar 1 type pg p1 2.16
    let#pwnd 7.5 ! [kN/m] Wandkopflast
    let#pst 50 ! [kN] Stützenkopflast
    #include kopflasten

lc 4 type G_3 titl 'Erdruhedruck'
    ! Last die von der "Säulenhöhe" abhängig sind,
    ! wie z.B. Wasserdruck oder Erddruck können mit "VOLU" definiert werden
    ! mit Z wird die globale Höhe wo der Druck 0 ist vorgegeben und mit
    ! p1 der Zuwachs des Drucks pro Höhe (kN/m).
    ! Für den Erdruhedruck als K0*Bodenwichte
    ! mit "ref sar no x" werden die Äußeren Wandflächen gewählt
    volu ref sar no 20,21,22,23,24,28 type pz z -2.5 p1 0.5*19

!*!Label Veränderliche Lasten
lc 5 type Q titl 'Nutzlast'
    area sar 1 type pg p1 2.0
    let#pwnd 5 ! [kN/m] Wandkopflast
    let#pst 33 ! [kN] Stützenkopflast
    #include kopflasten

lc 6 type S titl 'Schneelast'
    let#pwnd 3 ! [kN/m] Wandkopflast
    let#pst 30 ! [kN] Stützenkopflast
    #include kopflasten

end  
```

## Berechnung
Wie bei anderen Beispielen wird eine lineare Berechnung von allen Lastfällen durchgeführt:

```
+prog ase
head Berechnung
echo full extr
! Lineare Berechnung
syst prob line

! Alle Gruppen sind eingeschaltet
grp -
! Alle Lastfälle werden berechnet
lc all

end
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
    ACT G_3 ! Erddruck
    ACT Q ! Nutzlast
    ACT S ! Schnee

let#lf 1120
SUPP ETYP TYPE   LC   FROM  TITL EXTR=MAMI COMB=1

COMB 4 perm BASE - $ kombination quasi-ständig
    ACT G_1 ! EGW
    ACT G_2 ! Ständige Lasten
    ACT G_3 ! Erddruck
    ACT Q ! Nutzlast
    ACT S ! Schnee

let#lf 1420
SUPP ETYP TYPE   LC   FROM  TITL EXTR=MAMI COMB=4
#include results

COMB 10 desi BASE -  $ kombination GZT
    ACT G_1 ! EGW
    ACT G_2 ! Ständige Lasten
    ACT G_3 ! Erddruck
    ACT Q ! Nutzlast
    ACT S ! Schnee

let#lf 2120
SUPP ETYP TYPE   LC   FROM  TITL EXTR=MAMI COMB=10
#include results

end
```

## Bemessung der Flächenelemente
Die Flächenelemente werden mit dem Modul `BEMESS` bemessen, hier unterteilen wir die einzelnen Definitionen in mehrere `BEMESS` Module:
- Definition der geometrischen Parameter (Lage der Bewehrung) und der Bemessungsparameter (Rissbreite, Ausgangsbewehrung)
- Bemessung im Grenzzustand der Tragfähigkeit
- Bemessung im Grenzzustand der Gebrauchstauglichkeit - Rissweite

Definition der Parameter für die Fundamentplatte und Wände:
```code
+prog bemess
head 'Definition der Bewehrungsparameter'
echo full extr

! Definition der Abstände der Bewehrungslagen
! ha/hb ... Abstand von Bauteilrand zur oberen/unteren Hauptbewehrung
! dha/dhb ... Abstand zwischen der oberen/unteren Querbewehrung zu Hauptbewehrung
geom - ha 35+10/2 dha 10 hb 35+10/2 dhb 10

! Winkel der Bewehrung zu der lokalen Achse der Platten
dire upp 0 low 0

! Eingabe der Parameter für die Berechnung
! du ... [mm] Durchmesser der Bewehrung - Eingangsparameter für die Risbreitenberechnung
! wku/wkl ... [mm] Rissweite
! asu/as
! Beispiel AQ 76 matte (DM7.6 alle 10cm in beiden Richtungen)
! Bodenplatte
para - du 10 wku 0.3 wkl 0.3 $$
             asu 4.54 $$
             asu2 4.54 $$
             asl2 4.54 $$
             asl 4.54
! mit doppeldollar-Zeichen $$ kann einie Zeile in mehr Zeilen unterteilt werden (bessere Übersicht),
! wird aber als eine von Programm gelesen
!--------------------------------------------------
! Wände
! mit para nog wird die Gruppe der Wände explizit benannt
! die folgenden Eingaben sind somit nur für die Wände gültig
para nog 20 du 10 wku 0.3 wkl 0.3 $$
             asu 0.79*100/10 $$
             asu2 0.79*100/10 $$
             asl2 0.79*100/10 $$
             asl 0.79*100/10

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

punc ro_v 1 ! Begrenzung der Erhöhung der Längsbew. auf 1% um den Durchstanznachweis zu erbringen (vrdc)

grp 10,20 ! Welche Gruppe der Flächenelemente zu bemessen ist

! Eingabe der Lastfälle / Ergebniskombinationen
lc (d) ! alle als GZT gespeicherten Lastfälle
!LC (2121 2136 1) ! explizite Auswahl der LFs

END  
```

Besonders interessant ist die Bemessung aufs Durchstanzen an den Wandecken bei der Öffnung und bei der Stütze. 
Anbei der Auszug aus dem `REPORT` für den Stützenfuß.
Wie ersichtlich wird die Abmessung der Stütze automatisch erkannt (*a* und *b*), die Dicke der Platte *h* und der statischen Höhe *d*:

```js
Auflagerknoten Nummer =    20          X= 4.840 [m]     Y= 5.940 [m]
                                        Z= 0.000 [m]
größte Querkraft  V-Ed= -746. [kN]     LF=  2121  aus Querkräften (ELLA ohne Knotenwerte)
  (=Plattenquerkraft plus Bodenpressung, da Bodenpressung ja wieder abgezogen wird)
abzüglich Bodenpressung  0.08 [kN/m2] (um Pressung aus EG Bodenplatte vermindert)
größte Querkraft  V-Ed= -746. [kN]       = -746. +   0.1 [kN]
Stützenabmessung     b= 0.300 [m]       a= 0.300 [m]
Plattendicke  h-platte= 0.300 [m]       d= 0.255 [m]
Rundschnitt iteriert a= 0.510 [m]    utot= 4.404 [m]      u1= 4.404 [m]
reduzierter Abstand des Nachweisschnittes wegen gebetteter Fundamentplatte!
Biegebewehrung     fe>=  9.69 [cm2/m]  ro=  0.38 [o/o]  vrdc=  0.51 [MPa]
 v-Ed = 1.15·V/u/d    =  0.76 [MPa]      >  0.51 [MPa]  =vrdc <=  0.76 =VRd,max
        1.15=pauschaler Exzentrizitätsbeiwert beta
      ( 1.10=Exzentrizitätsbeiwert nach EN 1992-1-1 6.4.3 (6.39))
        Zwischenwerte:  y0=  0.00          x0=  0.00 (Schwerpunkt des Rundschnitts)
        (6.39)          ky=  0.60          kx=  0.60 (kx für My zu verwenden!)
                       W1y=  0.65         W1x=  0.65 (W1x für My zu verwenden!)
                       Mx = -16.3         My = -10.0 (um den Stützenschwerpunkt)
                       Mxs= -16.3         Mys= -10.0 (um Schwerpunkt des Rundschnitts)
                       x,y hier lokal mit alpha=  -0.0 Grad
                               -16.3   4.4                -10.0   4.4
         beta= 1+ SQRT( (0.60* ------*-----)^2  +  (0.60* ------*-----)^2 )= 1.10
                               -746.   0.7                -746.   0.7
Am Stützenanschnitt: v-ED(u0) =  2.80 [MPa]   <  v-RD,max =  2.91 [MPa]
(v-RD,max berechnet nach NA.6.4.5(3) mit vrd,c und u1/u0=  4.01)
1. Bewehrungsreihe im Abstand a1=0.50·d
Optimaler Abstand der Durchstanzbewehrungsreihen sr= 0.191 [m] =0.75·d
Keine Durchstanzschubbewehrung mehr erforderlich ab uout= 2.03 [m]
erf.Schubbewehrung Asw= (v-Ed-0.75·vrdc)·u·sr/1.5/fywd_ef (fywd_ef= 314.)
In den ersten zwei Reihen wird die Schubbewehrung um 60% erhöht.
    1. Rundschnitt Asi= 10.94 [cm2]   ass= 28.58 [cm2/m2]  a1= 0.127 [m]
    2. Rundschnitt Asi= 10.94 [cm2]   ass= 17.86 [cm2/m2]  a2= 0.319 [m]
Im Durchstanzbereich sind mindestens        9.69 [cm2/m]
als Biegebewehrung einzulegen.
```
![031_Bemess_Durchstanzen.png](/docs/assets/images/031_Bemess_Durchstanzen.png)

Das Ergebnis der Bemessung ist auch in `GRAPHICS` graphisch/textlich dargestellt:
![031_Graphics_Durchstanzen.png](/docs/assets/images/031_Graphics_Durchstanzen.png)

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

grp 10,20 ! Welche Gruppe der Flächenelemente zu bemessen ist

! Eingabe der Lastfälle / Ergebniskombinationen
lc (p) ! alle als GZG Quasi-ständig gespeicherten Lastfälle
!LC (1421 1436 1) ! explizite Auswahl der LFs

end   
```
## Ergebnisausgabe

Schnittgrößen für die Fundamentplatte:
```code
+PROG WING
HEAD 'Ergebnisse Flächenelemente'
ctrl meas 2d ! Steuerung, dass alle Ergebnisse für 2D Darstellung angepasst werden

SIZE TYPE -URS SC 100 SPLI '1*1'

COL2 C5 -2071 C25 -3017 -3017 ! Auflagerdarstellung ausgeschaltet

! Blick von oben (Z = 1 -> Normal zur unserer Ebene)
VIEW TYPE DIRE X 0 Y 0 Z 1 AXIS POSY ROTA 0

grp 10 ! Auswahl welche Elementgruppe dargestellt werden soll - Fundamentplatte

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

```
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

! Untere (Lower - L) Längsbewehrung rein aus Biegung
LC DESI 2 ; QUAD TYPE ABLH UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! Untere Querbewehrung rein aus Biegung
LC DESI 2 ; QUAD TYPE ABLQ UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! Obere (Upper - U) Längsbewehrung rein aus Biegung
LC DESI 2 ; QUAD TYPE ABUH UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! Obere Querbewehrung rein aus Biegung
LC DESI 2 ; QUAD TYPE ASUQ UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! Rissbreite unten
LC DESI 2 ; QUAD TYPE WKLM UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! Rissbreite unten
LC DESI 2 ; QUAD TYPE WKUM UNIT DEFA SCHH 0.3 STYP E2N FILL NO REPR DISO AVER NO

! mit semi-colon ; können mehrere Zeilen in einer Zeile geschrieben werden
END
```

