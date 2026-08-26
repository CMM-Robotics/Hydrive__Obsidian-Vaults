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

1. nutzbare Drohnenbilder aufnehmen
2. Punktwolke mittels Photogrammetrie erzeugen
3. Boden klassifizieren und entfernen
4. restliche Punktwolke nach Pflanzen bzw. Pflanzenreihen klassifizieren
5. (einzelnen Pflanzen einen Schwerpunkt berechnen -> möglicher Stammansatz)
6. alle Pflanzen in einer Reihe detektieren
7. Pflanzenreihen validieren und belegen, mittels horizontaler/vertikaler Schnitte in die Reihe
8. Eckpunkte der Pflanzenreihe erfassen
9. Polyline den Eckpunkten entlang führen
10. Polylines im richtigen Format ausgeben

# 2.3 Verfahren und Algorithmen recherchieren

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
