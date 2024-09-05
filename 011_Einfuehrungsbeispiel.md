# 1. Übung - Einführungsbeispiel
Das Einführungsbeispiel ist ein einfacher Halbrahmen mit einer Wand und einer Decke.

![Skizze_Halbrahmen.png](/docs/assets/images/Skizze_Halbrahmen.png)

Diese Übung soll als Einstieg in die Modellierung und Berechnung von 2D Rahmenbauwerken dienen mittels SOFiSTiK.

Der komplette SOFiSTIK Code der unten Abschnittsweise gegeben ist, kann komplett im .dat Format
[hier](https://aiztok.github.io/SBB/docs/FH_SBB_Einfuehrungsbeispiel_v01.dat) 
heruntergeladen werden.

<iframe width="640" height="480" style="border:1px solid #eeeeee;" src="https://3dviewer.net/embed.html#model=https://raw.githubusercontent.com/AIztok/SBB/main/docs/FH_SBB_Einfuehrungsbeispiel.ifc$camera=-1.97859,-4.14302,-6.48807,2.81250,-0.60000,0.50000,0.00000,-1.00000,0.00000,45.00000$projectionmode=perspective$envsettings=fishermans_bastion,off$backgroundcolor=42,43,46,255$defaultcolor=200,200,200$defaultlinecolor=100,100,100$edgesettings=off,0,0,0,1"></iframe>


## Inhalt
[Was passiert im Hintergrund?](#was-passiert-im-hintergrund)

[Erstellen des Projekts](#erstellen-des-projekts)

[Projektinfo](#projektinfo)

[Materialangabe](#materialangabe)

[Querschnittsdefinition](#querschnittsdefinition)

[Modelleingabe](#modelleingabe)

[Definition der Einwirkungen](#definition-der-einwirkungen)

[Definition der Lasten](#definition-der-lasten)

[Berechnung](#berechnung)

[Überlagerung der Ergebnisse - Erstellung der Einwirkungskombinationen](#überlagerung-der-ergebnisse)

[Bemessung der Stabelemente](#bemessung-der-stabelemente)

[Ergebnisausgabe](#ergebnisausgabe)

[Extra TRAC](#extra)

## Was-passiert-im-Hintergrund?
FEA - Finite Element Analysis - Finite Elemente Berechnung

Um die Grundlagen einer Finite Elemente Berechnung an dem einfachen Einführungsbeispiel mit Stabelementen besser zu verstehen, wurde in einem [Jupyter](https://jupyter.org/) Notebook die Berechnung der Schnittgrößen und Verschiebungen Schritt per Schritt durchgeführt. Die Anzahl der Finite Elemente wurde klein gehalten, damit die Übersicht nicht verloren geht.
Damit keine Installation von Python erforderlich ist (aber dafür ein Account auf Google :)) wurde der Code auf Google Colab hochgeladen:

[Einführungsbeispiel in Python - Link zu dem Jupyter Notebook auf Google Colab](https://colab.research.google.com/drive/1Yio_5SlEL6frEUguNEkPdDJhUXO9Q-f7?usp=sharing)

Das File / Workbook kann auch [hier](https://aiztok.github.io/SBB/docs/FH_SBB_FEM_Example.ipynb) von GitHub heruntergeladen werden. 

## Erstellen-des-Projekts

Das neue Projekt wird anhand einer Anwendervorlage erstellt.
Die Anwendervorlage kann [hier](https://aiztok.github.io/SBB/docs/FH_SBB_SSD-Vorlage_V01.sofistix) heruntergeladen werden.
Somit ist die Norm und andere Systeminformationen (2D oder 3D, Z-Richtung, Gruppendivisor) bereits vor eingestellt. 

Wir öffnen das Programm SOFiSTiK SSD 2023 (oder auch neuer) 

![Animation_Search_SSD2023](/docs/assets/images/Animation_Search_SSD2023.gif)

und wählen die oben heruntergeladene Datei als Anwendervorlage. 
> In der Lehrveranstaltung wird auch das Erstellen der Anwendervorlage gezeigt.

![SSD vorlage](/docs/assets/images/SSD_Vorlage.png)

In der Anwendervorlage wurde bereits eine Basisstruktur fürs Projekt erstellt. Diese sollte alle Übungen im Rahmen der LVA abdecken.
Die Aufteilung und Nummerierung basiert auf Erfahrung des Autors in seiner Praxisarbeit, kann aber natürlich angepasst werden. Wesentlich hier ist, dass man eine Struktur findet, die bei meisten Projekten anwendbar ist.

Es wird bei den Übungen mit Texteingabe gearbeitet (also im Teddy bzw. in der Cadinp Programmiersprache), deswegen wurden nur Tasks mit Teddy Inputs (gelber Teddybär) hergerichtet. Es ist jedoch möglich im SSD sowohl Task mit Texteingabe als auch über GUI zu kombinieren.

![Projektnavi.png](/docs/assets/images/Projektnavi.png)

## Projektinfo
Es ist sinnvoll und hilfreich am Anfang wie ein Log / readme zu erstellen, wo die wesentlichen Informationen zum Projekt geschrieben sind. Dies dient als Erinnerung für einen selber oder auch wenn jemand andere das Projekt übernimmt.
Über den Modul `TEMPLATE` kann z.B. ein Text generiert werden:

```javascript
+prog template
head 'Einfuehrung'

! mit "!" oder "$" können Kommentare geschrieben werden
<text>
FH Campus Wien
SBB
Beispiel: Einfuehrungsbeispiel
Datum der letzten Änderung: 01.10.2023
Autor: AIztok
Sofistik Version: 2023

Log der Änderungen:
/

</text>
end
```


## Materialangabe
Im weiteren Schritt werden die Materialangaben im Modul `AQUA` definiert.
Wir werden nur mit Beton und Bewehrung arbeiten, jedoch wurden als Beispiel noch andere Materialien (Spannstahl, Konstruktionsstahl und Holz) definiert.
>Im Modul `AQUA` werden sowohl Materiale als auch Querschnitte definiert. Es wäre möglich in einem Modul beide zu definieren. In diesem Beispiel wird aber die Eingabe getrennt erstellt, zu achten ist hier im zweiten Modul auf den `CTRL REST 2` des sicherstellt, dass die Eingabe aus dem ersten `AQUA` nicht überschrieben wird.

```javascript
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
CONC NO 2  TYPE c 30R           titl 'Beton C30/37 Schnellerhärtend'
CONC NO 3  TYPE c 30S           titl 'Beton C30/37 Langsamerhärtend'
CONC NO 4  TYPE c 25 gam 0.0    titl 'Beton C25/30 Ohne EGW' ! Ohne EGW, z.B. für Bohrpfähle
!
$----------------------------------------------------------------------------------
!*! Bewehrung
STEE NO 101 TYPE b 550b titl 'Bewehrung B550B'
$----------------------------------------------------------------------------------
!*! Spannstahl
STEE NO 201 TYPE Y 1860 titl 'Y1860'
$----------------------------------------------------------------------------------
!*! Konstruktionsstahl
STEE NO 301 TYPE S 355 titl 'S355'
$----------------------------------------------------------------------------------
!*! Holz
TIMB NO 401 TYPE C 30 titl 'Holz C30'

end 
```

## Querschnittsdefinition
Es werden zwei rechteckige Querschnitte für die Wand bzw. Decke erstellt.
Dazu werden zwei mögliche Definitionen vorgestellt:

```javascript
+prog aqua
head 'Definition des Querschnitts'
echo full extr
ctrl rest 2 ! Steuerung, dass die Angaben des vorherigen AQUA nicht überschrieben werden

$----------------------------------------------------------------------------------
!*! Wand-QS
srec 1 h 0.25 b 100[cm] so 0.04[m] su 0.04[m] mno 1 mrf 101 ! QS-Definition mit vereeinfachter Eingabe für Rechteckquerschnitte

$----------------------------------------------------------------------------------
!*! Decken-QS
SECT NO   2  MNO  2 MRF 101 titl 'Section A-A' ! QS-Definition über Polygonpunkte

let#h 0.3[m] ! Lokale Variable für die Querschnittshöhe
let#b 1.0[m] ! Lokale Variable für die Querschnittsbreite

!*! Spannungspunkte - wo wir die Spannungen ermitteln wollen
$ Oberkante
SPT OKL  Y -#b/2 Z 0 MNO 2
SPT OKM  Y  0.00   Z 0 MNO 2
SPT OKR  Y +#b/2 Z 0 MNO 2
$ Unterkante
SPT UKL  Y -#b/2 Z #H MNO 2
SPT UK   Y  0.00   Z #H MNO 2
SPT UKR  Y +#b/2 Z #H MNO 2

POLY TYPE O   MNO  2
    VERT   1  Y   0     Z   0       REFP OKL
    VERT   2  Y   0     Z   0       REFP OKM
    VERT   3  Y   0     Z   0       REFP OKR
    VERT  11  Y   0     Z   0       REFP UKR
    VERT  12  Y   0     Z   0       REFP UK
    VERT  13  Y   0     Z   0       REFP UKL
! Obere Bewehrung
! Eingabe Bewehrungsdurchmesser und Abstand
LRF 1 YB -0.04 ZB  0.04 YE 0.04 ZE  0.04 REFA 'OKR' REFE 'OKL' D 10[mm] A 0.1[m] DIST FULL LAY M1
! Noch andere Möglichkeiten Linienbewehrung im QS zu definieren
! Eingabe Bewehrungsdurchmesser und Anzahl
$LRF 1 YB -0.04 ZB  0.04 YE 0.04 ZE  0.04 REFA 'OKR' REFE 'OKL' D 10[mm] A 9[-] DIST FULL LAY M1
! Eingabe Bewehrungsquerschnittsfläche im Querschnitt
$LRF 1 YB -0.04 ZB  0.04 YE 0.04 ZE  0.04 REFA 'OKR' REFE 'OKL' AS 6.0[cm2]  D 10[mm] DIST FULL LAY M1
! Eingabe Bewehrungsquerschnittsfläche pro m
$LRF 1 YB -0.04 ZB  0.04 YE 0.04 ZE  0.04 REFA 'OKR' REFE 'OKL' AS 6.0[cm2/m] A 1 D 10[mm] DIST EVEN LAY M1

! Untere Bewehrung
LRF 1 YB -0.04 ZB -0.04 YE 0.04 ZE -0.04 REFA 'UKR' REFE 'UKL' D 10[mm] A 0.1[m] DIST FULL LAY M2

! Schubschnitte                                                                                         $schwächere Steg wegen der Kabel
CUT 'Csc' ZB 'S' BRED 0.1[m] ! Schubschnitt durch den Schwerpunkt des QS

end 
```

## Modelleingabe
Es wird das Modul `SOFIMSHC` verwendet, dass mit Strukturelementen arbeitet.
Es werden z.B.:
- Strukturpunkte (`SPT`)
- Strukturlinien (`SLN`)
- Strukturflächen (`SAR`)
erstellt.
Die Vernetzung der Strukturelement erfolgt nach der Berechnung des Moduls im Hintergrund.
Alternativ kann auch der Modul `SOFIMSHA` verwendet werden, wo direkt mit Finiten Elementen gearbeitet wird. 

```javascript
+prog sofimshc
head 'Model'
page unii 0 unio 0

! Definition der Parameter für die Vernetzung
ctrl hmin 1.0
ctrl mesh 1

! Definition des FE-Systems (gruppen divisor gdiv, richtung des eigengewichts)
syst 2D gdiv 100 gdir posy

! Definition der Strukturpunkte mit Auflagerbedingungen
spt 1 x 0 y 0 fix f
spt 2 x 0 y -3.15
spt 3 x 3 y -3.15
spt 4 x 6 y -3.15 fix py

! Definition der Strukturlinien
sln 1 1 2 sno 1 grp 1 titl 'Wand'   styp b.e
sln 2 2 3 sno 2 grp 2 titl 'Decke'  styp b.e
sln 3 3 4 sno 2 grp 2 titl 'Decke'  styp b.e

end   
```

## Definition-der-Einwirkungen
Diese Definition in  `SOFILOAD` dient der Benennung der Einwirkungen  und um ggf. Sicherheitsbeiwerte und Kombinationsbeiwerte explizit du anzupassen - mehr dazu siehe Kommentare im Code unten:

```javascript
+prog sofiload
head 'Definition der Einwirkungen'
echo full extr
! Hier werden die Sicherheitsbeiwerte und Kombinationsbeiwerte definiert

! Ohne eine explizite EIngabe der Sicherheitsbeiwerte und Kombinationsbeiwerte werden
! die im Programm vordefinierten Werte genommen (Auswahl Norm und Kategorie von Anwender).
! Altrnativ ist es möglich, wie unten kommentiert dargestellt, diese Werte auch explizit anzugeben,
! falls die ggf. erforderlich ist.        

!Ständige Lasten
    ACT G   $GAMU 1.35 GAMF 1.00 PSI0 1.00 PSI1 1.00 PSI2 1.00 PS1S 1.00 PART G  SUP PERM TITL 'STäNDIGE LASTEN'
    ACT G_1 $GAMU 1.35 GAMF 1.00 PSI0 1.00 PSI1 1.00 PSI2 1.00 PS1S 1.00 PART G  SUP PERM TITL 'KONSTRUKTIONSGEWICHT'
    ACT G_2 $GAMU 1.35 GAMF 1.00 PSI0 1.00 PSI1 1.00 PSI2 1.00 PS1S 1.00 PART G  SUP PERM TITL 'AUSBAUGEWICHT'

! Nutzlast
    ACT Q   $GAMU 1.50 GAMF 0.00 PSI0 0.80 PSI1 0.50 PSI2 0.00 part Q sup excl titl 'Nutzlast'

! Schneelast
    ACT S  $GAMU 1.50 0 SUP COND PSI0 0.75 PSI1 0.50 PSI2 0.00  TITL 'Schneelast'

! Windlast
    ACT W  $GAMU 1.50 0 SUP COND PSI0 0.75 PSI1 0.50 PSI2 0.00  TITL 'Wind'
    
end
```

## Definition-der-Lasten

Ständige Lasten:
```javascript
+prog sofiload
head 'Definition der Lasten - Ständige'

!*!Label EGW
lc 1 facd 1.0 type G_1 titl 'EGW'
! Mit "facd" wird das Eigengewicht gem. in Projektdefinition vorgegebener Richtung der Schwerkraft
! der Querschnittsflächen und der Materialwichte, ermittelt.


end 
```

Veränderliche Lasten:
```javascript
+prog sofiload
head 'Definition der Lasten - Veränderliche'

!*!Label Nutzlast
lc 10 type Q titl 'Nutzlast'
poin auto type pg p 50 x 3 y -3.15
! Wir definieren beispielhaft eine punktuelle Nutzlast in der Mitte der Decke

!*!Label Schneelast
lc 20 type S titl 'Schneelast'
$beam grp 2 type pg pa 0.8 $ wir können die Linienlast über Referenz zu der Stabgruppe aufbringen
$line auto type pg p1 0.8 x1 0 y1 -3.15 x2 6 y2 -3.15 ! Linienlast über Referenz zu globalen Koordinaten
line sln 2 type pg p1 0.8 ! Linienlast über Referenz zu strukturlinie
! wie oben aufgezeigt, kann eine Linienlast über mehrere Wege definiert werden

!*!Label Windlast
lc 30 type W titl 'Windlast'
beam grp 1 type pxx pa 1

end       
```

## Berechnung
Es wird eine lineare Berechnung durchgeführt - also es werden keine geometrischen oder materiellen Nichtlinearitäten berücksichtigt.

```javascript
+prog ase
head Berechnung
! Berechnungsparameter
syst prob line        ! lineare Berechnung
!
grp - ! alle Gruppen sind eingeschaltet, sonst ist es möglich gruppen auszuschalten (z.B. Bauzustände)
!
lc all ! so werden alle definierten Lastfälle berechnet
$lc 1,10,20,30 ! so können explizite Lastfälle berechnet werden

end 
```


## Überlagerung-der-Ergebnisse
Die linear ermittelten Schnittgrößen und Verschiebungen werden für die Ermittlung der Einwirkungskombinationen (EWK) linear überlagert mit entsprechenden Sicherheits- und Kombinationsfaktoren.

Wichtig ist anzumerken, dass SOFiSTiK die Ergebnisse der Überlagerungen in Lastfälle (LF) speichert.
Jedoch muss darauf geachtet werden, dass nicht vorhandene LFs (sei es tatsächlich eingegebenen Lasten oder Ergebnisse von Kombinationen) überschrieben werden - also nicht verwendete LFs verwendet werden. Hier hilft ein klares Nummerierungs-system von LFs.

```javascript
+prog maxima
head 'Ergebniskombination'

! Output control
$echo full yes
echo chck val full

COMB 1 rare BASE - $ kombination charakteristisch
    ACT G_1
$        LC NO 1 TYPE G  ! man kann explizit die LF nennen oder es werden alle die mit G_1 im SOFILOAD genannt wurden genommen
    ACT Q
$        LC NO 10 TYPE Q
    ACT S
$        LC NO 20 TYPE Q
    ACT W
$        LC NO 30 TYPE Q

let#lf 1120
SUPP ETYP TYPE   LC   FROM  TITL EXTR=MAMI COMB=1
! mit #define / #enddef können text blöcke definiert werden, die immer wieder verwendet werden
! in diesem Fall wählen wir aus einer List aus, für welche Ergebnisse wollen wir, dass die Software
! eine Ergebniskombination erstellt / ermittelt.
#define results
$      'spri' P   #lf+1
$      'spri' M   #lf+3

      'NODE' PX  #lf+1
      'NODE' PY  #lf+3
$      'NODE' PZ  #lf+5
$      'NODE' MX  #lf+7
$      'NODE' MY  #lf+9
      'NODE' MZ  #lf+11
      'NODE' UX   #lf+13
      'NODE' UY   #lf+15
$      'NODE' UZ   #lf+17
$      'NODE' URX  #lf+19
$      'NODE' URY  #lf+21
      'NODE' URZ  #lf+23

      'beam' N   #lf+1
$      'beam' VY  #lf+3
      'beam' VZ  #lf+5
$      'beam' MT  #lf+7
      'beam' MY  #lf+9
$      'beam' MZ  #lf+11
$      'BEAM' PTY #lf+13
$      'BEAM' PTZ #lf+15

$      'QUA*' mxx #lf+1
$      'QUA*' myy #lf+3
$      'QUA*' mxy #lf+5
$      'QUA*' vx  #lf+7
$      'QUA*' vy  #lf+9
$      'QUA*' nxx #lf+11
$      'QUA*' nyy #lf+13
$      'QUA*' nxy #lf+15

$      'QBED' p   #lf+1
$      'QBED' pt  #lf+3
#enddef
#include results

COMB 4 perm BASE - $ kombination quasi-ständig
    ACT G_1
    ACT Q
    ACT S
    ACT W

let#lf 1420
SUPP ETYP TYPE   LC   FROM  TITL EXTR=MAMI COMB=4
#include results


COMB 10 desi BASE -  $ kombination GZT
    ACT G_1
    ACT Q
    ACT S
    ACT W

let#lf 2120
SUPP ETYP TYPE   LC   FROM  TITL EXTR=MAMI COMB=10
#include results

end    
```

## Bemessung-der-Stabelemente

Die Bemessung der Stabelemente erfolgt im Modul `AQB`. In der zweiten Übung wird auch die Bemessung der Flächenelemente mit dem Modul `BEMESS` vorgestellt.

GZT Bemessung:
```javascript
+prog aqb urs:11
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

beam grp 1,2 ! wir können auswählen welche Gruppe soll bemessen werden

! Auswahl der Lastfälle für die Bemessung
$lc 21??
lc (d) ! alle gespeicherten Lastfälle

desi ulti ksv uld ksb uld

end   
```

GZG Bemessung:
```javascript
+prog aqb urs:13
head 'GZG Bemessung Stäbe'
echo full extr
page lano 0

rein mod sect rmod accu lcr 1 ! wir nehmen die Ergebnisse aus GZT und falls mehr Bew. erforderlich (Rissbreite) wird es akkumuliert.

beam grp 1,2 ! wir können auswählen welche Gruppe soll bemessen werden

! Auswahl der Lastfälle für die Bemessung
$lc 14??
lc (p) ! alle gespeicherten Lastfälle

nstr serv ksb sl ksv sl crac yes cw 0.3 ! Rissbreitennachweis

end 
```

## Ergebnisausgabe
Im folgenden werden folgende Ausgaben der Ergebnisse dargestellt:
- graphisch Ausgabe: der Code kann über das Programm `SOFiSTiK Graphic` exportiert werden, unten ist der Code "gesäubert" dargestellt;
- tabellarische Ausgabe: der Code kann über das Programm `SOFiSTiK Results viewer` exportiert werden, unten ist der Code "gesäubert" dargestellt;
- Excel Ausgabe: es ist möglich Ergebnisse auch in ein Excel Workbook zu exportieren und dann nachfolgende Nachweise z.B. dort weiterzuführen

### Graphische Ausgabe

```javascript
+PROG WING urs:14
HEAD 'Schnittgrößen'

PAGE LANO 0                       ! Ausgabe in Deutsch
SIZE -4 SC 50 FORM URS SPLI '3*1' ! A4 Blatt Vertikal, Maßstab 1:50, 3 Abbildungen über die Blatthöhe

LET#lf 1,10,20,30  ! wir definieren eine lokale Variable (bzw. Vektor mit 4 Werten), und zwar die Lastfallnummern

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
SIZE -4 SC 50 FORM URS SPLI '3*1' ! A4 Blatt Vertikal, Maßstab 1:50, 3 Abbildungen über die Blatthöhe

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

END

! wenn mehrere gleiche Module hintereinander berechnet werden
! in diesem Fall WING, kann auf das +prog verzichtet werden
! das Ergebniss ist, dass die Berechnung schneller läuft, da
! das Modul nicht jedesmal geschlossen und neu gestartet wird

HEAD 'Auflagerkräfte'

PAGE LANO 0
SIZE -4 SC 50 FORM URS SPLI '2*1'

! Auflagerkraft in X Richtung global
LC   NO 2121 ; NODE TYPE SX UNIT DEFA SCHH 0.3 FILL NO
and
LC   NO 2122 ; NODE TYPE SX UNIT DEFA SCHH 0.3 FILL NO

! Auflagerkraft in Y Richtung global
LC   NO 2123 ; NODE TYPE SY UNIT DEFA SCHH 0.3 FILL NO
and
LC   NO 2124 ; NODE TYPE SY UNIT DEFA SCHH 0.3 FILL NO

END

HEAD 'Verschiebungen und Verdrehungen'

PAGE LANO 0
SIZE -4 SC 50 FORM URS SPLI '2*1'

! Verschiebungen GZG Quas-Ständig in X Richtung
LC   NO 1420+13 ; NODE TYPE UX UNIT DEFA SCHH 0.3 FILL NO
LC   NO 1420+14 ; NODE TYPE UX UNIT DEFA SCHH 0.3 FILL NO

! Verschiebungen GZG Quas-Ständig in Y Richtung
LC   NO 1420+15 ; NODE TYPE UY UNIT DEFA SCHH 0.3 FILL NO
LC   NO 1420+16 ; NODE TYPE UY UNIT DEFA SCHH 0.3 FILL NO

END      
```
## Extra

### Tabellarische Ausgabe
Mit dem Modul `RESULTS` können Ergebnisse graphisch (Diagramme) oder tabellarisch ausgegeben werden. 

```javascript
+PROG RESULTS
HEAD 'Tabelarische Ausgabe Schnittgrößen'

PAGE LANO 0                       ! Ausgabe in Deutsch
SIZE TYPE "-URS" SPLI "2x1"

LC   NO 1
! Normalkräfte von Stäben
TXTP SHOW SIGN OVLP AMAX EXTR  YES
BEAM TYPE    N STYP BEAM REPR DLST
! Querkraft von Stäben
AND $ damit gleiche Tabelle, neue Spalte
TXTP SHOW SIGN OVLP AMAX EXTR  YES
BEAM TYPE   VZ STYP BEAM REPR DLST
! Biegemomente von Stäben
AND
TXTP SHOW SIGN OVLP AMAX EXTR  YES
BEAM TYPE   MY STYP BEAM REPR DLST

END  
```

### Excel Ausgabe
Es ist möglich Ergebnisse oder Modelangaben über einen Task wie in dem [Video](https://sofistikforyou.com/how-to-generate-a-xlsx-file/) gezeigt zu exportieren.
Alternativ können aber die Daten auch über den Code im `RESULTS` Modul automatisch exportiert werden. 
Im folgenden werden die Schnittgrößen (maximale Drucknormalkraft und dazugehöriges Biegemoment) in das [hier zur Verfügung gestelltes Excel](https://aiztok.github.io/SBB/docs/CONC_Knicken_stahlbetonstuetze.xlsx) zur Ermittlung der Schlankheit / Grenzschlankheit und des Biegemomentes unter Theorie 2. Ordnung gem. EN 1992-1-1, Kap. 5.8, exportiert. Dafür muss das xlsx im gleichen Ordner wie die Sofistik Datei abgelegt sein.
Der Code unten erzeugt neue bzw. überschreibt existierende Worksheets in der Exceldatei.

Exportieren von Biegemoment My,Ed und Normalkraft NEd ins Excel:
```javascript
+prog results
head 'Export ins xlsx Format'

! ein Doppeltes Dollar $$ ist ein umbruch, die Zeilen werden von der Software
! gelesen als ob es die gleiche Zeile wäre - praktisch bei langen Eingaben um es
! trotzdem auf dem Bildschirm zu sehen, ohne das Fenster zu verschieben
xlsx "CONC_Knicken_stahlbetonstuetze.xlsx" WS "SOFi_Erg_GZT" $$
ROW 1 COL 1

! Export der maximalen Druckkraft in der Wand und des dazugehörigen Biegemoments
grp 1 ! nur die Wandelemente

LC   NO 2120+2 ! min N
! Normalkräfte NEd von Stäben
TXTP SHOW SIGN OVLP AMAX EXTR  YES
BEAM TYPE    N STYP BEAM REPR DLST
! Biegemomente MEd von Stäben
AND
TXTP SHOW SIGN OVLP AMAX EXTR  YES
BEAM TYPE   MY STYP BEAM REPR DLST
! Biegemomente MEd von Stäben

end     
```

Exportieren von My,Ek unter GZG Quasi-ständige Einwirkungskombination:
```javascript
+prog results
head 'Export ins xlsx Format'

xlsx "CONC_Knicken_stahlbetonstuetze.xlsx" WS "SOFi_Erg_GZG" $$
ROW 1 COL 1

LC   NO 1420+9 ! Auch Quasi-Ständig My wird benötigt
TXTP SHOW SIGN OVLP AMAX EXTR  YES
BEAM TYPE   MY STYP BEAM REPR DLST

end  
```

Exportieren der Querschnittsfläche und Trägheitsmoments:
```javascript
+prog results urs:15
head 'Querschnittswerte'

xlsx "CONC_Knicken_stahlbetonstuetze.xlsx" WS "SOFi_QS" $$
ROW 1 COL 1

LC   SECT 1 ! Auswahl Querschnittsnummer
$ Nummer des Querschnitts
TXTP SHOW SIGN OVLP AMAX EXTR  YES
STRU TYPE  ENO ETYP SECT REPR DLST
$ Querschnittsfläche
AND
TXTP SHOW SIGN OVLP AMAX EXTR  YES
STRU TYPE    A ETYP SECT REPR DLST
$ Trägheitsmoment um die Y Achse
AND
TXTP SHOW SIGN OVLP AMAX EXTR  YES
STRU TYPE   IY ETYP SECT REPR DLST

END      
```



**Erzeugte Einwirkungskombinationen nachvollziehen**

Insbesondere bei Einwirkungskombinationen wo viele Einwirkungen kombiniert werden, ist es sinnvoll für die kritischen Schnitte (z.B. Auflager, Feldmitte) sich die automatisch erzeugten Einwirkungskombinationen anzeigen zu lassen um zu prüfen ob richtige Einwirkungen mit richtigen Sicherheits- und Kombinationsbeiwerten angesetzt worden sind.
Diese Kombination wird dann in `SOFILOAD` in ein Lastfall umgeformt werden und danach explizit in `ASE` berechnet werden. Ein sinnvolles wenn:
- mit einer linearen Berechnung die vermutlich maßgebenden Einwirkungskombinationen ermittelt werden und danach eine nichtlineare Berechnung nur für diese maßgebenden Kombinationen durchgeführt wird.
	*Hinweis: dies ist korrekt nur für System wo die Nichtlinearitäten das Verhalten des Gesamtsystems nicht ändern. Z.B. in Ordnung für Fachwerkträger, wo Druckstreben mittels Theorie 2. Ordnung gerechnet werden, aber nicht in Anwendbar bei Seilkonstruktionen, die müssen mittels Theorie 3. Ordnung berechnet werden, nichtlinear Steifigkeit der einzelnen Bauteile hat Einfluss auf das Gesamtsystem*
- um dazugehörige Schnittgrößen oder Verformungen schnell zu ermitteln
	*Hinweis: hier ist auch `AQUA` RSET  nützlich*


```javascript
+prog maxima
head 'Track EWK'
echo full

trac 2120+9 etyp beam elem 204 x 0 csav 801

end

+prog sofiload urs:10
head 'Umformen gespeicherte Kombination in Lastfall'

lc 900 type none
copy 801 comb

end

+prog ase urs:9
head 'Berechnung'

lc 900

end 

```
