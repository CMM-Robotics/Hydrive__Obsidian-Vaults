## 2.1 Input analysieren

![[Pasted image 20260826113414.png|387]]

### relevante Kartenbausteine

| Kartenbaustein                                  | Nutzungsmöglichkeit                                                                                                                                                                              | Datenformat                           |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------- |
| PointCloud                                      | essentielle Datengrundlage                                                                                                                                                                       | LAS/LAZ       PLY                 CSV |
| DEM (Digital Elevation Model)                   | Validierung von sonstigen Objekten/Hindernissen in der Map. Differenz zwischen DEM und DSM bietet Rückschlüsse auf besondere Feldobjekte/Hindernisse                                             | GeoTIFF<br>JPEG<br>PNG<br>KMZ         |
| DSM (Digital Surface Model)                     | Abgrenzung des Bodens und all seiner Bodenobjekte von den restlichen Daten                                                                                                                       | GLB                                   |
| NDVI (Normalized Differential Vegetation Index) | der Vegetation Index kann zur genaueren Validierung potentiell entdeckter Pflanzenreihen genutzt werden. Bzw. zur Abgrenzung zwischen anlagenrelevanter Pflanzgebiete und Arbeitsgebieten/-wegen | GeoTIFF<br>JPEG<br>PNG<br>KMZ         |
| Orthofotos                                      | Bilderkennung für die grobe Detektion von Pflanzenreihen --> HeatMap für relevante Beobachtungsbereiche                                                                                          | GeoTIFF<br>JPEG<br>PNG<br>KMZ         |

| Kartenbaustein                                  | Konfigurationen |
| ----------------------------------------------- | --------------- |
| PointCloud                                      |                 |
| DEM (Digital Elevation Model)                   |                 |
| DSM (Digital Surface Model)                     |                 |
| NDVI (Normalized Differential Vegetation Index) |                 |
| Orthofotos                                      |                 |

# nötige Verarbeitungsschritte

nutzbare Drohnenbilder aufnehmen

Punktwolke mittels Photogrammetrie erzeugen

Boden klassifizieren und entfernen

restliche Punktwolke nach einzelnen Pflanzen klassifizieren

einzelnen Pflanzen einen Schwerpunkt berechnen (möglicher Stammansatz)

alle Schwerpunkte die in einer Linie sind als Reihe klassifizieren

mittels Validierungsschnitten und Verarbeitungen die Existenz der Pflanzenreihe untermauern

