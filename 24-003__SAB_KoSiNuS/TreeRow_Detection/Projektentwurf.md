# 01 Projektentwurf
TreeRow-Detection

## 1.1 Projektdefinition

Für die weitere Verarbeitung und Planung autonomer Systeme werden strukturierte Informationen über die reale Umgebung benötigt. Als Ausgangsdaten stehen Punktwolken und DSM-Daten zur Verfügung. Diese enthalten zwar die benötigten geometrischen Informationen, doch sind für weitere Planungs- und Automatisierungsprozesse in ungeeigneter Form.

Die Software soll diese Eingangsdaten automatisiert verarbeiten und daraus relevante Strukturen und geometrische Informationen ableiten, die anschließend als Grundlage für einen digitalen Anlagenplan bzw. weitere autonome Planungsprozesse verwendet werden können.

## 1.2 Projektziel

```text
Input --> Verarbeitung --> Ergebnis --> Nutzen
```

Ziel des Projekts ist die Entwicklung einer Softwarepipeline, die räumliche Eingangsdaten wie Punktwolken und DSM-Daten automatisiert verarbeitet, relevante Umgebungsstrukturen erkennt und daraus strukturierte geometrische Informationen für die Erstellung eines Anlagenplans bzw. die weitere Verarbeitung durch autonome Systeme erzeugt.

## 1.3 Nutzer und Anwendungsfall

```text
Benutzer
→ Entwickler / Ingenieur

Software
→ Verarbeitung der Messdaten

Ergebnis
→ vereinfachtes Anlagen-/Umgebungsmodell

Weiterverwendung
→ autonome Navigation / Planung / Visualisierung /
   technische Weiterverarbeitung
```

## 1.4 Input

| Anforderung                 | Information                                        | Umsetzung                                                                               |
| --------------------------- | -------------------------------------------------- | --------------------------------------------------------------------------------------- |
| Räumliche Informationen     | räumliche 3D-Punkte                                | PointCloud, DephMap                                                                     |
| Referenzsystem              | passendes Koordinatensystem                        | WG84                                                                                    |
| Oberflächenmodell           | Beschaffenheit und Struktur des Bodens             | DSM (Digital Surface Model), DTM (Digital Terrain Model), DEM (Digital Elevation Model) |
| Flug- bzw. Geoinformationen | Flugkonfigurationen, Flughöhe und -geschwindigkeit | GeoTIFF (Metadaten)                                                                     |
| Anlagengrenzen              | Betrachtungsbereich, Anlagengrenzen (GeoFence)     | GeoJSON                                                                                 |

## 1.5 Output

- Klassifizierungen von:
	- Stamm
	- Blätterdach
- Pflanzenreihe
- Hindernisse
- Boden

alle Pflanzenreihen werden als Vektor im folgenden Format gespeichert
=> DXF (Drawing Exchange Format)
=> DWF (Drawing Format)
## 1.6 Funktionale Anforderungen

- A-01: die Input-Daten werden korrekt eingelesen und verarbeitet
- A-02: alle relevanten Datenpunkte sollen aus dem Rohdatensatz ausgefiltert werden
- A-03: der ganzheitliche Boden soll richtig erkannt und abgegrenzt werden (Ground-Segmentation)
- A-04: die Pflanzenreihen sollen richtig erfasst, und als Polyline vereinfacht werden
- A-05: es soll bestenfalls jede einzelne Pflanze korrekt erfasst werden (wenn möglich als einzelnes Individuum/Objekt)
	- korrekte Erfassung der Pflanze und ihrer strukturellen Einteilung (Blätterdach, Stammansatz)
- A-06: restliche nicht klassifizierte Objekte oberhalb vom Boden sollen vorerst als "Hindernisse" gelabelt werden
- A-07: die Verarbeitungs-Pipeline soll parametrisierbar, konfigurierbar und nach Funktionalität einstellbar sein
- A-08: die Ausgabe soll als Drawing-Format oder allgemein CAD-taugliches Format erfolgen
- A-09: alle Feldbestandteile müssen korrekten Bezug zum Referenzsystem haben (richtiges Koordinatensystem --> Kartenprojektion)
## 1.7 Nichtfunktionale Anforderungen

- NA-01: kein vollständiges, hochauflösendes Pflanzenmodell rekonstruieren --> mindestens ein Primitive Model
- NA-02: keine hochgenaues Polyline für die Pflanzreihe bzw. dessen Verlauf
- 