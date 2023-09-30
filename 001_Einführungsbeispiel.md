Das Einführungsbeispiel ist ein einfacher Halbrahmen mit einer Wand und einer Decke.

## Inhalt
[Was passiert im Hintergrund?](#was-passiert-im-hintergrund)

[Erstellen des Projekts](#erstellen-des-projekts)

[Basis Input](#basis-input)

[Materialangabe](#materialangabe)

[Querschnittsdefinition](#querschnittsdefinition)

[Modelleingabe](#modelleingabe)


## Was passiert im Hintergrund?
FEA - Finite Element Analysis - Finite Elemente Berechnung

Um die Grundlagen einer Finite Elemente Berechnung an dem einfachen Einführungsbeispiel mit Stabelementen besser zu verstehen, wurde in einem [Jupyter](https://jupyter.org/) Workbook die Berechnung der Schnittgrößen und Verschiebungen Schritt per Schritt durchgeführt. Die Anzahl der Finite Elemente wurde klein gehalten, damit die Übersicht nicht verloren geht.
Damit keine Installation von Python erforderlich ist (aber dafür ein Account auf Google :)) wurde der Code auf Google Colab hochgeladen:

[Einführungsbeispiel in Python - Link zu dem Jupyter workbook auf Google Colab](https://colab.research.google.com/drive/1Yio_5SlEL6frEUguNEkPdDJhUXO9Q-f7?usp=sharing)

Das File / Workbook kann auch [hier](https://aiztok.github.io/SBB/docs/FH_SBB_FEM_Example.ipynb) von GitHub heruntergeladen werden. 


## Erstellen des Projekts
Wir öffnen das Programm SOFiSTiK SSD 2023 (oder auch neuer):
![SSD vorlage](/docs/assets/images/SSD_Vorlage.png)

## Basis input

```
+prog template urs:1
head 'Einfuehrung'

! mit "!" oder "$" können Kommentare geschrieben werden
<text>
FH Campus Wien
SBB
Beispiel: Einfuehrungsbeispiel
Datum der letzten Änderung: 02.08.2023
Autor: AIztok
Sofistik Version: 2023

Log der Änderungen:
/

</text>
end
```



## Materialangabe


```
+prog aqua urs:43.1
head 'Materialangabe'
$----------------------------------------------------------------------------------
!*! Angabe der Norm
NORM 'OEN' 'en1992-2004' CAT 'A'  unit 0 !
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

```
+prog aqua urs:2
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
Es wird das Modul SOFIMSHC verwendet, dass mit Strukturelementen arbeitet.
Es werden z.B.:
- Strukturpunkte (SPT)
- Strukturlinien (SLN)
- Strukturflächen (SAR)
erstellt.
Die Vernetzung der Strukturelement erfolgt nach der Berechnung des Moduls im Hintergrund.
Alternativ kann auch der Modul SOFIMSHA verwendet werden, wo direkt mit Finiten Elementen gearbeitet wird. 

```
+prog sofimshc urs:3
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

## Definition der Einwirkungen




## Definition der Lasten



## Lineare Berechnung



## Überlagerung der Ergebnisse - Erstellung der Einwirkungskombinationen
Die linear ermittelten Schnittgrößen und Verschiebungen werden für die Ermittlung der Einwirkungskombinationen (EWK) linear überlagert mit entsprechenden Sicherheits- und Kombinationsfaktoren.

Wichtig ist anzumerken, dass SOFiSTiK die Ergebnisse der Überlagerungen in Lastfälle (LF) speichert.
Jedoch muss darauf geachtet werden, dass nicht vorhandene LFs (sei es tatsächlich eingegebenen Lasten oder Ergebnisse von Kombinationen) überschrieben werden - also nicht verwendete LFs verwendet werden. Hier hilft ein klares Nummerierungs-system von LFs.


## Bemessung der Stabelemente

Die Bemessung der Stabelemente erfolgt im Modul AQB. In der zweiten Übung wird auch die Bemessung der Flächenelemente mit dem Modul BEMESS vorgestellt.


