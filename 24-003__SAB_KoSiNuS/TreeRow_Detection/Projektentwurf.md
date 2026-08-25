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
→ verarbeitet Messdaten

Ergebnis
→ Anlagen-/Umgebungsmodell

Weiterverwendung
→ autonome Navigation / Planung / Visualisierung /
   technische Weiterverarbeitung
```

## 1.4 Input

- Räumliche Informationen -> 3D-Punkte (PointCloud, DepthMap)
- Referenzsystem -> passendes Koordinatensystem (WG84)
- Oberflächenmodell -> DSM (Digital Surface Model), DTM (Digital Terrain Model), DEM (Digital Elevation Model)
Flug- bzw. Geoinformationen -> Metadaten
- Anlagengrenzen -> GeoFence

## 1.5 Output

## 1.6 Funktionale Anforderungen

## 1.7 Nichtfunktionale Anforderungen