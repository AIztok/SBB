# 2. Hausübung
## Angaben gem. PKZ

Letzte Zahl = Y
Vorletzte Zahl = X

**X <= 5**
2-Feldträger

**X >5**
3-Feldträger

**Y <= 5**
Das Zwischenauflager des 2-Feldträgers (Stütze) soll mit modelliert werden (Stab) mit der Geschosshöhe gem. Ihrer Planung bei KE2. Das Auflager am Fuß der Stütze kann eingespannt angenommen werden.

**Y >5**
Die Zwischenauflager (z.B. Stützen) können als Punktauflager angenommen werden (z.B. `SPT ... FIX PZ`).

## Allgemein

Neben der Berechnung selber ist ein kurzer Bericht, mit folgenden Inhalt, zu erstellen :
- Beschreibung der Aufgabe
- Modellannahmen, Auszug aus dem KE2 Grundriss des Bereichs mit Unterzug
- Lastansätze
- Ergebnisse (siehe unten)

Folgende Ergebnisse sollen graphisch dargestellt werden:
- Querschnitte mit Querschnittswerten
- FE-Modell
- Schnittgrößen (M, N, V) aus einzelnen Lastfällen
- Schnittgrößen aus Einwirkungskombinationen (GZT und GZG Charak + Quasi-Ständig)
- Verformungen (ux, uy, u) aus GZG Quasi-Ständig
- Auflagerreaktionen aus Einzellasten und GZT Einwirkungskombination
- Bemessung der Unterzugs und der Stahlbetonplatte (GZT und GZG Rissweiten)

Die Abgabe erfolgt via Moodle-Kurs.
Abzugebende Unterlagen:
- Sofistik SSD file (.sofistik)
- PDF des Berichts

## Aufgabe

Sie wählen einen Bereich mit einem Unterzug aus StB (wenn nur Stahlbetonverbund, dann als StB annehmen) Ihres Bauwerks aus der LVA Konstruktiver Entwurf 2.

Die Wände und Stützen können als Auflager modelliert werden. Parallel liegende Unterzüge können ebenfalls als Auflager angenommen werden werden.

Der Bereich ist ggf. nach Angabe PKZ in ein 2-Feld oder 3-Feld System umzuwandeln - hier können die gleichen Spannweiten angenommen werden.

Nur als Stabmodell modelliert (Achtung auf die Festhaltungen wenn als 3D System gerechnet - Verdrehungen um die x, y oder z Achse können beim `SPT .... FIX PPMX` festgehalten werden)

![022_Bild_2.png](/docs/assets/images/022_Bild_2.png)

Für Bonus Punkte kann auch die Platte mitmodeliert werden.

![022_Bild_1.png](/docs/assets/images/022_Bild_1.png)

Das rechteckige Netz der Vernetzung der Platte kann bei `SAR` mit `mctl regm` vorgegeben werden. Wenn nicht, kann es etwas unregelmäßig sein, was bei ausreichender Feinheit des Netztes und keinen sehr "scharfkantigen" Formen noch trotzdem übereinstimmende Ergebnisse liefert.

![022_Bild_4.png](/docs/assets/images/022_Bild_4.png)

In jedem Fall immer drauf denken, dass das Berechnungsmodell wie unten ausschaut, also der Unterzug ist ein Stab, mit nur einer Dimension in seiner Achse und "eingebettet" bzw. vernetzt mit der Platte, Rest ist Visualisierung. 

![022_Bild_3.png](/docs/assets/images/022_Bild_3.png)
