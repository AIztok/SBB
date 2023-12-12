## Angaben gem. PKZ

Letzte Zahl = Y
Vorletzte Zahl = X

Abmessungen und Baustoffe:

 | XY | Betongüte |    Ort    |
 |---|---|---|
 | 0-33 | C30/37 | Lienz   |  
 | 34-66 | C25/30 |  Bregenz  |
 | 67-99 | C35/45 |  St. Pölten  |

 Abmessung a:
```math
a = 5 + (9 / (6+Y))
```

Höhe h:
```math
Y = gerade Zahl -> h = 4,0m
Y = ungerade Zahl -> h = 5,0m 
```


## Allgemein

Neben der Berechnung selber ist Bericht gem. der [ONR24005](https://moodle.fh-campuswien.ac.at/pluginfile.php/1388302/mod_folder/content/0/Statische%20Berechnungen_Dokumentation_Umfang_Inhalt.pdf?forcedownload=1), mit folgenden Inhalt, zu erstellen :
- Beschreibung der Aufgabe
- Modellannahmen
- Lastansätze
- Ergebnisse (siehe unten)
- Bewehrungsskizze für Decke inkl. Unterzug und Stütze
- Plausibilisierung der Biegebemessung des Unterzugs im kritischen Schnitt (z.B. Feldbereich) mit händischer Bemessung und Berechnung der Biegemomente

Folgende Ergebnisse sollen graphisch dargestellt werden:
- Querschnitte mit Querschnittswerten
- FE-Modell
- Schnittgrößen (M, N, V für Stabelemente bzw. mxx,myy,Hauptquerkraft für Flächenelemente) aus einzelnen Lastfällen
- Schnittgrößen aus Einwirkungskombinationen (GZT und GZG Charak + Quasi-Ständig)
- Verformungen (uz) der Decke/Unterzuge aus GZG Quasi-Ständig
- Auflagerreaktionen aus Einzellasten und GZT Einwirkungskombination
- Bemessung der Unterzugs und der Stahlbetonplatte (GZT und GZG Rissweiten)

Die Abgabe erfolgt via Moodle-Kurs.
Abzugebende Unterlagen:
- Sofistik SSD file (.sofistik)
- PDF des Berichts

## Aufgabe

Es ist eine Garage wie an der unteren Abbildung dargestellt zu modellieren und zu bemessen.

![032_Garage_Plan.png](/docs/assets/images/032_Garage_Plan.png)

Aufgabe:
- Erstellen eines 3D Modells zur Ermittlung der Schnittgrößen, Verformungen und zur Bemessung
- Zu modellieren sind:
	- Wände
	- Stützen
	- Unterzug
	- Decke
- Mit der Software zu bemessen sind folgende Bauteile:
	- Stütze
	- Unterzug
	- Decke

Vereinfachungen:
- die Auflager (Fundierung) darf vereinfacht als Auflager (Linien- und Punktauflager) definiert werden

Folgende Einwirkungen sind zu berücksichtigen:
- Eigengewicht der tragenden Konstruktion
- Aufbau:
	- 25cm Substratschicht
	- 12 cm Dränschüttung
	- Wurzelschutz
	- Abdichtung 2-Lagig
	- Dampfsperre
	- 9 cm Gefälleestrich

- Schneelast (Feldweise ansetzten)
- Windlast (beide Richtungen)
# Bewehrungsskizze

Bewehrungsskizzen können per Hand oder auch mit der EDV erstellt werden. Es soll nachvollziehbar sein, welche Bewehrung wird anhand der Bemessungsergebnisse vorgesehen und sollte in der Praxis die Grundlage für den Bewehrungskonstrukteur bilden. 

Beispiel einer händische Bewehrungsskizze:
![032_Bewehrungsskizze_Bspl.png](/docs/assets/images/032_Bewehrungsskizze_Bspl.png)
