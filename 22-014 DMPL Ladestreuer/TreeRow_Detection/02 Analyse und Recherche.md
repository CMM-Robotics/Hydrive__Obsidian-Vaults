# 2.1 Input analysieren

![[DSM-Vergleichsbilder.png|387]]

## relevante Kartenbausteine

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

---
Für die Datenerfassung wird folgende Drohne genutzt: **DJI Mavic 3M**
Sie besitzt eine Multispektralkamera mit 4 einzelnen Kameraspektren.
- R
- G
- RE
- NIR

Für die Aufnahme der Anlage Pillnitz wurden folgende Flugkonfigurationen gewählt:

| <p align="right">Einstellung</p>         | <p align="left">Parameter</p>     |
| ---------------------------------------- | --------------------------------- |
| <p align="right">``Front-Overlap``</p>   | <p align="left">90</p>            |
| <p align="right">``Side-Overlap``</p>    | <p align="left">80</p>            |
| <p align="right">``Geschwindigkeit``</p> | <p align="left">1,3 m/s</p>       |
| <p align="right">``Höhe``</p>            | <p align="left">30 m</p>          |
| <p align="right">``Auflösung``</p>       | <p align="left">1,38 cm/pixel</p> |
| <p align="right">``Course Angle``</p>    | <p align="left">40°</p>           |

Mit folgendem Abflugmuster:

![[Abflugmuster_Pillnitz.png|422]]

## 2.3.2 Informationsbeschaffung und -generierung
"Punktwolke anhand der Aufnahmen erzeugen"

Um aus den Serienbildern der Anlage Informationen wie räumliche Datenpunkte zu erhalten, wird die Photogrammmetrie mit der OpenSource-Software **WebODM** angewendet.

---
> **Photogrammmetrie**
> ist eine berührungslose Messmethode, bei der aus einer Serie überlappender Aufnahmen eines Objekts oder Geländes präzise, dreidimensionale Modelle und 2D-Zeichnungen konstruiert werden
>
> **1. Bildaufnahme:**
> Es werden mehrere überlappende Aufnahmen von einem Objekt aus verschiedenen Perspektiven/Positionen erstellt.
> 
> **2. Bild-Analyse:**
> Mithilfe von Algorithmen werden die Bilder analysiert, Muster erkannt und Zusammenhänge erschlossen. Die konsistente Zusammenführung der Informationen und spätere Rekonstruktion basiert auf dem Auffinden von gleichen Punkten und ähnlichen Strukturen.
> 
> **3. Räumliche Rekonstruktion:**
> Anhand von den zusammenhängenden Punkten und ihren Unterschieden zwischen den Perspektivwechseln der Serienaufnahmen, kann die räumliche Position rückgerechnet werden.

---
Die **Bildaufnahme** wurde in **2.3.1 Datenerfassung** mit der DJI Mavic 3M behandelt
Die **Bild-Analyse** sowie auch die **Räumliche Rekonstruktion** werden wie folgt mit WebODM realisiert.

Für die Informationsgenerierung aus allen Aufnahmen werden folgende Konfigurationen als Basis genommen:

| Kategorie              | Einstellung                           | RGB                                 | Multispectral                       |
| ---------------------- | ------------------------------------- | ----------------------------------- | ----------------------------------- |
| Structure From Motion  | `feature-quality`                     | high                                | high                                |
|                        | `min-num-features`                    | 20000 for crops/vegetation          | 20000 for crops/vegetation          |
| Point Cloud            | `pc-quality`                          | high                                | medium                              |
| Digital Elvation Model | `dsm`                                 | Enabled                             | Enabled                             |
|                        | `dtm`                                 | Disabled                            | Disabled                            |
|                        | `dem-resolution`                      | 5                                   | 5                                   |
| Meshing                | `mesh-size`                           | <font color="#ff0000">300000</font> | <font color="#ff0000">200000</font> |
| Orthophoto             | `orthophoto-resolution (float > 0.0)` | <font color="#ff0000">4.61</font>   | <font color="#ff0000">5</font>      |
|                        | `fast-orthophoto`                     | Disabled                            | Disabled                            |
| Tiles                  | `tiles`                               | Enabled                             | Enabled                             |
|                        | `3d tiles`                            | Enabled                             | Disabled                            |
<font color="#ff0000">Diese Werte sind variabel für den jeweiligen Anwendungsbereich einzustellen.</font>

---
In WebODM können folgende Daten exportiert werden:

![[Daten-Export-WebODM.png]]

#### Punktwolke
Der Export der PointCloud aus WebODM kann folgendermaßen gewählt werden.

Datentypen:

| Datentyp | Eigenschaften | Vorteile | Nachteile |
| -------- | ------------- | -------- | --------- |
| LAS      |               |          |           |
| LAZ      |               |          |           |
| PLY      |               |          |           |
| CSV      |               |          |           |

Coordinate Reference System (CRS):

#### Digital Surface Modell (DSM)


## 2.3.3 Informationsaufbereitung und -verarbeitung
"Punktwolke für die Analyse aufbereiten"

Die generierte Punktwolke muss für die Pipeline aufbereitet werden.

Folgende Verfahrensschritte sind nötig:
- Punktwolke auf nötige Auflösung reduzieren
- Punktwolke auf einen flächenbezogenen Betrachtungsbereich reduzieren
- fehlerhafte bzw. irrelevante Datenpunkte erkennen und entfernen
---
### V1: Punktwolke reduzieren

#### Downsampling

### V2: Betrachtungsbereich einstellen

#### Manuelles Eingrenzen des Bereiches (Cropping)
### V3: Störfaktoren entfernen

#### Statistical Outlier Removal


## 2.3.4 Bodenmodellierung und -segmentierung
"Bodenpunkte von den restlichen Punkten trennen"

Um die Informationsmenge der Punktwolke für den neuen Relevanzbereich weiter um ein Vielfaches zu reduzieren, werden alle bodenspezifischen Datenpunkte detektiert und vom Rest der Punktwolke getrennt.

Folgende Verfahrensschritte sind nötig:
- bodenspezifische Datenpunkte erkennen
- Ausreißer und fehlerhafte Datenpunkte herausfiltern
---
### V1: Bodensegmentierung

#### Ground Segmentation
#### DSM-Analyse

Für die Segmentierung des Bodens kann einfach halber das erzeugte Digital Surface Model (DSM) genutzt werden. Dieses Modell weist ein Höhenmodell der Anlage als 2D-Bild auf. Dabei wurden die jeweiligen Höhenprofile durch Gaussian Distribution Coloring eingefärbt. 
Das bedeutet, alle Punkte mit den größten Flächenabdeckungen einer bestimmten Höhe erhalten die kleinste Farbverteilung, während kleinere Flächenabdeckungen mit einer größeren Farbverteilung coloriert werden. 
Da alle Bodenpunkte allgemein auf einem bestimmten horizontalen Höhenbereich zu erwarten sind, diese recht homogen gefärbt. Dafür sind andere starken Höhenunterschiede visuell sehr leicht voneinander zu trennen.

![[Pillnitz-dsm_without_shattering 1.png|272]]

Durch geschicktes Einstellen der Höhengrenzen bei der Farbverteilung, können höhengleiche Bodenpunkte farblich stark homogenisiert werden, wobei der Unterschied zu den darüberliegenden relevanten Punkten stark differenziert sind.

Um alle Bodenpunkte von der Punktwolke zu trennen, wird das das DSM im Dateiformat **GeoTIFF (RGB)** benötigt. Da die Farbwerte, wie auch der dahinter liegende Georeferenzpunkt, zu jedem Punkt für die Segmentierung essentiell sind.


### V2: Störfaktoren entfernen



## 2.3.5 Punktwolkenverarbeitung
"neuen Betrachtungsbereich (Vegetationsbereich) der Punktwolke erzeugen"

Nachdem alle Bodenpunkte erfolgreich vom Rest segmentiert wurden, kann der restliche Teil der Punktwolke auf einen neuen höhenbezogenen Betrachtungsbereich reduziert werden.

Folgende Verfahrensschritte sind nötig:
- Vereinfachte geometrische Ebene/Fläche, => Bodenebene, aus allen Bodenpunkten an der Oberfläche erstellen
- höhenbezogene Betrachtungsgrenzen erstellen (untere und obere Betrachtungsgrenze), durch vertikales Verschieben der Bodenebene
- alle Punkte außerhalb beider Betrachtungsgrenzen entfernen -> Vegetationsbereich
---
### V1: Ebene erstellen

#### Hyper-Plane

### V2: Höhenbezogener Betrachtungsbereich

#### Shifting der Hyper-Plane


## 2.3.6 Segmentierung bzw. Clustering
"einzelne Pflanzenstrukturen erfassen und segmentieren"

Die Punktwolke ist nun auf den nötigsten Informationsgehalt, dem Vegetationsbereich, reduziert wurden. Innerhalb dieses Bereiches werden nun alle Vegetationsstrukturen gesucht.

Folgende Verfahrensschritte sind nötig:
- alle Cluster, nach bestimmten Kriterien, detektieren
- einzelne Cluster voneinander segmentieren
- irrelevante Nicht-Pflanzen-Cluster entfernen

___
### V1: Clustering

### V2: Cluster segmentieren


## 2.3.7 Clusterklassifizierung
"einzelne Pflanzencluster klassifizieren"

In der Punktwolke sind nur noch pflanzenrelevante Cluster, welche nach ihrer Relevanz, Geometrie und vor allem Qualität klassifiziert werden müssen.

Folgende Verfahrensschritte sind nötig:
- die einzelnen Cluster werden eingeteilt nach: einzelne **Pflanze**, **Pflanzenhaufen** oder wenn nichts von beidem zutrifft sogar in **Pflanzenreihe**

### V1: Cluster-Analyse und Klassifikation

## 2.3.8 Clusterrepräsentation
"Repräsentativpunkt/-geometrie der Pflanzencluster bestimmen"

Nun werden nur noch die Cluster **Pflanze** und **Pflanzenreihe** betrachtet, um eine vereinfachte (eindimensionale bzw. zweidimensionale) Repräsentationsgeometrie für die jeweiligen Cluster abzubilden. 

Folgende Verfahrensschritte sind nötig:
- für die Cluster **Pflanze** wird ein Repräsentationspunkt berechnet
- für die Cluster **Pflanzenreihe** wird eine Repräsentationslinie berechnet, wozu der Reihenverlauf und vor allem die (äußeren) Eckpunkte richtig detektiert werden müssen
### V1: Erkennung der Reihen-Endeckpunkte
### V2: Geometrische Repräsentation
#### Mittelpunkt in Clustern
#### Schwerpunkt in Clustern

#### 2D-Repräsentation von Clustern

## 2.3.9 Reihenkandidatenerkennung
"alle Repräsentanten in einer Reihe erfassen -> Reihenkandidaten"

Alle Repräsentanten, die unter bestimmten Kriterien eine mögliche Reihe mit anderen Repräsentanten bilden, werden als Reihenkandidaten aufgenommen.
## 2.3.10 Reihenkandidatenvalidierung
"alle Reihenkandidaten anhand von Kriterien validieren"

Alle relevanten Repräsentanten in einem Reihenkandidaten müssen nach bestimmten Kriterien validiert werden. Der Grad der Eignung der Repräsentanten in einem Reihenkandidaten gibt die Qualität und Güte der Reihe wieder.
## 2.3.11 Reihenklassifikation
"Reihenkandidaten klassifizieren"

Alle Reihenkandidaten werden nach bestimmten Kriterien klassifiziert.

Mögliche Betrachtungspunkte:
- Qualität
- Länge
- Anlagenposition
- Reihenbreite
- Homogenität
- Geselligkeit
- Reihenverlauf
- Reihenhöhe 
- Baumspitzenverlauf

| Reihenklassifikation       | Erklärung                                                                                                                                                                    |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pflanzenreihe              | alle bzw. die meisten Kriterien für eine Reihe sind sichergestellt                                                                                                           |
| mögliche Pflanzenreihe     | die Kriterien sind zum Teil erfüllt, da bestimmte Repräsentanten die Reihe kompromittieren                                                                                   |
| fehlerhafte Pflanzenreihe  | die Kriterien sind nur zu einem kleinen Teil erfüllt, da Störfaktoren die Qualität der Reihe massiv einbüßen, oder ihren Reihenverlauf beeinträchtigen oder gar unterbrechen |
| keine Pflanzenreihe        | beinhalten überwiegend fehlerhafte Repräsentanten und liegen hauptsächlich außerhalb vom relevanten Arbeits- bzw. Anlagenbereich                                             |
| undefinierte Pflanzenreihe | alle Reihenkandidaten die außerhalb aller anderen Klassifikationen liegen, aber eine gewisse Relevanz aufweisen                                                              |

## 2.3.12 Modellabstraktion
"Reihenverlauf geometrisch abstrahieren"

Alle Repräsentanten bilden mit ihren Repräsentationspunkten einen Reihenverlauf. Die Gesamtheit aller einzelnen Punkte abstrahieren die komplexe Reihenstruktur und ihren Verlauf als einfaches Polyline.

## 2.3.13 Datenexportierung
"die gesamten Punkte aller Polylines werden in ein Datenformat gepackt"

Die Eckpunkte aller Polylines werden absteigend nach ihrer Reihenklassifikation in einem komprimierfreies Datenformat gesammelt.

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

