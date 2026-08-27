#### ToDo:
- [ ] Welche verfügbaren Ressourcen sind nutzbar
- [ ] Welche Aufnahme-Methode eignet sich am besten (lokal bzw. extern)
- [ ] Welche Übertragungsmöglichkeit eignet sich am besten
- [x] Krishna fragen, ob der Simulationsbetrieb den realen Bedingungen entspricht (nötig für genaue Topic-Analyse)
- [x] Topic-Analyse während des Autonomie-Betriebes
- [x] Wie viele Subscriber je Topic
- [ ] Welche verfügbaren Schnittstellen gibt es für eine Debugging/Recording-Schnittstelle
- [x] In Code nach Publisher und Subscriber schauen (Anzahl, QoS-Depth)
- [x] Alle rechenintensiven Message-Fields in den einzelnen Messages heraussuchen (→ Strings, PointClouds, nested Message, Arrays)
- [x] Krishna alles fertige schicken
- [ ] alles fertige auf GitHub hochladen

---
# Analyse aller genutzten Topics

Bandbreite:
`BW = mean_size × frequenz`
- hohe Frequenz & kurze Nachrichten = hohe CPU-Last
- kleine Frequenz & lange Nachrichten = hohe I/O-Last

Message-Load:
`Msg_Load = frequenz * Subscriber-Anzahl` (wenn extern aufgezeichnet → 1 zusätzlicher Subscriber)

CPU-Last:
`CPU_Load = frequenz × log2(mean_size + 1)`

Schedueling-Load:
`Schedueling_Load = std_dev * (1 / frequenz)`
`Schedueling_Load = std_dev / frequenz

Max-Gap-Score:
`Max_Gap_Score = max_delta / (1 / frequenz)`
`Max_Gap_Score = max_delta * frequenz`

### Nützliche CLI-Commands zur Analyse während der Ros-Bag-Aufnahme

##### RAM
```bash
htop
```

```bash
pidstat -urd -p ALL 1
```
---
```bash
free -h
pidstat -r
```

##### I/O
```bash
iostat -xz 1
```

```bash
iotop -oPa
```
---
```bash
pidstat -d
```

##### Externes Recording
```bash
nload
```

```bash
iftop
```




```bash

```

```bash

```

```bash

```

```bash

```

```bash

```


| Topic                         | mean size | max. Bandbreite                  | max. Sendungsrate                    |
| ----------------------------- | --------- | -------------------------------- | ------------------------------------ |
| /fixposition/odometry_llh     | 136B      | ca. 1 kB/s (große Varianz)       | 5700Hz; max. 0,550s, std dev 0,080s  |
| /hyd_acu_vcu/nscmd1           | 0,03kB    | ca. 6,5 kB/s (große Varianz)     | 200Hz; max. 0,070s; std dev 0,005s   |
| /hyd_acu_vcu/nscmd2           | 0,03kB    | ca. 7 kB/s (große Varianz)       | 200Hz; max. 0,072s; std dev 0,004s   |
| /hyd_acu_vcu/nscmd3           | 0,04kB    | ca. 5 kB/s (große Varianz)       | 200Hz; max. 0,067s; std dev 0,004s   |
| /hyd_acu_vcu/nsstatus1        | 0,15kB    | ca. 2 kB/s (1,6 kB/s konstant)   | 10000Hz; max. 0,308s; std dev 0,045s |
| /hyd_acu_vcu/nsstatus2        | 64B       | ca. 700 B/s (643 B/s konstant)   | 10000Hz; max. 0,307s; std dev 0,060s |
| /hyd_acu_vcu/nsstatus3        | 28B       | ca. 300 B/s (280 B/s konstant)   | 10000Hz; max. 0,216s; std dev 0,04s  |
| /hyd_acu_vcu/nsstatus4        | 40B       | ca. 450 B/s (410 B/s konstant)   | 10000Hz; max. 0,300s; std dev 0,030s |
| /hyd_zone_manager/ref_cmd_vel | 0,05kB    | ca. 1,5 kB/s (1,1 kB/s konstant) | 20000Hz; max. 0,300s; std dev 0,043s |
| /hyd_zone_manager/zone_state  | 8B        | ca. 200 B/s (170 B/s konstant)   | 20000Hz; max. 0,250s; std dev 0,050s |
| /work_lamp                    | 8B        | genau 8 B/s                      | 1000Hz; max. 1.150s; std dev 0,040s  |
= 24,658 kB/s → max. 25 kB/s Bandbreite für alle 
→ realistisch 20kB/s

---

| Topic             | mean       | max. BW                          | max. HZ                              |
| ----------------- | ---------- | -------------------------------- | ------------------------------------ |
| /fixposition      | ---------- | ----------                       | ----------                           |
| /odometry_llh     | 136B       | ca. 1 kB/s (große Varianz)       | 5800Hz; max. 0,550s, std dev 0,080s  |
| /odometry_enu     |            |                                  |                                      |
| /hyd_acu_vcu      | ---------- | ----------                       | ----------                           |
| /nscmd1           | 0,03kB     | ca. 6,5 kB/s (große Varianz)     | 200Hz; max. 0,070s; std dev 0,005s   |
| /nscmd2           | 0,03kB     | ca. 7 kB/s (große Varianz)       | 200Hz; max. 0,072s; std dev 0,004s   |
| /nscmd3           | 0.04kB     | ca. 5 kB/s (große Varianz)       | 200Hz; max. 0,067s; std dev 0,004s   |
| /hyd_acu_vcu      | ---------- | ----------                       | ----------                           |
| /nsstatus1        | 0,15kB     | ca. 2 kB/s (1,6 kB/s konstant)   | 10000Hz; max. 0,308s; std dev 0,045s |
| /nsstatus2        | 64B        | ca. 700 B/s (643 B/s konstant)   | 10000Hz; max. 0,307s; std dev 0,060s |
| /nsstatus3        | 28B        | ca. 300 B/s (280 B/s konstant)   | 10000Hz; max. 0,216s; std dev 0,04s  |
| /nsstatus4        | 40B        | ca. 450 B/s (410 B/s konstant)   | 10000Hz; max. 0,300s; std dev 0,030s |
| /hyd_zone_manager | ---------- | ----------                       | ----------                           |
| /ref_cmd_vel      | 0,05kB     | ca. 1,5 kB/s (1,1 kB/s konstant) | 20000Hz; max. 0,300s; std dev 0,043s |
| /zone_state       | 8B         | ca. 200 B/s (170 B/s konstant)   | 20000Hz; max. 0,250s; std dev 0,050s |
| /work_lamp        | 8B         | genau 8 B/s                      | 1000Hz; max. 1.150s; std dev 0,040s  |

---

| Topic             |            |            |            |
| ----------------- | ---------- | ---------- | ---------- |
| /fixposition      | ---------- | ---------- | ---------- |
| /odometry_llh     |            |            |            |
| /odometry_enu     |            |            |            |
| /hyd_acu_vcu      | ---------- | ---------- | ---------- |
| /nscmd1           |            |            |            |
| /nscmd2           |            |            |            |
| /nscmd3           |            |            |            |
| /hyd_acu_vcu      | ---------- | ---------- | ---------- |
| /nsstatus1        |            |            |            |
| /nsstatus2        |            |            |            |
| /nsstatus3        |            |            |            |
| /nsstatus4        |            |            |            |
| /hyd_zone_manager | ---------- | ---------- | ---------- |
| /ref_cmd_vel      |            |            |            |
| /zone_state       |            |            |            |
| /work_lamp        |            |            |            |

---
---
##### genutzte Topics:
> /fixposition/odometry_llh
> /hyd_acu_vcu/nscmd1
> /hyd_acu_vcu/nscmd2
> /hyd_acu_vcu/nscmd3
> /hyd_acu_vcu/nsstatus1
> /hyd_acu_vcu/nsstatus2
> /hyd_acu_vcu/nsstatus3
> /hyd_acu_vcu/nsstatus4
> /hyd_zone_manager/ref_cmd_vel
> /hyd_zone_manager/zone_state
> /work_lamp

---
##### optionale Topics:
> /bond
> /diagnostics
> /innotrac_feedback 
> 	(String-Wert: path_active/total_distance/distance_remaining)
> /innotrac_progress 
> 	(nur String)
> 	(aktiver Pfad; Gesamtentfernung; verbleibende Entfernung)
> /imu/data

---
##### essentielle Topics:
> /fixposition/odometry_llh
> /fixposition/odometry_enu (fehlt)
> /hyd_acu_vcu/nscmd1
> /hyd_acu_vcu/nscmd2
> /hyd_acu_vcu/nscmd3
> /hyd_acu_vcu/nsstatus1
> /hyd_acu_vcu/nsstatus2
> /hyd_acu_vcu/nsstatus3
> /hyd_acu_vcu/nsstatus4
> /hyd_acu_vcu/nsstatus5 (nicht genutzt)
> /hyd_acu_vcu/nsstatus6 (nicht vorhanden)
> /hyd_zone_manager/ref_cmd_vel
> /hyd_zone_manager/zone_state
> /mission_state (nur String)
> /work_lamp

### Performance-Bottle-Neck

##### 1. Callback:
---
Jedes versendete Message hat einen langen Verarbeitungsprozess:
- DDS Receive
- Deserialisierung
- Queue push
- Executor wakeup
- Callback dispatch
- Speicherverwaltung
- evtl. Logging
- rosbag serialization

Formel:
$$
	C_{msg}​ = f * N_{sub}
$$
gewichtete Formel:
$$	
	C_{cpu}​ = f * N_{sub}​ * W_{cb}
$$
Mit Gewicht $W_{cb} =$
- 1: trivial
- 2: Filter
- 4: Transformation
- 10: komplexe Verarbeitung

##### 2. Scheduling Load
---
**Threads-WakeUps**

Formel:

$$
	L_{sched}​ = ∑ f_i
$$
---
**Jitter**

Formel:
$$
J=\frac{σ}{T}​
$$
Bei Ergebnis $J$
- < 0.1: gut
- 0.1 - 0.5: befriedigend
- 0.5 - 2: kritisch
- > 2: Scheduling-Fehler

#### 3. Serialisierung
---
Formel:
$$
S=size⋅f
$$
gewichtete Formel:
$$ 
Sc​=size⋅f⋅C_{type}
$$
Mit Gewicht $C_{type}$ =
- 1: einfacher Struct
- 2:  String
- 3: Arrays
- 4 - 8: nested msgs
- >10: PointCloud

#### 4. Speicher
---
**Queue Memory**
Formel:

$$
M=q⋅size⋅N_{sub}​
$$
- $q$ - QoS depth
---
Allocator Churn



### Speichernutzung
Formel:
$$
Speicher ≈ Messagegröße × Depth × Endpunkte
$$

$$
Endpunkte = N_{Sub} + 1 Publisher
$$




# Vorkehrungen zur Ressourceneinsparung:
- [ ] Kompression aus stellen (nicht Flags nutzen, wie `--compression-mode` & `--compression-format`)
- [ ] nur alle nötigen Topics auswählen
- [ ] rclcpp (C++) viel besser für Performance statt rclpy (Python)
- [ ] QoS-Konfiguration ändern
- [ ] FastDDS nutzen wenn möglich
- [ ] CLI-Tuning
- [ ] Topic-Throttle mit `topic_tools`
	-  /nscmd:          50 -100 Hz
	-  /nsstatus:      10 - 100 Hz
- [ ] Publish-Frequenz systemweit einstellbar machen mit Schnittstelle (verhältnismäßig für alle Publisher) --> Fak-Einstellung {acu_vcu-Schnittstelle weg lassen}
- [ ] Topics aggregieren (mehrere gleiche Topics zu einem zusammenfassen)
- [ ] externes Recording
- [ ] ungenutzte Arrays aus Message-File entfernen
- [ ] Strings, wenn möglich, als Low-Bit-Message verschicken (bspw. als uint8)
	-  bei großer variabler Liste, am Anfang die 

### CLI-Tuning

hilfreiche Flags:
```bash
--ignore-leaf-topics
--compression-mode
--compression-format
```

CLI-Command für reduziertes ROS-Bag
```bash
ros2 bag record \  
-o rosbag_1 \  
/fixposition/odometry_llh /hyd_acu_vcu/nscmd1 /hyd_acu_vcu/nscmd3 /hyd_acu_vcu/nsstatus1 /hyd_acu_vcu/nsstatus2 /hyd_acu_vcu/nsstatus3 /hyd_acu_vcu/nsstatus4 /hyd_zone_manager/ref_cmd_vel /hyd_zone_manager/zone_state /work_lamp
```


### Quelle

Erklärung des CLI-Commands für Topic-Analyse:
https://roboticsbackend.com/ros2-topic-cmd-line-tool-debug-ros2-topics-from-the-terminal/
