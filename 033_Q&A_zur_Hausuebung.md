Inhalt:
[Definition von Windlasten](#definition-von-Windlasten)

# Definition von Windlasten
Die Windlasten können mit verschiedenen Ansätzen auf das Modell aufgebracht werden:
- Software interne Ermittlung der Windlast und Definition der Außendruckbeiwerte
- "Händische" Eingabe mit expliziter Definition der Flächenlasten

Die .sofistik Datei des Beispiels kann [hier](https://aiztok.github.io/SBB/docs/FH_SBB_SSD-Beispiel_Windlasten.sofistik) heruntergeladen werden.
![033_Lastbild.png](/docs/assets/images/033_Lastbild.png)

## Programminterne Windlasten
Die Windlasten können seitens der Software anhand von Eingaben der Geländekategorie, Höhe, Basisgeschwindigkeit inkl. der Bereiche ermittelt werden:
```code
+prog sofiload
head 'Windlasten'

!*!Label Windlasten gem. Sofistik
! Erstens werden die Flächen auf die die Windlasten wirken definiert
! Es wird dabei auf die Flächennummern Bezug genommen
LC 100 TITL 'DEF. DER FLÄCHEN' TYPE NONE
    AREA SAR NO 10 TYPE WIND P1 1.0 TITL 'Vorne'
    AREA SAR NO 11 TYPE WIND P1 1.0 TITL 'Linke Seite'
    AREA SAR NO 13 TYPE WIND P1 1.0 TITL 'Rechte Seite'
    AREA SAR NO 12 TYPE WIND P1 1.0 TITL 'Hinten'
    AREA SAR NO  2 TYPE WIND P1 1.0 TITL 'Dach'

LC 111 TITL 'WIND +X' TYPE W
    ! CLAS: geländerkategorie lt. EN 1991-1-4 Tab. 4.1
    ! DX bzw. DY sind wesentlich für die Richtung
    ! GH: Geländehöhe
    ! VB0:Basiswindgeschwindigkeit vb,0 z.B. lt. ÖNB 1991-1-4 Tab. A.1
    WIND CODE 1991 CLAS III DX +1.0 GH 10[m] VB0 25.1[m/sec]
    COPY NO 100 FACT +1.0 TYPE WIND

LC 112 TITL 'WIND +Y' TYPE W
    WIND CODE 1991 CLAS III DY +1.0 GH 10[m] VB0 25.1[m/sec]
    COPY NO 100 FACT +1.0 TYPE WIND
```

## Händische Eingabe
Die Flächenlasten können auch explizit nach den Bereichen gem. EN 1991-1-4 definiert werden, anbei das Beispiel für die Richtung +X:

```code
+prog sofiload
head 'Windlasten'
!*!Label Windlasten "Händisch"
! Alternativ können Windlasten auch "händisch" eingegaben werden
LC 200 TITL 'Wind +X Händisch' TYPE W
let#cqb 1.75*(15/10)^(0.29)*0.39 !
    AREA SAR NO 10  TYPE PXX P1 +0.7*#cqb ! Vorne Bereich D
    AREA AUTO       TYPE PYY P1 -1.2*#cqb X1 0    0     0     X2 0    0         -5      X3 5/5  0       -5  X4 5/5  0       0 ! Linke Seite Bereich A
    AREA AUTO       TYPE PYY P1 -0.8*#cqb X1 5/5  0     0     X2 5/5  0         -5      X3 5    0       -5  X4 5    0       0 ! Linke Seite Bereich B
    AREA AUTO       TYPE PYY P1 -0.5*#cqb X1 5    0     0     X2 5    0         -5      X3 20   0       -5  X4 20   0       0 ! Linke Seite Bereich C
    AREA AUTO       TYPE PYY P1 +1.2*#cqb X1 0    5     0     X2 0    5         -5      X3 5/5  5       -5  X4 5/5  5       0 ! Rechte Seite Bereich A
    AREA AUTO       TYPE PYY P1 +0.8*#cqb X1 5/5  5     0     X2 5/5  5         -5      X3 5    5       -5  X4 5    5       0 ! Rechte Seite Bereich B
    AREA AUTO       TYPE PYY P1 +0.5*#cqb X1 5    5     0     X2 5    5         -5      X3 20   5       -5  X4 20   5       0 ! Rechte Seite Bereich C
    AREA SAR NO 12  TYPE PXX P1 +0.3*#cqb ! Hinten Bereich E
    AREA AUTO       TYPE PZZ P1 -1.8*#cqb X1 0    0     -5    X2 0    1.25      -5      X3 5/10 1.25    -5  X4 5/10 0       -5 ! Dach Bereich F
    AREA AUTO       TYPE PZZ P1 -1.8*#cqb X1 0    5     -5    X2 0    5-1.25    -5      X3 5/10 5-1.25  -5  X4 5/10 5       -5 ! Dach Bereich F
    AREA AUTO       TYPE PZZ P1 -1.2*#cqb X1 0    1.25  -5    X2 0    5-1.25    -5      X3 5/10 5-1.25  -5  X4 5/10 1.25    -5 ! Dach Bereich G
    AREA AUTO       TYPE PZZ P1 -0.7*#cqb X1 5/10 0     -5    X2 5/10 5         -5      X3 5/2  5       -5  X4 5/2  0       -5 ! Dach Bereich H
    AREA AUTO       TYPE PZZ P1 -0.2*#cqb X1 5/2  0     -5    X2 5/2  5         -5      X3 20   5       -5  X4 20   0       -5 ! Dach Bereich I

end     

```