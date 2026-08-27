Diese OpenSource-Software ist ein Visualisierungstool für laufende Datensignale in komplexen robotischen Systemen und ermöglicht das Analysieren ausgewählter Signalverläufe für das Debugging.
Diese Software arbeitet streng mit Plugins und kann somit für den jeweiligen Anwendungsfall umkonfiguriert werden. Für unser Vorhaben wird das PlotJuggler mit ROS-Plugins genutzt, welches zusätzlich ein Topic-Subscriber- und Data-Load-Plugin enthält, um aktiv laufende ROS-Topics zu erhalten oder ROS-Bags abzuspielen.

| <font color="#c0504d">Wichtig</font>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Die Handhabung und Benutzerfreundlichkeit der Software ist sehr gewöhnungsbedürftig und stark eingeschränkt. Gewisse Konfigurationen sind an manchen Stellen anfangs schwierig nachvollziehbar oder zögerlich in der Verfügbarkeit, weshalb erfolgreiche Einstellungen erst nach mehrmaligen Wiederholungen vorangegangener Schritte in Kraft treten. Kommt es dennoch zu Komplikationen, ist es ratsam die jeweiligen Script Editoren neu aufzurufen, nicht funktionierende Plots zu löschen und neu zu erstellen, oder gar die ganze Software neu zu starten. |

___
---

# <font color="#9bbb59">PlotJuggler Installation:</font>

| <font color="#953734">Sehr wichtig</font>                                                                                                                                                                                                                                                                                                             |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Für die ordnungsgemäße Nutzung dieser Debugging-Oberfläche soll das native PlotJuggler der Version V3.16 installiert und genutzt werden. Da das für ROS-Nutzer empfohlene `ros-humble-plotjuggler-ros` noch über die vorherige Version V3.15 läuft, treten somit häufige Komplikationen und Fehler auf, welche die Nutzung sehr problematisch machen. |

##### <font color="#9bbb59">1. Workspace anlegen</font>
```bash
mkdir -p ~/my_plotjuggler_ws/src
cd ~/my_plotjuggler_ws/src
```

##### <font color="#9bbb59">2. Package installieren</font>
<font color="#c0504d">Nicht!!!:</font>
```bash
sudo apt install ros-$ROS_DISTRO-plotjuggler-ros
```

<font color="#c0504d">Sondern:</font>
```bash
git clone https://github.com/PlotJuggler/plotjuggler_msgs.git  
git clone https://github.com/facontidavide/PlotJuggler.git  
git clone https://github.com/PlotJuggler/plotjuggler-ros-plugins.git
```

##### <font color="#9bbb59">3. Workspace builden:</font>
```bash
colcon build
```

##### <font color="#9bbb59">4. Launch:</font>
```bash
ros2 run plotjuggler plotjuggler
```

---
### <font color="#9bbb59">Message-Package einbinden</font>

Damit Topics mit eigenen Message-Typen von PlotJuggler erkannt werden, müssen die Abhängigkeiten der Packages mit den Messages-Templates gebuildet werden.
Damit die nötigen Abhängigkeiten in der bevorstehenden PlotJuggler-Session auch in Kraft treten, muss die jeweilige Projekt-Umgebung vor dem Launch immer gesourced werden.

##### <font color="#9bbb59">1. Workspace builden</font>
```bash
cd ~/my_robot_workspace
# in Workspace-Root navigieren

# Auswählen des jeweiligen Build-Commands:

colcon build --packages-select package_1 package_2     # 1. Commmand
# builden nur der benötigten Packages

colcon build --packages-to-skip package_1 package_2    # 2. Command
# Gesamt-Build mit Ausschluss bestimmter Packages
```

##### <font color="#9bbb59">2. Launch</font>
```bash
   source ~/my_robot_workspace/install/setup.bash
   
   ros2 run plotjuggler plotjuggler
```

---

# <font color="#4f81bd">PlotJuggler-Oberfläche</font>

### <font color="#4f81bd">> File</font>
Neben "<font color="#9bbb59">Data: </font>" kann mit dem Button eine Datensammlung zum Plotten importiert werden.
Dabei werden diese Datentypen unterstützt (*.csv *.mcap *.yaml *.ulg)

Unter "<font color="#9bbb59">Layout: </font>" kann das individuell konfigurierte Debug-Layout gespeichert oder wieder geladen werden.
_______________________________________________________________________
### <font color="#4f81bd">> Streaming</font>
Hierüber kann gewählt werden, über welche Streaming-Quelle (ROS2 Topic Subscriber, UDP Server, ...) die zu plottenden Daten empfangen werden sollen. 
Ist "<font color="#9bbb59">ROS2 Topic Subscriber</font>" gewählt und mit "<font color="#9bbb59">Start</font>" bestätigt wurden, öffnet sich ein Fenster aller aktiven Topics. Durch Anklicken können nun die vorgesehenen Topics für den Daten-Stream aus- bzw. abgewählt werden.

Mit dem **||**-Button kann der Daten-Stream temporär pausiert werden, um Analysen an dem Funktionsverlauf durchführen zu können. Um die Streaming-Quelle allgemein zu beenden oder mit anderen Topics neu einrichten zu können, muss der "<font color="#9bbb59">Pause</font>"-Button betätigt werden.
_______________________________________________________________________
### <font color="#4f81bd">> Timeseries List</font>
Hier werden alle ausgewählten und aktiven Topics gesammelt und gelistet, um einzelne Topic-Nachrichten durch "Drag and Drop" in das Plot-Fenster zu ziehen und visuell auszugeben.

Um Datenverläufe miteinander zu vergleichen, können mehrere Topics im gleichen "<font color="#9bbb59">Tab</font>" visualisiert werden. 

Unter "<font color="#9bbb59">Custom Series: </font>" werden alle aktiven und verfügbaren Scripts gelistet, und können an dieser Stelle erstellt, bearbeitet oder gelöscht werden.
_______________________________________________________________________

### <font color="#4f81bd">> Script-Editor</font>
Um Daten für das Plotten aufzubereiten, werden Scripts genutzt. Diese können entweder einzelne Timeseries manipulieren oder mit anderen verarbeitet werden, um Zusammenhänge visuell sichtbar zu machen. Zum Einsatz kommen zwei bestimmte Script-Arten (TimeSeries-Plot oder XY-Plot), welche im Script-Editor erstellt und verwaltet werden.

| <font color="#c0504d">Wichtig</font>                                                                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Beim Erstellen von Scripts im Script-Editor ist darauf zu achten, dass dieser im Allgemeinen schon das Gerüst der Script-Funktion darstellt. In "<font color="#9bbb59">Global variables</font>" werden somit Initialisierungen außerhalb der Funktion deklariert, und "<font color="#9bbb59">function(tracker_time)</font>" stellt somit den Funktionskopf dar, wobei im unteren Feld nur der Funktionsrumpf hinterlegt werden muss. |

#### <font color="#4bacc6">Single Function (TimeSeries-Plot)</font>
Dieses Script manipuliert einzelne oder mehrere TimeSeries, um ein bestimmtes Ausgabe-Ergebnis für Analysen zu erhalten. Das Ergebnis ist wiederum ein TimeSeries, welches unter "<font color="#9bbb59">Custom Series: </font>" gelistet ist, und als Quelle für weitere Single Functions genutzt werden kann.
##### Script-Erstellung:
1. **Single Function Editor öffnen:**
	- Betätigen von "+" bei "<font color="#9bbb59">Custom Series: </font>" unter <font color="#4f81bd">Timeseries List</font>
2. **Timeseries Quellen hinzufügen:**
	- Vor Öffnen des Editors → vorheriges Anwählen des Topics in <font color="#4f81bd">Timeseries List</font>
	- Oder nachträglich in Zeile "drag & drop here the input timeseries" ziehen
	- zusätzliche Timeseries werden im Feld unter "<font color="#9bbb59">Additional source timeseries: </font>" hinzugefügt und nacheinander automatisch mit den Variablen "<font color="#9bbb59">v1, v2, ... , vn</font>" bezeichnet
3. **Globaler Code:**
	- Alle globalen Initialisierungen außerhalb der Single Funktion werden unter das Feld <font color="#9bbb59">Global variables</font> niedergeschrieben
4. **Funktions-Kopf <font color="#9bbb59">function(time, value, v1, v2, ..., vn)</font>:**
	- Dieser ist schon mit seinen Funktionsparametern implizit im Editor vordefiniert, und darf nicht nochmal im Script-Code deklariert werden
	- primär genutzte TimeSeries-Quellen erhalten die Standard-Funktions-Parameter "<font color="#9bbb59">(time, value, </font>"
	- alle zusätzlichen TimeSeries-Quellen lassen sich nutzen über ihre Bezeichner "<font color="#9bbb59">v1, v2, ... , vn</font>"
5. **Script Code:**
	- Im Feld unter dem Funktions-Kopf wird nur der Funktions-Rumpf hinterlegt
	- Man hat Zugriff auf den Globalen Code und die einzelnen Funktionsparameter
	- auch schon vorhandene Funktionen aus der "<font color="#9bbb59">Function library: </font>" können geladen, umgeändert und genutzt werden
6. **Compilieren:**
	- Unter"<font color="#9bbb59">New name: </font>name the new timeseries" wird die fertige Funktion benannt
	- Ist das Script kompilierbar, so wechselt der nebenstehende Indikator von rot (fehlerhaft) zu grün (verfügbar) → das Script ist nun nutzbar
7. **Script-Nutzung:**
	- alle erstellten und nutzbaren Scripts werden als neue vollwertige TimeSeries unter "<font color="#9bbb59">Custom Series</font>" und in der <font color="#4f81bd">Timeseries List</font> gelistet und verfügbar gemacht
	- zur Langzeitaufbewahrung der Scripts, können diese unter "<font color="#9bbb59">Function library: </font>" gespeichert werden
---
#### <font color="#4bacc6">Reactive Script (XY-Plot)</font>
Diese Scripts stellen eine besondere Klasse dar und werden reaktiv auf eintreffende Daten oder zu bestimmten Zeitfortschritten (durch <font color="#9bbb59">tracker_time</font>) aufgerufen, um den hinterlegten Script-Code auszuführen.

| <font color="#c0504d">Wichtig</font>                                                                                                                                                                                                                                                  |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Für XY-Plots nutzt man die Eigenschaft der Reaktivität, welche durch tracker-time ausgelöst wird. Zu jedem dieser Zeitfortschritte wird das Script aufgerufen, und wird dazu genutzt, die Argumente aus verschiedenen TimeSeries zu extrahieren und gemeinsam als XY-Plot auszugeben. |
##### Script-Erstellung:
1. Reactive Script Editor öffnen:
	- In der Menü-Leiste unter "<font color="#4f81bd">Tools</font>" bei "<font color="#9bbb59">Reactive Script Editor</font>" 
2. **Timeseries Quellen hinzufügen:**
	- hier müssen die einzelnen TimeSeries-Quellen explizit lokal im Funktionsrumpf deklariert sein ![[Time-Series__Source-Topics.png]]
3. **Globaler Code:**
	- Alle globalen Initialisierungen außerhalb der Single Funktion werden unter das Feld <font color="#9bbb59">Global variables</font> niedergeschrieben
4. **Funktions-Kopf <font color="#9bbb59">function(tracker_time)</font>:**
	- Dieser ist schon mit seinen Funktionsparametern implizit im Editor vordefiniert, und darf nicht nochmal im Script-Code deklariert werden
	- "<font color="#9bbb59">tracker_time</font>" ist hier der einzige Funktionsparameter. Dieser bewegt sich mit dem Zeitdurchlauf, und bietet somit eine Triggermöglichkeit für das Script
5. **Script Code:**
	- Im Feld unter dem Funktionskopf wird nur der Funktions-Rumpf hinterlegt
	- Man hat Zugriff auf den Globalen Code und den Funktionsparameter "<font color="#9bbb59">tracker_time</font>"
	- Unter "<font color="#9bbb59">Function library: </font>" können auch hilfreiche Code-Snippets genutzt werden
6. **Compilieren:**
	- Unter "<font color="#9bbb59">Name: </font>" wird die fertige Funktion benannt mit "<font color="#9bbb59">Save</font>" gespeichert
	- Ist das Script kompilierbar, so wird es unter "<font color="#9bbb59">Active Scripts</font>", "<font color="#9bbb59">Recent scripts</font>" und "<font color="#9bbb59">Custom Series</font>" gelistet
7. **Script-Nutzung:**
	- alle erstellten und nutzbaren Scripts werden als  XY-TimeSeries unter "<font color="#9bbb59">Custom Series</font>" und in der <font color="#4f81bd">Timeseries List</font> gelistet und verfügbar gemacht
---

## <font color="#4f81bd">> Konfigurations-Datei:</font>
Die Konfigurationsdatei wird als XML-Datei unter dem eigen gewählten Pfad im Workspace gespeichert und enthält alle nötigen Konfigurationen für das Laden des angefertigten Layouts. Dieses kann bei "Layout: [↓]" geöffnet oder ein eigen Erstelltes bei "Layout [↑]" abgespeichert werden.

| <font color="#c0504d">Wichtig</font>                                                                                                                                                                                                                                          |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| In bestimmten Situationen ist es viel einfacher programmweite Veränderungen in der Konfigurationsdatei vorzunehmen, als über die umständliche Handhabung des Programms, da dies träge reagiert und meistens keine weiteren Möglichkeiten für Umkonfigurationen mehr anbietet. |
### Ausgewählte Topic-Quellen (`<selected_topics>`)

Bei jedem Start des ROS2 Topic Subscribers wird man dazu aufgefordert alle nötigen Topics auszuwählen, wobei die Auswahl auch normalerweise für die nächste PlotJuggler-Sessions abgespeichert wird. Doch unter bestimmten Umständen kommt es vor, dass diese Selektion nicht mehr verfügbar ist, was das erneute Auswählen, vorallem bei sehr vielen bzw. akribisch selektierten Topics, sehr mühsam und zeitaufwendig macht. 
Deshalb ist es ratsam derartige Schwierigkeiten in der gespeicherten Layout-XML-Datei zu lösen. Nach einer erfolgreichen Auswahl aller nötigen Topics, werden diese im Abschnitt `<selected_topics value= ... />`  abgespeichert. Dieser sollte für jene bestimmte Topic-Auswahl zwischengespeichert werden, um diese an gegebener Stelle in der genutzten Layout-Datei einfach zu ersetzen. Zudem kann es unkomplizierter sein bestimmte und vereinzelte Topics in diesem Abschnitt hinzuzufügen bzw. zu entfernen.
```xml
<selected_topics value="/fixposition/odometry_llh;/hyd_acu_vcu/nscmd1;/hyd_acu_vcu/nscmd2;/hyd_acu_vcu/nscmd3;/hyd_acu_vcu/nsstatus1;/hyd_acu_vcu/nsstatus2;/hyd_acu_vcu/nsstatus3;/hyd_acu_vcu/nsstatus4;/hyd_acu_vcu/nsstatus5;/hyd_acu_vcu/nsstatus6;/hyd_acu_vcu/scmd1;/hyd_zone_manager/ref_cmd_vel;/hyd_zone_manager/zone_state"/>
```
---

### Single Functions (`<customMathEquations>`)

Wurde eine Single Function erstellt, wird sie unter `<customMathEquations ... />` gespeichert. Zu erkennen ist, dass das gegebene Speicher-Layout den einzelnen Bearbeitungsfeldern im Script-Editor entsprechen. 

| <font color="#c0504d">Sehr wichtig</font>                                                                                                                                                                                                                                                                                                 |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Da sich Funktionsname und Topic-Quelle einer bereits erstellten Single Funktion leider nicht mehr ordnungsgemäß in PlotJuggler ändern lassen, sollten diese im Script Editor gelöscht und erneut erstellt werden. Am einfachsten und unkompliziertesten hingegen ist es, diese Änderungen in der XML-Datei an gewisser Stelle zu tätigen. |

| Code-Snippet                                                           | Erklärung                                                                                                 |
| ---------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------- |
| `<snippet name=""> ... </snippet>`                                     | Darin wird die gesamte Single Function beschrieben                                                        |
| `<global> ... </global>`                                               | enthält den globalen Code                                                                                 |
| `<function> ... </function>`                                           | enthält den Funktions-Rumpf (-Code)                                                                       |
| `<linked_source> ... </linked_source>`                                 | enthält die primäre Topic-Quelle                                                                          |
| `<additional_sources>`<br>    `<v1> ... </v1>` `</additional_sources>` | darin werden alle zusätzlichen sekundären Topic-Quellen mit ihren Bezeichnungen `v1, ... , vn` hinterlegt |

```xml
 <customMathEquations>
 ...
  <snippet name="IST-Lenkwinkel (links)">
   <global></global>
   <function>return value * v1</function>
   <linked_source>IST-Lenkwinkel</linked_source>
   <additional_sources>
    <v1>/hyd_acu_vcu/nsstatus1/steer_dir_le</v1>
   </additional_sources>
  </snippet>
  ...
</customMathEquations>
```
---

### Reactive Scripts (`<scripts>`)

Im Reactive Script Editor Plugin werden unter `<scripts> ... </scripts>` alle reactive Scripts nach dem gleichen Schema wie im Single Function Editor abgespeichert, nur die Topic-Quellen brauchen nicht gespeichert werden, da diese schon im Funktions-Rumpf lokal deklariert sein müssen. Besonderheit im Reactive Script Editor ist der Tab "<font color="#9bbb59">Function Library</font>", wo hilfreiche Code-Snippets für den Script-Entwurf gesammelt sind. Diese liegen in `<library code=" ... "/>` und können durch "<font color="#9bbb59">Restore Default</font>" geladen werden, oder man sammelt eigene Snippets im vorgesehenen Feld und speichert diese ab, mit "<font color="#9bbb59">Apply changes</font>".

| Variable | Erklärung                           |
| -------- | ----------------------------------- |
| global   | enthält den globalen Code           |
| name     | definiert den Script-Name           |
| function | enthält den Funktions-Rumpf (-Code) |

```xml
<plugin ID="Reactive Script Editor">
 <library code=" ... "/>
 <scripts>
  <script global="series_xy = ScatterXY.new(&quot;Caesar Trajectory&quot;)" name="Odometry_LLH" function="series_xy:clear()&#xa;&#xa;series_x = TimeseriesView.find(&quot;/fixposition/odometry_llh/longitude&quot;)&#xa;series_y = TimeseriesView.find(&quot;/fixposition/odometry_llh/latitude&quot;)&#xa;&#xa;if series_x == nil or series_y == nil then&#xa;    print(&quot;series not found&quot;)&#xa;    return&#xa;end&#xa;&#xa;nx = series_x:size()&#xa;&#xa;for i = 0, nx - 1 do&#xa;&#xa;    t, x = series_x:at(i)&#xa;&#xa;    y = series_y:atTime(t)&#xa;&#xa;  &#xa;&#xa;    if y ~= nil then&#xa;        series_xy:push_back(x, y)&#xa;    end&#xa;end"/>
 </scripts>
</plugin>
```
---

### Layout (`<Tab>`)

Unter Tab werden alle Konfigurationen zu den einzelnen Tab-Layouts und ihrer Plot-Ausgabe getroffen.

| Code-Snippet                                                                          | Erklärung                                                                                                                                                                                                                                                                                                               |
| ------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `<Tab tab_name="" containers="1"> ... </Tab>`                                         | Tabs sind globale konfigurierbare Plot-Umgebungen<br><br>`tab_name`  -  vergibt dem Tab den Namen `containers`  - gibt die Anzahl genutzter Container wieder                                                                                                                                                            |
| `<Container> ... </Container>`                                                        | ein Container dient als lokale abgegrenzte konfigurierbare Plot-Umgebung                                                                                                                                                                                                                                                |
| `<DockSplitter sizes="0.6;0.4" orientation="-" count="2"> ... </DockSplitter>`        | Ein Dock-Splitter teilt gegebene Plot-Umgebungen horizontal oder vertikal und definiert die Dimensionen seiner Teilstücke<br><br>`sizes`  -  beschreibt das Verhältnis beider Teile<br>`orientation`  -  gibt an ob horizontal "-" oder vertikal "\|" geteilt wurde<br>`count`  -  gibt an wie viele Dock-Teile es gibt |
| `<DockArea name=""> ... </DockArea>`                                                  | DockAreas sind die vorgesehenen Plot-Bereiche, welche den jeweiligen Plot beinhalten<br><br>`name`  -  vergibt dem Plot-Bereich einen Namen                                                                                                                                                                             |
| `<plot flip_y="false" mode="TimeSeries" line_width="1.0" flip_x="false"> ... </plot>` | Hier werden die Konfigurationen zum jeweiligen Plot getroffen<br><br>`mode`  -  gibt an ob es sich um ein Timeseries oder XY-Plot handelt (mode kann nicht einfach in den anderen Plot umgeändert werden)                                                                                                               |
| `<range bottom="0.0" top="50.0" left="50.0" right="0.0"/>`                            | in Range sind die jeweiligen Achsengrenzen hinterlegt                                                                                                                                                                                                                                                                   |
| `<limitY max="50" min="0"/>`                                                          | hier wird explizit die Ordinatenachse eingegrenzt                                                                                                                                                                                                                                                                       |
| `<curve color="#cc0000" name="SOLL-Lenkwinkel"/>`                                     | Hier wird das Plot-Ergebnis konfiguriert<br><br>`color`  -  Färbt die Kurve durch RGB-Farbcode<br>`name`  -  Name der Timeseries für den Plot bzw. betitelt es die Kurve danach                                                                                                                                         |

```xml
   <Tab tab_name="Fahrbefehle" containers="1">
    <Container>
     <DockSplitter sizes="0.6;0.4" orientation="-" count="2">
      <DockArea name="Lenkwinkel [Grad] als Gesamtbetrag">
       <plot flip_y="false" mode="TimeSeries" line_width="1.0" flip_x="false">
        <range bottom="0.000000" top="50.000000" left="1776669808.937795" right="1776670108.931051"/>
        <limitY max="50" min="0"/>
        <curve color="#cc0000" name="SOLL-Lenkwinkel"/>
        <curve color="#00cc00" name="IST-Lenkwinkel"/>
       </plot>
      </DockArea>
      <DockArea name="Lenkwinkel [Grad] für rechts und links">
       <plot flip_y="false" mode="TimeSeries" line_width="1.0" style="Sticks" flip_x="false">
        <range bottom="0.000000" top="50.000000" left="1776669808.937795" right="1776670108.931051"/>
        <limitY max="50" min="0"/>
        <curve color="#ff7788" name="IST-Lenkwinkel (links)"/>
        <curve color="#4adc9e" name="IST-Lenkwinkel (rechts)"/>
       </plot>
      </DockArea>
     </DockSplitter>
    </Container>
   </Tab>
   
```
___

# <font color="#e36c09">Probleme:</font>
<font color="#c0504d">!!! Einige Probleme wurden scheinbar behoben durch die aktuelle PlotJuggler-Version 3.16. !!!</font>

## <font color="#e36c09">Plotting - Timeseries und ScatterXY</font>
<font color="#c0504d">!!! Die Ursache des Problems ist undefiniert, weshalb es zeitweise und willkürlich auftaucht !!!</font>
<font color="#c3d69b">Kein Problem mehr in PlotJuggler V3.16</font>

- **Ausgangslage:** 
	Es wurden vorher schon einige Timeseries in einzelnen Tabs und zuletzt ein ScatterXY in einem eigenen Tab geplottet. Jetzt soll wieder ein Timeseries geplottet werden, doch das wird verweigert und jeder neue Tab ist im XY (Scatter) Format gefangen und kann nicht rückwirkend umgestellt werden. Das betrifft vorallem ungespeicherte Layouts, da nach Auftreten des Problems, das ganze Layout verworfen werden muss und nach Anleitung des gegebenen Lösungsansatzes wieder eingerichtet werden sollte.

- **Fehlerausgabe:**
	![[Warning__XY-Plot.png]]

- **Lösungsansatz:**
	Damit alle Plots (Time-Series und XY-Scatter) gemeinsam in der Software genutzt werden können, ist scheinbar die Konfigurations-Reihenfolge des Plottings sehr wichtig. Um dieses Problem von Grundauf zu umgehen, sollten anfänglich alle Timeseries zuerst geplottet werden und zuletzt alle restlichen ScatterXY-Plots.
___

## <font color="#e36c09">Script-Editor - Funktionskopf</font>

- **Ausgangslage:**
	Bei der Script-Erstellung treten beim Speichern unerwartete Probleme auf, obwohl die erstellte Funktion im Grunde völlig fehlerfrei ist.

	Falsch:
	![[Script-Editor__marked_wrong_function-body.png]]
	auch Falsch:
	![[Script-Editor__cutted_wrong_function-body.png]]

- **Fehlerausgabe:**
	![[Lua-Error__function_calc.png]]

- **Lösungsansatz:**
	Es ist unbedingt darauf zu achten, dass der Funktionskopf implizit schon im Script-Editor vorgegeben ist und auf keinen Fall mit in das Script-Feld (seinen Funktions-Rumpf)  geschrieben werden darf. Die Script-Engine erkennt die Funktion sonst als unvollständig, und kann das Script nicht ordnungsgemäß kompilieren, obwohl der niedergeschriebene Script-Code richtig ist.

## <font color="#e36c09">Script-Editor - Funktionsrumpf</font>

- **Ausgangslage:**
	Bei der Script-Erstellung wurde eine Schleife genutzt und das "end" vergessen oder steht an falscher Stelle bzw. es wurde falsch eingerückt.
	(Beispielsweise nach Feststellen und Korrigieren des Funktions-Kopf-Fehlers)

- **Fehlerverhalten:** 
	Es werden sehr schnell die selben Fehlerfenster "<font color="#9bbb59">Error in Lua code</font>" angezeigt und PlotJuggler hängt sich auf.

- **Lösungsansatz:**
	Sobald das Problem auftritt, muss PlotJuggler schnell im Terminal beendet werden, um ein Aufhängen des Systems vorzubeugen. Nicht gespeicherte Layouts und Scripts sind dadurch leider verloren. Es ist darauf zu achten, dass bei Schleifen stets das "end" an passender Stelle steht.

## <font color="#e36c09">Script-Editor - Scriptänderung</font>
- **Ausgangslage:**
	Um bei einem vorhandenen Script den Script-Namen bzw. seine Timeseries-Quelle zu ändern empfiehlt es sich, das Script ganz zu löschen und nochmal von Grundauf zu erstellen. Besonders wenn sich die Namen gleichen oder die Quell-Timeseries ähneln, treten häufige Komplikationen auf, da bei einer anderen Vorgehensweise Script-Artefakte bestehen bleiben und beim nächsten Launch mit aufgelistet werden, was zu einer Unübersichtlichkeit der verfügbaren Scripts führt.
- **Lösungsansatz:**
	Jeweiliges Script in "<font color="#9bbb59">Custom Series: </font>" auswählen und löschen.
	Quell-TimeSeries wählen und Script erstellen mit "+" bei "<font color="#9bbb59">Custom Series: </font>".
	Script-Namen vergeben und speichern.
___

## <font color="#e36c09">RosBag - Import-Fehler</font>
<font color="#c3d69b">Scheinbar behoben in PlotJuggler V3.16</font>
- **Ausgangslage:**
	Ein RosBag besteht aus einer db3-File, welche die abspielbaren Daten enthält, sowie eine Konfigurations-File im YAML Format. Nur die YAML-File wird allgemein unterstützt und kann aufgerufen werden, wobei aber folgender Fehler ausgegeben wird.

- **Fehlerausgabe:**
	![[Rosbag-Error.png]]

- **Lösungsansatz:**
	Das Problem ist zwar schon in der Community bekannt, doch richtige Lösungsansätze sind dürftig oder für die aktuelle Situation nicht anwendbar. Dazu müssen wohl die DataLoad-Plugins intern analysiert und geprüft werden. 
___

## <font color="#e36c09">Topic - Package-Import-Fehler</font>

- **Ausgangslage:**
	Beim Auswählen aller nötigen Topics wirft PlotJuggler ein Package-Import-Fehler aus.
	
- **Fehlerausgabe:**
	![[Plugin-Error__nav2.png]]
	![[Plugin-Error__fixposition.png]]
	![[Plugin-Error__hyd_msgs.png]]

- **Lösungsansatz:**
	Es müssen alle Topics abgewählt werden, die Action-Calls auslösen. Es dürfen nur Topics ausgewählt sein, dessen Datentyp ein Message-Format enthält. Ist die Ursache eine andere, so ist das allgemeine Vorgehen, die problematischen Topics rekursiv und heuristisch aus der Liste abzuwählen. Dazu gibt die Fehlernachricht Aufschluss über die fehlerbehafteten Datentypen, welche systematisch aussortiert werden können.
	
	Das Fixposition-PlugIn benötigt für das richtige builden das fehlende SDK.
	Nav2-Messages können nicht genutzt werden, da bestimmte Message-Typen (beispielsweise Action-Calls) nicht für das Plotten vorgesehen sind oder nicht von PlotJuggler erkannt werden können.

---
# Quellen:

https://docs.ros.org/en/humble/p/plotjuggler/

https://slides.com/davidefaconti/introduction-to-plotjuggler

https://docs.px4.io/main/en/debug/plotting_realtime_uorb_data

Message-Package-Import-Issue:
https://www.reddit.com/r/ROS/comments/1kvowng/plotjuggler_cant_find_ros_package/

RosBag-Import-issue
https://www.reddit.com/r/ROS/comments/119qpbl/ros_2_bags_in_plotjuggler/?tl=de