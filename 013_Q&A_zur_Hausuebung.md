[Last unter einem Winkel?](#last-unter-einem-winkel)

[Beispiel: Bogen u. nutzen von Schleifen](#beispiel:-bogen-u.-nutzen-von-schleifen)

[Fehlerquelle Maxima](#fehlerquelle-maxima)


# Last unter einem Winkel
Knotenlasten oder auch Linienlasten die unter einem Winkel zu den X/Y/Z Achsen stehen, können wie folgend eingegeben werden (pro Komponente):
```code
! Knotenlast in X-Y Ebene
lc 2 type Q titl 'Punktlast'
! mit let wird eine lokale (nur in dem +prog xyz aktiv) Variable definiert
let#spt 24   !Strukturpunktnummer angeben
let#ang 20   ![°] Angabe der Winkels in grad
let#p   1000 ![kN9 Angabe Lastgröße
! horizontale komponente der Kraft
node #spt type pxx p1 cos(#ang)*#p
! vertikale komponente der Kraft
node #spt type pyy p1 sin(#ang)*#p   
```

![Knotenlast_winkel.png](/docs/assets/images/Knotenlast_winkel.png)

# Beispiel: Bogen u. nutzen von Schleifen

![[013_Fachwerk.png]]
Anbei ein Beispiel eines Fachwerkträgers dessen Obergurt einem Bogen folgt und das Modell mit Verwendung von Schleifen (loops) erzeugt wird um sich die Arbeit zu erleichtern:

```code
!#!Chapter Modell
+prog aqua
head 'Material- und Querschnittsangabe'
$----------------------------------------------------------------------------------
!*! Angabe der Norm
NORM OEN en199X-200X unit 0 !
$----------------------------------------------------------------------------------
!*! Umfang der Ausgabe
echo full extr
$----------------------------------------------------------------------------------
!*! Konstruktionsstahl
STEE NO 301 TYPE S 355 titl 'S355'

! Ober- und Untergurt
sect 1 mno 301
prof 1 rhs 160 160 8
! Vertikale
sect 2 mno 301
prof 2 rhs 100 100 8
! Diagonale
sect 3 mno 301
prof 3 rhs 100 100 8

end

head 'Model'
page unii 0 unio 0

! Definition der Parameter für die Vernetzung
ctrl hmin 0.5 ! Netzgröße in [m]
ctrl mesh 1 ! Stabelemente

! Definition des FE-Systems (gruppen divisor gdiv, richtung des eigengewichts)
syst 2D gdiv 1000 gdir posy

! Definition der Strukturpunkte mit Auflagerbedingungen
! Es ist möglich eine wiederholende Eingabe mittels Schleifen zu erledigen.
! Die folgende Eingabe generiert Punkte nummeriert mit
! 1 bis 11 aber mit einem Schritt von 1, also 1,2,3,4,5,6,7,8,9,10,11
! Die Koordinate X beginnt dabei bei 0 und hat einen Schritt von 2m.
! Die Koordinaten der 11 Punkte sind also: 0,2,4,6,8,10,12,14,16,18,20
! Punkte Untergurt
spt (1 11 1) x (0 2) y 0

! Oder mit einer eigenen Schleife
! Punkte Obergurt, folgt einem Bogen
let#r 30 ![m] Bogenradius
let#s (11-1)*2 ![m] Sehnenlänge

let#a 2*asin(#s/(2*#r)) ! Mittelpunktswinkel
prt#a ! Druck im Bericht (unter Input Data) den Wert aus - zur Kontrolle

let#ys #r*cos(#a/2) $ radius minus Bogenstich
prt#ys

loop#i 11
    spt 21+#i x 0+2*#i y -1-(sqr((#r)^2-(-#s/2+0+2*#i)^2)-#ys)
endloop

! Auflager explizit definieren durch das bearbeiten
! von existierenden Strukturpunkten
spt -1 fix ppmx
spt -11 fix pymx

! Definition der Strukturlinien
! Untergurt
sln (1 10 1)   npa (1 1)  npe (2 1)  sno 1 grp 1 titl 'Untergurt'   styp 'b.e'
! Obergurt
loop#i 10
    sln 11+#i npa 21+#i npe 22+#i sno 1 grp 2 styp 'b.e'
    slnb r #r ! gebogener Stab
endloop
! Vertikalen
loop#i 11
    sln 21+#i npa 1+#i npe 21+#i sno 2 grp 3 titl 'Vertikale' styp 't.e' ! t=fachwerkstab (my=vz=0)
endloop
! Diagonalen
sln (41 45 1) npa (21 1) npe (2 1)  sno 3 grp 4 titl 'Diagonale'   styp 't.y'
sln (46 50 1) npa (6 1) npe (27 1)  sno 3 grp 4 titl 'Diagonale'   styp 't.y'

end  
```

Das komplette Beispiel kann  [hier](https://aiztok.github.io/SBB/docs/013_Beispiel_Q&A_HUE.dat) heruntergeladen werden.


# Fehlerquelle Maxima
Die folgende Warnung bedeutet, dass für die gewählten Einwirkungen (`ACT`) keine zugeordneten Lastfälle gefunden wurden:
![013_Warnung_Maxima.png](/docs/assets/images/013_Warnung_Maxima.png)

Z.B. in Maxima wird das Programm aufgefordert eine Kombination anhand der gewählten Einwirkungen zu erstellen:
```code
+prog maxima
head 'Ergebniskombination'
! Output control
echo chck val full

COMB 1 rare BASE - $ kombination charakteristisch
    ACT G_1 ! EGW
    ACT G_2 ! Ständige Lasten
    ACT Q ! Nutzlast
    ACT S ! Schnee  

end
```

Aber in `Sofiload` fehlt die Zuweisung der Last (z.B. LC2) zu einer Einwirkung

```code
1 +prog sofiload
2 head 'Definition der Lasten'
3 !*!Label Ständige
4 lc 2 titl 'Ausbaulasten'
5    ! Flächenlast kann über Bezug auf Gruppennummer definiert werden
6    quad grp 10 type pg p 2.5
7 end
```

Für den Beispiel wäre die 4. Zeile im oberen Codeblock wie folgend zu korrigieren:
```
4 lc 2 type G_2 titl 'Ausbaulasten'
```

Alternativ könnte auch wie folgend vorgegangen werden:
- im Sofiload werden die Lasten keiner Einwirkung zugewiesen (hier sollte dann `type none` stehen)
```code
4 lc 2 type none titl 'Ausbaulasten'
```
- in Maxima werden explizit die Lastfälle der Einwirkung zugewiesen (beispielhaft für G_2, LC2 dargestellt):
```code
+prog maxima
head 'Ergebniskombination'
! Output control
echo chck val full

COMB 1 rare BASE - $ kombination charakteristisch
    ACT G_1 ! EGW
    ACT G_2 ! Ständige Lasten
	    LC 2 ! explizit der Einwirkung zugewiesen Lastfall
    ACT Q ! Nutzlast
    ACT S ! Schnee  

end
```
# Graphische Darstellung der Ergebnisse

Die folgenden graphische Darstellung zeige wie:
- im Graphix die Schnittgrößen für die erstellen Einwirkungskombinationen dargestellt werden

![013_Grafix_Fores.gif](/docs/assets/images/013_Grafix_Forces.gif)

- im Graphix die Auflagerreaktionen dargestellt werden

![013_Grafix_Reactions.gif](/docs/assets/images/013_Grafix_Reactions.gif)

- diese Ergebnisse in Text Forma umgewandelt werden und im Report browser dargestellt werden 

![013_Grafix_Report.gif](/docs/assets/images/013_Grafix_Report.gif)
