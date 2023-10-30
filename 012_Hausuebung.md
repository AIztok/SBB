## Wahl der Bauwerks gem. PKZ

Das Bauwerk ist zu wählen entsprechend der letzten zwei PKZ-Zahlen XY

**XY = 00 bis 33**
[Valtschielbrücke](#Valtschielbrücke)

**XY = 34 bis 66**
[Lufthansa Wartungshalle V](#lufthansa-wartungshalle-v)

**XY = 67 bis 99**
[Pont La Fayette Paris](#pont-la-fayette-paris)


## Allgemein

Neben der Berechnung selber ist ein kurzer Bericht, mit folgenden Inhalt, zu erstellen :
- Beschreibung der Aufgabe
- Modellannahmen
- Lastansätze
- Ergebnisse (siehe unten)

Folgende Ergebnisse sollen graphisch dargestellt werden:
- Querschnitte mit Querschnittswerten
- FE-Modell
- Schnittgrößen (M, N, V) aus einzelnen Lastfällen
- Schnittgrößen aus Einwirkungskombinationen (GZT und GZG Charak + Quasi-Ständig)
- Verformungen (ux, uy, u) aus GZG Quasi-Ständig
- Auflagerreaktionen aus Einzellasten und GZT Einwirkungskombination

Die Abgabe erfolgt via Moodle-Kurs.
Abzugebende Unterlagen:
- Sofistik SSD file (.sofistik)
- PDF des Berichts

## Valtschielbrücke
[Valtschielbrücke in der Schweiz von Robert Maillart](https://de.wikipedia.org/wiki/Valtschielbr%C3%BCcke)
Bildaufnahme, Längsschnitt und Querschnitt entnommen aus [D. Proske, Kleiner Brückenführer Schweiz](https://link.springer.com/book/10.1007/978-3-658-32229-8)

![Valtschielbruecke_Bild.png](/docs/assets/images/Valtschielbruecke_Bild.png)

![Valtschielbruecke_LS.png](/docs/assets/images/Valtschielbruecke_LS.png)

![Valtschielbruecke_QS.png](/docs/assets/images/Valtschielbruecke_QS.png)

Aufgabe:
- Erstellen eines 2D Modells zur Ermittlung der Schnittgrößen und Verformungen

Vereinfachungen:
- Es soll nur die StB-Bogenkonstruktion in 2D Stabmodell abgebildet werden.
- Vouten im Überbauquerschnitt können vernachlässigt werden, es darf eine durchgehende Höhe von 112cm und Plattenstärke 15cm angenommen werden.
- Verjüngung des Bogens (von 29 bis 23cm) darf vernachlässigt werden.
- Für die in den Abbildungen nicht definiert Maße dürfen ingenieurmäßige Annahmen getroffen werden.
- Materialeigenschaften dürfen angenommen werden.

Folgende Einwirkungen sind zu berücksichtigen:
- Eigengewicht der tragenden Konstruktion
- Aufbau 1 cm Abdichtung + 12cm Asphalt
- Nutzlast: 5 kN/m2 (Feldweise aufgebracht)

# Lufthansa Wartungshalle V
Die Wartungshalle V, Stahlbetonstützen und Dach als Spannbeton
Bilder entnommen aus [Structurae](https://structurae.net/de/bauwerke/lufthansa-wartungshalle-v?r=/bauwerke/lufthansa-wartungshalle-v)

![Hanger_V_Bild.png](/docs/assets/images/Hanger_V_Bild.png)

Systembild der Stütze, teilweise entnommen von [ETH eQuilibrium](https://www.block.arch.ethz.ch/eq/drawing)
![Hanger_V_System.png](/docs/assets/images/Hanger_V_System.png)

Aufgabe:
- Erstellen eines 2D Modells zur Ermittlung der Schnittgrößen und Verformungen der Stützenkonstruktion
- Ermittlung von F1 = F2 = Gegengewicht um im GZT keine abhebenden Auflagerreaktionen zu bekommen.

Vereinfachungen:
- Es soll nur die StB-Stützenkonstruktion in 2D Stabmodell abgebildet werden.
- Annahme der Querschnitte der einzelnen Stäbe
- Für die in den Abbildungen nicht definiert Maße dürfen ingenieurmäßige Annahmen getroffen werden.
- Materialeigenschaften dürfen angenommen werden.

Folgende Einwirkungen sind zu berücksichtigen:
- Eigengewicht der tragenden Konstruktion
- Zugkraft der vorgespannten Dachkonstruktion

Die Zugkräfte im Dach werden wie folgend abgeschätzt:
```math
N = g * L^2 / (8*f)
```

*N - Zugkraft
g - Gleichlast
L - Spannweite
f - Durchhang*
$$g = B*H*\rho=10 m*0,3 m*25 kN/m=75 kN/m;  
L = 150 m;
f = 15 m$$
Zugkraft aus EGW:
$$N_k = 75 kN/m * (150m)^2/(8*15m) =  14000kN$$
Für die veränderliche Lasten (z.B. Schnee), nehmen wir an eine q = 10 kN/m und somit eine Zugkraft von:
$$ N_q = 10 kN/m * (150m)^2/(8*15m) =  1875kN
$$

## Pont La Fayette Paris

Stahlbetonfachwerkbrücke, Bilder entnommen aus [Structurae](https://structurae.net/de/bauwerke/pont-la-fayette).

![La_Fayette_Bild.png](/docs/assets/images/La_Fayette_Bild.png)

Hauptabmessungen:
- Fachwerkhöhe: 10,4m
- Stützweite ca. 60m
- Überbaubreite 20,4m -> Annahme: die Lasten teilt sich in Querrichtung 50/50 auf die Fachwerkträger links und rechts

Aufgabe:
- Erstellen eines 2D Modells zur Ermittlung der Schnittgrößen und Verformungen des Fahrwerkträgers - nur ein Feld (das reale Tragwerk hat drei Felder).

Vereinfachungen:
- Es soll nur den StB-Fachwerkträger in 2D Stabmodell abgebildet werden.
- Annahme der Querschnitte der einzelnen Stäbe
- Für die in den Abbildungen nicht definiert Maße dürfen ingenieurmäßige Annahmen getroffen werden.
- Materialeigenschaften dürfen angenommen werden.

Folgende Einwirkungen sind zu berücksichtigen:
- Eigengewicht der tragenden Konstruktion
- Aufbau 1 cm Abdichtung + 12cm Asphalt
- Nutzlast: 5 kN/m2
