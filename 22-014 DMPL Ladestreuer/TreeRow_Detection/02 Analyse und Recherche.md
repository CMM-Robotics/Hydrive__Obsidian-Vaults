## 2.1 Input analysieren

![[DSM-Vergleichsbilder.png|387]]

### relevante Kartenbausteine

| Kartenbaustein                                  | Nutzungsmöglichkeit                                                                                                                                                                              | Datenformat                           |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------- |
| PointCloud                                      | essentielle Datengrundlage                                                                                                                                                                       | LAS/LAZ       PLY                 CSV |
| DSM (Digital Surface Model)                     | Abgrenzung des Bodens und all seiner bodennahen Objekten von den restlichen Daten                                                                                                                | GLB                                   |
| DEM (Digital Elevation Model)                   | Validierung von sonstigen Objekten/Hindernissen in der Map. Differenz zwischen DEM und DSM bietet Rückschlüsse auf relevante Feldobjekte/Hindernisse                                             | GeoTIFF<br>JPEG<br>PNG<br>KMZ         |
| NDVI (Normalized Differential Vegetation Index) | der Vegetation Index kann zur genaueren Validierung potentiell entdeckter Pflanzenreihen genutzt werden. Bzw. zur Abgrenzung zwischen anlagenrelevanter Pflanzgebiete und Arbeitsgebieten/-wegen | GeoTIFF<br>JPEG<br>PNG<br>KMZ         |
| Orthofotos                                      | Bilderkennung für die grobe Detektion von Pflanzenreihen --> HeatMap für relevante Beobachtungsbereiche                                                                                          | GeoTIFF<br>JPEG<br>PNG<br>KMZ         |

| Kartenbaustein                                  | Eigenschaften |
| ----------------------------------------------- | ------------- |
| PointCloud                                      |               |
| DEM (Digital Elevation Model)                   |               |
| DSM (Digital Surface Model)                     |               |
| NDVI (Normalized Differential Vegetation Index) |               |
| Orthofotos                                      |               |

# 2.2 nötige Verarbeitungsschritte

1. Geeignete Eingangsdaten aufnehmen
2. Punktwolke anhand der Aufnahmen erzeugen
3. Punktwolke für die Analyse aufbereiten
4. Bodenpunkte von den restlichen Punkten trennen
5. neuen Betrachtungsbereich (Vegetationsbereich) der Punktwolke erzeugen
6. einzelne Pflanzenstrukturen erfassen und segmentieren
7. einzelne Pflanzencluster klassifizieren
8. Repräsentativpunkt/-geometrie der Pflanzencluster bestimmen
9. alle Repräsentanten in einer Reihe erfassen -> Reihenkandidaten
10. alle Reihenkandidaten anhand von Kriterien validieren
11. Reihenkandidaten klassifizieren
12. Reihenverlauf geometrisch abstrahieren
13. die gesamten Punkte aller Polylines werden in ein Datenformat gepackt

# 2.3 Verfahren und Algorithmen recherchieren

## 2.3.1 Datenerfassung
"Geeignete Eingangsdaten aufnehmen"

Als Eingangsdaten sollen Flugbilder der jeweiligen Anlage dienen.
Für die Datenerfassung wird folgende Drohne genutzt: **DJI Mavic 3M**

Für die Aufnahme der Anlage Pillnitz wurden folgende Flugkonfigurationen gewählt:
1. Überlappung
	1. Front: 90
	2. Side: 80
2. Geschwindigkeit: 1,3 m/s
3. Höhe: 30 m
4. Auflösung: 1,38 cm/pixel
5. Course Angle: 40°

Das Abflugmuster sieht folgendermaßen aus:


## 2.3.2 Punktwolkengenerierung
"Punktwolke anhand der Aufnahmen erzeugen"

Um aus den Serienbildern der Anlage räumliche Datenpunkte zu erhalten, wird die Photogrammmetrie mit WebODM angewendet.

Für die Generierung werden folgende Konfigurationen:

| Kategorie              | Einstellung                           | RGB                        | Multispectral              |
| ---------------------- | ------------------------------------- | -------------------------- | -------------------------- |
| Structure From Motion  | `feature-quality`                     | high                       | high                       |
|                        | `min-num-features`                    | 20000 for crops/vegetation | 20000 for crops/vegetation |
| Point Cloud            | `pc-quality`                          | high                       | medium                     |
| Digital Elvation Model | `dsm`                                 | Enabled                    | Enabled                    |
|                        | `dtm`                                 | Disabled                   | Disabled                   |
|                        | `dem-resolution`                      | 5                          | 5                          |
| Meshing                | `mesh-size`                           | 300000                     | 200000                     |
| Orthophoto             | `orthophoto-resolution (float > 0.0)` | 4.61                       | 5                          |
|                        | `fast-orthophoto`                     | Disabled                   | Disabled                   |
| Tiles                  | `tiles`                               | Enabled                    | Enabled                    |
|                        | `3d tiles`                            | Enabled                    | Disabled                   |


## 2.3.3 Punktwolkenaufbereitung
"Punktwolke für die Analyse aufbereiten"

Die generierte Punktwolke muss für die Pipeline aufbereitet werden.

Folgende Verfahrensschritte sind nötig:
- Punktwolke auf nötige Auflösung reduzieren
- Punktwolke auf einen flächenbezogenen Betrachtungsbereich reduzieren
- fehlerhafte bzw. irrelevante Datenpunkte erkennen und entfernen

## 2.3.4 Bodenmodellierung und -segmentierung
"Bodenpunkte von den restlichen Punkten trennen"

Um die Informationsmenge der Punktwolke für den neuen Relevanzbereich weiter um ein Vielfaches zu reduzieren, werden alle bodenspezifischen Datenpunkte detektiert und vom Rest der Punktwolke getrennt.

Folgende Verfahrensschritte sind nötig:
- bodenspezifische Datenpunkte erkennen
- Ausreißer und fehlerhafte Datenpunkte herausfiltern

## 2.3.5 Punktwolkenverarbeitung
"neuen Betrachtungsbereich (Vegetationsbereich) der Punktwolke erzeugen"

Nachdem alle Bodenpunkte erfolgreich vom Rest segmentiert wurden, kann der restliche Teil der Punktwolke auf einen neuen höhenbezogenen Betrachtungsbereich reduziert werden.

Folgende Verfahrensschritte sind nötig:
- Vereinfachte geometrische Ebene/Fläche, => Bodenebene, aus allen Bodenpunkten an der Oberfläche erstellen
- höhenbezogene Betrachtungsgrenzen erstellen (untere und obere Betrachtungsgrenze), durch vertikales Verschieben der Bodenebene
- alle Punkte außerhalb beider Betrachtungsgrenzen entfernen -> Vegetationsbereich

## 2.3.6 Segmentierung bzw. Clustering
"einzelne Pflanzenstrukturen erfassen und segmentieren"

Die Punktwolke ist nun auf den nötigsten Informationsgehalt, dem Vegetationsbereich, reduziert wurden. Innerhalb dieses Bereiches werden nun alle Vegetationsstrukturen gesucht.

Folgende Verfahrensschritte sind nötig:
- alle Cluster, nach bestimmten Kriterien, detektieren
- Cluster voneinander segmentieren
- irrelevante Cluster entfernen
## 2.3.7 Clusterklassifizierung
"einzelne Pflanzencluster klassifizieren"

In der Punktwolke sind nur noch pflanzenrelevante Cluster, welche nach ihrer Relevanz und Geometrie klassifiziert werden müssen.

Folgende Verfahrensschritte sind nötig:
- die einzelnen Cluster werden eingeteilt nach: einzelne **Pflanze**, **Pflanzenhaufen** oder wenn nichts von beidem zutrifft sogar in **Pflanzenreihe**

## 2.3.8 Clusterrepräsentation
"Repräsentativpunkt/-geometrie der Pflanzencluster bestimmen"

Nun werden nur noch die Cluster **Pflanze** und **Pflanzenreihe** betrachtet, um einen vereinfachten Repräsentationspunkt zu ermitteln.
## 2.3.9 Reihenkandidatenerkennung
"alle Repräsentanten in einer Reihe erfassen -> Reihenkandidaten"

## 2.3.10 Reihenkandidatenvalidierung
"alle Reihenkandidaten anhand von Kriterien validieren"

## 2.3.11 Reihenklassifikation
"Reihenkandidaten klassifizieren"

## 2.3.12 Modellabstraktion
"Reihenverlauf geometrisch abstrahieren"

## 2.3.13 Datenexporttierung
"die gesamten Punkte aller Polylines werden in ein Datenformat gepackt"


# 2.4 Bibliotheken recherchieren


| Programmiersprache | Bibliothek | Bezeichnung |
| ------------------ | ---------- | ----------- |
| C++                | PCL        |             |
|                    | cilantro   |             |
| Python             | pyntcloud  |             |
|                    | Open3D     |             |
Standalone-Software:
CloudCompare

Nützliche Systeme:
MeshLab: bearbeiten von Meshes

