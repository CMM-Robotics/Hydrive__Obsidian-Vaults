# ToDo
- [ ]  Publisher-Frequenzen zu jedem Topic finden
- [ ] Sind Intra-Prozesse möglich
	→ Integration von Composable Nodes
- [ ] Code nach shared_ptr/unique_ptr überprüfen
	→ nur Callback-Aufruf
	→ spart De-/Serialization, Kopiervorgänge, Speicherverwaltung, CPU-Zeit
	→ besseres Ressourcenmanagement

In Tabelle:
- [ ] QoS-Depth für Costmap-Topics herausfinden

In Top-Tool:
- [ ] Rosbag laufen lassen und alle nötigen Ressourcen vergleichen
	--> wird SWAP genutzt --> Echtzeitverhalten
# Wichtige Infos
- Can-Bus im Simulator: 1 MBit/s
- Can-Bus im Caesar: 500 kBit/s
- Hauptsächlich `shared_ptr` genutzt

### Optimierungsansätze
- Hauptprozess und alle Subroutinen sollten zusammenhängend sein
- Mit Intraprozess:
	--> `shared_ptr` --> kaum Kopien

---
---

## DDS
Mit Intraprozess:
--> `shared_ptr` --> kaum Kopien
##### Serialisierung
- CPU-Last
- Speicherzugriffe
- Kopiervorgänge
- Allokationen

##### Message-Größe
- Speicherverbrauch
- Kopierzeit
- Serialisierungszeit
- I/O-Last
- Cache-Belastung

##### Topic-Frequenz
- Callbacks
- DDS-Events
- Queue-Verwaltung
- Auflösung der Zeitstempel
- Scheduling
- Kopier-Vorgänge
- Package-Loss-Gefahr
---

## Timing

##### Jitter
Ursachen:
- CPU-Last
- andere Prozesse
- Scheduler
- Locks
- Speicherallokation
- Garbage/Allocator-Effekte
- I/O-Blocking
- lange Callbacks
- Executor-Stau
→unsauberes Roboterverhalten

##### Deadline Risk
- [ ] Topics nach möglichen Deadline Risks kategorisieren
	→ welche Topics gefährden die Echtzeitfähigkeit

##### Executor
Managed die zeitliche Abarbeitung der Callbacks

`SingleThreadedExecutor` - einzelne Ausführung eines Callbacks
→ blockiert bei Ausführung andere Callbacks

`MultiThreadedExecutor` - parallele Ausführung mehrer Callbacks
→ Mutex contention
→ Race Conditions
→ Datenzugriffskonflikte
→ Thread Scheduling
→ Lock-Wartezeiten

---


## I/O

##### Sensor Throughput

##### Disk Writes
- [ ] Logging -> kleine Dateien Buffern und als große Blöcke auf Disk schreiben 


_____
---

# CPU
- [ ] Callback-Komplexität bei hochfrequenten Topics untersuchen
	- viele kleine komplexe Callbacks >> wenig einfache Callbacks
- [ ] Callbackdauer mit Callbackrate vergleichen
- [ ] PointClouds verarbeitungen genauer untersuchen (Größe der Pointcloud und Zwischenschritte heraussuchen)
- [ ] Subscriberanzahl bei schwerlastigen Topics (/image, /PointCloud2, /OccupancyGrid, viele Strings)
##### Callback
- Funktionsaufrufe
- Executer-Verwaltung
- Speicherzugriff
- Schedueling
- Kontextwechsel
- Locking/Mutexe

##### Subscriber
- mehr Kopiervorgänge
- mehr Serialisierung
- mehr Speicherverbrauch
- mehr CPU-Zeit
- mehr DDS-Verwaltung

### <font color="#f79646">Callback Processing Load</font>
Arbeitsaufwand zum Verarbeiten aller Callbacks
$$
Processing_-Load=frequency×N_{sub}×W_{callback_-complexity}
$$

mit $W_{callback.complexity}$ :
- 1: trivial
- 2: filter
- 4: transformation
- 10: komplexe Verarbeitung

---
# Speicher
- [ ] bei erwarteten Allokationen, ausreichend Speicher vorher reservieren --> besser als viele kleine Allocs zur Programmlaufzeit
##### Strings
- Heap-Allokation
- Kopiervorgänge
- Cache-Misses
- Fragmentierung
- Serialisierungsaufwand

##### (Dynamic) Arrays
- dynamische Speicherreservierung
- mehr Kopieraufwand

##### Nested Images
- mehr Speicherzugriffe
- mehr Verwaltungsaufwand
- mehr Kopieroperationen
- oft mehr dynamische Speicherbereiche
- schlechtere Cache-Lokalität

##### QoS
- erhöhter RAM-Verbrauch
- größere Latenz
- ältere Daten in der Queue (schlecht für Regelungssysteme)

##### Dynamic Allocation
Zwischenschritte:
- verfügbaren Speicher suchen
- Speicherbereich reservieren
- Verwaltungsdaten aktualisieren
- gegebenfalls Speicher verschieben/fragmentieren
- Speicher wieder freigeben

### <font color="#f79646">Datenrate (Speicher)</font>
$$
BW_{mem}=M_{msg_-size}×f
$$
### <font color="#f79646">Queue-Speicher</font>
Maximaler RAM-Verbrauch der Queues
$$
M_{queue}=M_{msg_-size}×depth×N_{subs}
$$
---
# DDS
### <font color="#f79646">Datenrate pro Publisher</font>
Datenbandbreite für Publisher
$$
BW_{pub}=M_{msg_-size}×f
$$
### <font color="#f79646">Datenrate pro Topic</font>
Datenbandbreite für Topic (Publisher Seite)
$$
BW_{topic}=M_{msg_-size}×f×N_{pub}
$$
### <font color="#f79646">Data Distribution Load</font>
Kommunikationslast (Subscriber Seite)
$$
DDS_-Load=f×M_{msg_-size}×W_{callback_-complexity}​×N_{sub}​​
$$
Mit $W_{callback.complexity}$ :
- 1: trivial
- 2: filter
- 4: transformation
- 10: komplexe Verarbeitung

---

# Timing
### <font color="#f79646">Jitter-Coefficient</font>
$$
Jitterratio​=\frac{std_-deviation}{Period} ​
$$Mit $Jitterratio$:
`< 0.01 :` sehr stabil
`0.01 - 0.05 :` in Ordnung
`> 0.1 :` zeitkritisch

### <font color="#f79646">QoS-Risiko</font>
$$
QoS_{risk}​=History_-{depth}×Reliability_-factor​×Durability_-factor
$$

### <font color="#f79646">QoS-Risiko mit Callback-Complexity</font>
$$
QoS_{risk}​=History_-{depth}×Reliability_-factor​×Durability_-factor×W_{callback_-complexity}​
$$ 
mit $History_-depth$:
BEST_EFFORT:       1.0
RELIABLE:      1.4-2.4

mit $Reliability_-factor$:
B   -->   1.0 
R   -->   1.4-2.5

mit $Durability_-factor$:
KEEP_LAST 1   -->   1.0
KEEP_LAST 10   -->   1.3
KEEP_ALL   -->   2-5

Mit $W_{callback.complexity}$ :
- 1: trivial
- 2: filter
- 4: transformation
- 10: komplexe Verarbeitung





### Message-Komplexitäts-Last
$$
Complexityload​=Message_-feasibility×M_{msg_-size}×f
$$

### Callback-Rate
$$
Processingpressure​=f×N_{sub}
$$
### Worst_Case Queue Delay
wann die Queue voll ist und somit mit alten Daten zu rechnen ist

### Subscriber-WorkLoad
Callbackaufrufe pro Sekunde
$$
Processing_{pressure} = f \times Nsub
$$
