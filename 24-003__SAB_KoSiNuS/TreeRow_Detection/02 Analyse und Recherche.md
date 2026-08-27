## 2.1 Input analysieren

![[Pasted image 20260826113414.png|387]]

### relevante Kartenbausteine

| Kartenbaustein                                  | Nutzungsmöglichkeit                                                                                                                                                                              | Datenformat                           |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------- |
| PointCloud                                      | essentielle Datengrundlage                                                                                                                                                                       | LAS/LAZ       PLY                 CSV |
| DSM (Digital Surface Model)                     | Abgrenzung des Bodens und all seiner bodennahen Objekten von den restlichen Daten                                                                                                                | GLB                                   |
| DEM (Digital Elevation Model)                   | Validierung von sonstigen Objekten/Hindernissen in der Map. Differenz zwischen DEM und DSM bietet Rückschlüsse auf relevante Feldobjekte/Hindernisse                                             | GeoTIFF<br>JPEG<br>PNG<br>KMZ         |
| NDVI (Normalized Differential Vegetation Index) | der Vegetation Index kann zur genaueren Validierung potentiell entdeckter Pflanzenreihen genutzt werden. Bzw. zur Abgrenzung zwischen anlagenrelevanter Pflanzgebiete und Arbeitsgebieten/-wegen | GeoTIFF<br>JPEG<br>PNG<br>KMZ         |
| Orthofotos                                      | Bilderkennung für die grobe Detektion von Pflanzenreihen --> HeatMap für relevante Beobachtungsbereiche                                                                                          | GeoTIFF<br>JPEG<br>PNG<br>KMZ         |

| Kartenbaustein                                  | Konfigurationen |
| ----------------------------------------------- | --------------- |
| PointCloud                                      |                 |
| DEM (Digital Elevation Model)                   |                 |
| DSM (Digital Surface Model)                     |                 |
| NDVI (Normalized Differential Vegetation Index) |                 |
| Orthofotos                                      |                 |

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

## 2.3.2 Punktwolkengenerierung
"Punktwolke anhand der Aufnahmen erzeugen"

## 2.3.3 Punktwolkenaufbereitung
"Punktwolke für die Analyse aufbereiten"

## 2.3.4 Bodenmodellierung und -segmentierung
"Bodenpunkte von den restlichen Punkten trennen"

## 2.3.5 Punktwolkenverarbeitung
"neuen Betrachtungsbereich (Vegetationsbereich) der Punktwolke erzeugen"

## 2.3.6 Segmentierung bzw. Clustering
"einzelne Pflanzenstrukturen erfassen und segmentieren"

## 2.3.7 Clusterklassifizierung
"einzelne Pflanzencluster klassifizieren"

## 2.3.8 Clusterrepräsentation
"Repräsentativpunkt/-geometrie der Pflanzencluster bestimmen"

## 2.3.9 Reihenkandidatenerkennung
"alle Repräsentanten in einer Reihe erfassen -> Reihenkandidaten"

## 2.3.10 Reihenkandidatenvalidierung
"alle Reihenkandidaten anhand von Kriterien validieren"

## 2.3.11 Reihenklassifikation
"Reihenkandidaten klassifizieren"

## 2.3.12 Modellabstraktion
"Reihenverlauf geometrisch abstrahieren"

## 2.3.13 Datenexportierung
"die gesamten Punkte aller Polylines werden in ein Datenformat gepackt"












| Verarbeitungsschritt     | Bezeichnung                              | Algorithmen & Verfahren                                |
| ------------------------ | ---------------------------------------- | ------------------------------------------------------ |
| Punktwolke vorbereiten   | Reduzierung/Vereinfachung                | Voxel Downsampling, Cropping mit Hyperplane            |
|                          | Filterung                                | Statistical Outlier Removal, Radius Outlier Removal    |
| Feldkomponenten erfassen | Segmentierung vom Boden                  | Groundsegmentation                                     |
|                          | Erkennung aller Pflanzen                 | KI-Modelle, DBSCAN, RANSAC, Clustering                 |
|                          | Klassifizierung der Pflanzenteile        | KI-Modelle, Bilderkennung, Hilfsschnitt mit Hyperplane |
| Pflanzenreihen berechnen | Linien-Detection der Points of Interests |                                                        |

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


Sehr geehrtes NeurobitesTeam,

bei der Abwicklung des Kaufes ist ein Fehler passiert, und es soll keine weitere monatliche Lieferung sowie Abbuchung erfolgen. Dazu ist es total unübersichtlich, wo man eine Kündigung bei euch selber abwickeln kann, deshalb bitte ich euch das für mich zu erledigen