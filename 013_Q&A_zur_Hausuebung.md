[Last unter einem Winkel?](#last-unter-einem-winkel)

[Beispiel: Bogen u. nutzen von Schleifen](#beispiel:-bogen-u.-nutzen-von-schleifen)

[Fehlerquelle Maxima](#fehlerquelle-maxima)

[Graphische Darstellung der Ergebnisse](#graphische-darstellung-der-ergebnisse)

[Umfang Ausgabe im Report](#Umfang-Ausgabe-im-Report)
# Last unter einem Winkel
Knotenlasten oder auch Linienlasten die unter einem Winkel zu den X/Y/Z Achsen stehen, können wie folgend eingegeben werden (pro Komponente):
```js
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

```js
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
```js
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

```js
1 +prog sofiload
2 head 'Definition der Lasten'
3 !*!Label Ständige
4 lc 2 titl 'Ausbaulasten'
5    ! Flächenlast kann über Bezug auf Gruppennummer definiert werden
6    quad grp 10 type pg p 2.5
7 end
```

Für den Beispiel wäre die 4. Zeile im oberen Codeblock wie folgend zu korrigieren:
```js
4 lc 2 type G_2 titl 'Ausbaulasten'
```

Alternativ könnte auch wie folgend vorgegangen werden:
- im Sofiload werden die Lasten keiner Einwirkung zugewiesen (hier sollte dann `type none` stehen)
```js
4 lc 2 type none titl 'Ausbaulasten'
```
- in Maxima werden explizit die Lastfälle der Einwirkung zugewiesen (beispielhaft für G_2, LC2 dargestellt):

```js
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

In den Beispielen wurde die graphische Ausgabe im `REPORT BROWSER` mittels `+prog WING` bereits in einem sauberen Code dargestellt. Dieser Code kann natürlich weiter an anderen Beispielen (mit ggf. geringeren Anpassungen) angewendet werden.
Im Folgenden wird aber gezeigt, wie man die Bilder über das Programm `GRAPHICS` erstellen kann und dann in einen Code umformen kann.

Die Vorgehensweise ist wie im unteren GIF dargestellt:
- öffnen des Programms `Graphics`
- Auswahl welche Ergebnisse dargestellt werden sollen (1. Reiter)
- Auswahl des Lastfalls (2.Reiter)
- nicht im GIF dargestellt: im 3.Reiter kann die Art der Darstellung gewählt werden
- nicht im GIF dargestellt: im 4.Reiter können mehrere Graphiken/Blätter/Bilder erstellt werden

Beispiel wie im Graphic die Schnittgrößen für die erstellen Einwirkungskombinationen dargestellt werden:

![013_Grafix_Fores.gif](/docs/assets/images/013_Grafix_Forces.gif)

Beispiel im Graphic die Auflagerreaktionen dargestellt werden:

![013_Grafix_Reactions.gif](/docs/assets/images/013_Grafix_Reactions.gif)

Um die gewählten Ergebnisse in ein Code zu transformieren wir wie folgend vorgegangen:
- Rechtklick auf das Bild
- Auswahl "Kopieren ins Clipboard" / z.B. "Aktuelles Bild"
- Im SSD ein Text-Eingabe Task auswählen und mit *Steu+V* den Text einkopieren
- Im GIF wird noch mittels *Steu+Q+T* der Code ins Englische geändert (kann auch Deutsch bleiben)
- Berechnen des Tasks
- Anzeigen im `Report Browser`

![013_Grafix_Report.gif](/docs/assets/images/013_Grafix_Report.gif)

# Umfang Ausgabe im Report

Der Umfang der Ausgabe im Bericht (`Report`) kann in jedem Berechnungsmodul (prog) über die Kommende `ECHO` gesteuert werden. 
Unten als Beispiel in AQUA, wo wir z.B. statt der sehr umfangreichen Ausgabe (echo full extr) nur die basis Ausgabe für Material (echo mat yes) und umfangreiche Ausgabe für Querschnitte (echo sect full) ausgeben wollen. 

```js
+prog aqua
head Querschnitte

!echo full extr ! maximale Ausgabe im Bericht
echo mat yes    ! Material
echo sect full   ! Querschnitte
!... yes - basis Ausgabe
!... full - umfangreiche Ausgabe
!... extr - sehr umfangreiche Ausgabe


ctrl rest 2 ! damit vorheriges aqua nicht gelöscht

srec 1 h 0.3[m] b 1000 so 4.5[cm] su 4.5[cm] mno 1 mrf 101 titl 'Decke QS'
srec 2 h 0.25[m] b 1000 so 4.5[cm] su 4.5[cm] mno 1 mrf 101 titl 'Wand QS'

end      
```

Welche Ergebnisse wir anzeigen können, kann im Handbuch gelesen werden (im Sofistik F1 drucken und auf die ECHO Zeile gehen).

![013_Echo_command.png](/docs/assets/images/013_Echo_command.png)

Zusätzlich können im `Report` selber noch einzelne Ausgaben ausgeblendet werden, dadurch dass auf das "Buch" gedruckt wird:

![013_Report_open-close.png](/docs/assets/images/013_Report_open-close.png)
