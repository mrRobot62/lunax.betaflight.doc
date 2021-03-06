
# _FILTER Ein mal Eins_

![LunaX](./images/lunax_logo.png)

## Inhaltsverzeichnis

- [_FILTER Ein mal Eins_](#filter-ein-mal-eins)
	- [Inhaltsverzeichnis](#inhaltsverzeichnis)
	- [Historie](#historie)
- [Allgemeines](#allgemeines)
	- [Typische Frequenzen](#typische-frequenzen)
	- [_Noise / Vibrationen_](#noise--vibrationen)
	- [_Oszillation_](#oszillation)
	- [_Harmonics_](#harmonics)
- [Gyro-Data-Filtering](#gyro-data-filtering)
- [Grundlage Filter-Arten](#grundlage-filter-arten)
	- [_Lowpass-Filter_](#lowpass-filter)
	- [_Notch-Filter_](#notch-filter)
	- [_Static-Filter_](#static-filter)
	- [_Dynamic-Filter_](#dynamic-filter)
- [Betaflight-Filter](#betaflight-filter)
	- [_RPM-Filter_](#rpm-filter)
	- [_BF - Dynamic-Lowpassfilter_](#bf---dynamic-lowpassfilter)
	- [_BF - Static Notchfilter_](#bf---static-notchfilter)
	- [_BF - Dynamic-Notchfilter_](#bf---dynamic-notchfilter)
	- [_BF - Static-Lowpassfilter_](#bf---static-lowpassfilter)
		- [PT1](#pt1)
		- [BIQUAD](#biquad)
	- [_BF - Gyro-RPM-Filter_](#bf---gyro-rpm-filter)
	- [_BF - Gyro-LowPass Filter_](#bf---gyro-lowpass-filter)
	- [_BF - DTerm LowPass Filter_](#bf---dterm-lowpass-filter)
- [Appendix](#appendix)
	- [Bilder in größerem Format](#bilder-in-größerem-format)
	- [!](#)

## Historie
| Version  |  Datum |  Inhalt |
|:-:|---|---|
| 0.1  |  August 2020 | initial  |
|0.2| August 2020| Updates Dynamic Filter und anderes | 

-------------------------------------------------------------
# Allgemeines
## Typische Frequenzen
Vibrationen die am Copter auftreten lassen sich in verschiede Frequenzbereiche unterteilen

* 0 ~ 20Hz: normaler Frequenzbereich während des Fliegens (keine Filterung der Signale)
* 20 ~ 80Hz: Vibrationen niedriger Frequenz (häufig Propwash)
* 80 ~ 1000Hz: hochfrequente Vibrationen (Noise) verursacht durch
	- Motorvibrationen & Prop-Resonanzen. Hier sieht man auch das typische Bild des 
	Motor-Vibrations-Bandes [Harmonics](#harmonics)
	- Frame-Resonanzen
	- ...


-------------------------------------------------------------
## _Noise / Vibrationen_
Ist eine Bezeichnung für Rauschen bzw. Gyro-Signale die nicht zu den gewünschten/benötigten Signalen sind, die durch den FC verarbeitet werden müssen. Dies entspricht demnach Messwerten, die irregulär sind und die Performance des Copters negativ beeinflussen. 

Noise (Rauschen) kann ausgelöst werden durch: 

* äußere Einflüsse (z.B. Wind, Kontakte mit Ästen, ...) 
* Vibrationen am Chassis (z.B. lose schrauben, dünne schwingende Arme, ...) 
* Vibrationen durch Motoren(z.B. Lagerschäden, Unwucht, ...) 
* Vibrationen durch Props (z.B. Unwucht, Prop-Wash, ...) 
* Kombinationen aus allem •.

Noise wird durch den Gyro und anschließend durch den PID- Controller verarbeitet und kann zu fehlerhaften Verhalten führen. 

Eingesetzte Filter [LPF](#lowpass-filter)[^LPF], [NOTCH](#notch-filter)[^NF], [RPM](#rpm-filter)[^RPM],[DTerm](#bf-dterm-lowpass-filter)[^DTF] versuchen dieses Rauschen zu eliminieren.

Eine Deiner Hauptaufgabe beim Tunen Deines Copters ist, dass du diese Vibrationen in den Griff bekommst ohne deutliche Delay zu bekommen.

![Noise1][imgNoise1]

!!! note "Hinweis"
	Der DTerm-Anteil des PID-Controllers verstärkt Vibrationen deutlich. Daher ist eine Abstimmung der Filter mit dem DTerm[^DTF] gut abzustimmen.


Um Vibrationen zu analysieren musst du eine Blackbox-Analyse durchführen oder Tools wie `Blackbox-Explorer`, `PIDToolbox` oder `Plasmatree` verwenden.

Um einen ersten Eindruck von Vibrationen zu erhalten empfehle ich eine [Spektral-Analyse](https://github.com/mrRobot62/PIDtoolbox/wiki/SpectralAnalyzer).  Hier sieht man sehr deutlich in welchem Frequenzband Vibrationen an Deinem Copter auftreten.

Filter helfen, diese Vibrationen möglichst aus den Signalen für den Flight-Controller herauszufiltern um anschließend den Motoren möglichst saubere Signale zu übermitteln.

!!! note "Tip"
	Bevor du damit startetest die PID-Werte anzupassen, stelle Deine Filter optimal auf Deinen Copter ein. Erst dann fängst du mit den PID-Werten an. Versuche durch Deine Filter ein maximales Gyro & DTerm Delay von <5ms zu erreichen. Du kannst das sehr einfach mit `PIDToolbox` erkennen.

-------------------------------------------------------------
## _Oszillation_
Eine Oszillation ist eine Schwingung. In Bezug auf den PID-Controller ist es ein Über- und Unterschwingen zur Ideallinie. 

![Oszillation0][imgOszi0]

* **SetPoint** = SOLL-Wert = gestrichelte Linie (das ist das RC-Kommando z.B. 700deg/sec)
* **Orangene-Linie** ein Versuch sich möglichst schnell an den Soll-Wert heranzutasten. Man überschießt den Soll-Wert korrigiert und fällt unter den Soll-Wert, korrigiert dann wieder überschießt man usw. bis man irgendwann den Sollwert Erreicht. 

* **Ideal** ist es, wenn man die **grüne Linie**) möglichst schnell ohne bzw. geringer Oszillation den Sollwert erreicht 

* **Blauer Bereich** Schafft es der PID-Controller überhaupt nicht den Soll-Wert zu erreichen dann bewegt man sich in diesem Bereich. Schnelles Oszillieren wird als Vibration empfunden.

Nachfolgend ein Bild aus dem Blackbox-Explorer bei dem man sehr deutlich diese Oszillation sehen kann

![Oszillation1][imgOszi1]

Im nachfolgenden Bild sieht man im oberen Graphen die Oszillation auf der Roll-Achse und welche Auswirkungen diese auf die Motoren haben.
Dies läßt sich nur bedingt durch Filter bereinigen, sondern über die PID-Einstellungen.
![Oszillation2][imgOszi2]


Nachfolgende Bildfolge zeigt die obigen beschriebenen Vibrationen der Motoren bei einem Punch. Analyse über PIDToolbox[^PDT]

|<div style="width:400px">Roll</div> | Info |
|---|---|
| ![][imgPDTNoise1l] | Diese Ansicht zeigt den Verlauf kurz vor einer Snap-Roll. Der obere Graph zeigt die Roll-Achse. Der untere Graph zeigt zeitgleich Throttle. Man sieht auch, das ich meine Roll-Rates auf 700deg/sec stehen|
| ![][imgPDTNoise2l] | Dieser Graph zeigt Details der Roll-Daten. Die schwarze Linie entspricht den Gyro-Daten, die rote Linie ist der Setpoint, die grün Line zeigt den PTerm und die blaue Linie den DTerm. Hier sieht man das PTerm und DTerm gegenläufig arbeiten. Weiterhin erkennt man am die Oszillation des Gyros beim Erreichen des Setpoints. |
| ![][imgPDTNoise3l] | Nun eine noch genauere Darstellung der Roll-Daten beim Erreichen des Setpoints. Deutliche Oszillation des Gypros was darauf hindeutet, dass der PID-Controller noch nicht optimiert ist. |
| ![][imgPDTNoise4l] | Hier nun die Motordaten kurz bei Full-Throttle am Beispiel von Motor 1. Schwarze Linie ist Throttle, rote Linie ist Motor1. Die Vibrationen die man vor der Snap-Roll im Bild 2 deutlich sieht, sieht man auch hier in den Motordaten. Diese Motor-Signale können zu den Vibrationen führen und auch zur Motor-Überhizung. Wichtig ist, das man die Motor-Temperatur nach einem Flug überprüft - erst recht, wenn man versucht seinen Copter zu tunen. Zwischen Throttle reduzieren und Beginn der Snap-Roll liegen ca. 500ms|




-------------------------------------------------------------
## _Harmonics_
Harmonics oder Harmonische sind wiederkehrende Amplituden (Vibrationen) in den gleichen Frequenzabständen.

Das nachfolgende Bild zeigt deutlich drei sich im gleichen Abstand wiederholende Frequenz-Peaks von Vibrationen (Motor Vibrationen)

Beginnend mit dem Motor-Noise-Peak bei 158hz, dann die erste Harmonische Amplitude bei 316hz (2x158hz) und die dritte Amplitude bei 474hz (3x158).


![bbe_harmonics1.png](images/bbe_harmonics1.png "Harmonics")

Gyro-Data-Filtering
=========================================================
Signal & Prozessfluß zur Filterung

```mermaid
graph LR
	classDef dbg fill:#ddd
	classDef LP fill:#f96
	
	LS((Loop-Start<br>d/t)):::LP --> GY(Gyro)
	GY -.-> DBG1[[DebugMode<br>Gyro_Raw<br>Notch<br>FFT]]:::dbg
	GY --> DNF
	DBG1 -.-> DNF(Dynamic Notch Filter)
	DNF -.-> DBGFFT[[DebugMode<br>FFT]]:::dbg
	DNF --> SNF(Static Notch Filter)
	DBGFFT -.-> SNF

```

```mermaid
graph LR
	classDef dbg fill:#ddd

	SNF(Static Notch Filter) --> GLPF
	SNF -.-> DBGGY[[DebugMode<br>Gyro]]:::dbg
	DBGGY -.-> GLPF(Gyro LPF<br>LowPass Filter)
	GLPF --> PID(PID Loop)

```

```mermaid
graph LR
	classDef dbg fill:#ddd

	PID(PID Loop) -.-> DBGDF[[DebugMode<br>DFilter]]:::dbg
	PID --> DTLPF(DTerm<br>LPF)
	DBGDF -.-> DTLPF
	DTLPF --> DTNF(DTerm<br>Notch Filter)
```

```mermaid
graph LR
	classDef dbg fill:#ddd
	classDef LP fill:#f96

	DTNF(DTerm<br>Notch Filter) --> M(Motoren)
	M --> LE((Loop-End)):::LP
	
```


Grundlage Filter-Arten
=========================================================
## _Lowpass-Filter_
Niedrige Frequenzen werden durch den Filter durchgelassen, hohe Frequenzen werden gedämpft.

Hohe Frequenzen sind in der Regel im System nur Rauschen bzw. Vibrationen und werden für die Flugdaten nicht benötigt, daher versucht man sie herauszufiltern.

Mit dem LPF[^LPF] wird eine Grenzfrequenz (`cutoff`) angelegt und der FC reduziert die Signale die oberhalb dieser Grenzfrequenz liegen. 

Die Dämpfungskurve ist eine Steigung. d.h. je höher die Signalfrequenz desto stärker die Dämpfung 

![Low Pass Filter][imgLPF]

Für den Einsatzbereich des Quadcopters ist der Frequenzbereich von 0-80Hz relevant, alles darüber sollte möglichst effizient weg gefiltert werden. 

!!! note "Beachten"
	Ein fundamentaler Aspekt ist, je niedriger der die Grenzfrequenz (`cutoff frequency`) für den Lowpass Filter ist - umso mehr muss gefiltert werden. Das wirkt sich auf dem Gesamtperformance aus. Daher gibt es mehrere andere Filter, die andere Algorithmen verwenden und das System effizienter gestalten.  


-------------------------------------------------------------
## _Notch-Filter_
Notch-Filter[^NF] eigenen sich hervorragend zur Unterdrückung von Rauschen in einem sehr spezifischen Frequenzband.

Notch-Filter sind in der Regel effektiver zur Reduzierung von Motorrauschen als LPF- Filter aber ggf. müssen manuelle Abstimmungen durchgeführt werden um die Bandbreite und die Mittenfrequenz zu bestimmen (Notch=Kerbe) 

Weiterhin können Notch-Filter zur Reduzierung von Propwash genutzt werden.

![NotchFilter][imgDNF]

Der `Q-Faktor`(Quality-Factor) gibt die Güte des Notch-Filters an und beschreibt die Weite des Filters. Je größer der Q-Faktor, desto schmaler der Notch-Filter.

!!! note "Beispiel"
	Du stellst fest, dass eine Vibrationsspitze bei ca 260Hz auftritt (z.B. hervorgerufen durch Propwash), dann kann der Notch-Filter `cutoff` bei ca. 200Hz beginnen und bei 300Hz enden. Der Peak soll in der Mitte des Notch-Filters liegen, somit werden auch Frequenzen etwas unter und über dieses Peaks gefiltert.


-------------------------------------------------------------
## _Static-Filter_
Im Rahmen von Betaflight werden statische Filter als LowPass-Filter oder als Notch-Filter genutzt

-------------------------------------------------------------
## _Dynamic-Filter_
Dynamic Filter reduzieren ebenfalls Rauschen, vorausgesetzt die entsprechenden Parameter richtig eingestellt sind. Schwingungen, Motorgeräusche, etc. können durch die Dynamic-Filter reduziert werden. 

Ein Dynamic-Filter ist ein Algorithmus, der die Frequenz des Rauschens erkennen kann und er kann den Notch-Filter verwenden, um ihn automatisch zu reduzieren. 

!!! note "Nachteil"

	von Dynamic-Filters ist die Erhöhung der CPU-Last und die Delays. 

Betaflight-Filter
=========================================================
## _RPM-Filter_
RPM-Filter [^RPM] sind eine besondere Filtertechnik in Betaflight und wurden mit [BF 4.0]() eingeführt. Die RPM-Filter können über eine Vielzahl an Parameter[^RPMPARAM] konfiguriert werden. Eine detaillierte Beschreibung findet man im BF-Wiki [BF-RPMFilter](https://github.com/betaflight/betaflight/wiki/Bidirectional-DSHOT-and-RPM-Filter). 

RPM-Filter basieren auf mehreren [Notch-Filter](#bf-dynamic-notchfilter) die präzise darauf ausgerichtet sind Motorvibrationen und ihre `Harmonics` zu filtern und möglichst komplett zu elimenieren. Für jeden Motor können bis zu drei Notch-Filter (max 3 Harmonics) generiert werden (Pro Achse). Das heißt insgesamt steht eine Bank von 36 Notch-Filtern zur Verfügung. 3 Achsen * 3 `Harmonics` * 4 Motoren = 36 Notch-Filter.

Die RPM-Filter basieren auf die Drehzahl der Motoren und dem bidrektionalen DShot-Protokoll in Zusammenspiel mit den Gyrodaten - daher auch `Gyro-RPM-Filter`

!!! danger "Wichtig"
	RPM-Filter benötigen für BLHeli32 ESC die aktuellste Firmware `>= 32.7`. Für BLHeli_S ESCs empfehle ich die FW von (auch wenn sie Geld kosten) JFlight[^JFLIGHT]

!!! note "Tip"
	Durch die Einführung der RPM-Filter ist es möglich andere Filter (z.B. LPF) zu reduzieren bzw. komplett auszuschalten, dies geht zu Gunsten der Gesamtperformance des Copters und die Delays können dadurch weiter reduziert werden.


-------------------------------------------------------------
## _BF - Dynamic-Lowpassfilter_
Neu ab [BF 4.0 - DLPF] (https://github.com/betaflight/betaflight/wiki/4.0-Tuning-Notes#Dynamic-lowpass-filtering).
BF bietet nun die Möglichkeit, die Grenzfrequenz des LPF[^LPF] bei Vollgas im Vergleich zu 0-Throttle sanft auf einen höheren Wert zu verschieben. Die dem ersten Gyro und dem D-Lowpass-Filter zugewiesene Grenzfrequenz steigt nun dynamisch mit zunehmender Drosselung entlang einer Kurve an, die effektiv die Motordrehzahl beeinflusst. Dadurch wird die Verzögerung bei Vollgas verringert, und der Dynamische-Notch Filter [^NF] kann die Motor-Vibrations-Peaks besser verfolgen.

-------------------------------------------------------------
## _BF - Static Notchfilter_
Statische Notchfilter werden explizit mit der Center-Frequenz auf auf die Herzzahl der höchsten Vibrationen (Peak) gesetzt.
In BF kann man zwei dieser Notch-Filter aktivieren. Angegeben werden jeweils die cutoff-Frequenz und die Center-Frequenz.

-------------------------------------------------------------
## _BF - Dynamic-Notchfilter_
Ab `BF 3.1` kann man auch `Dynamic Notch Filter`[^NF]  einsetzen. Dieser hocheffiziente Dynamische Notch-Filter setzt sich automatisch auf den Motor-Peak - also den aktuelle höchste Frequenz der Motor-Vibrationen, basieren auf die jeweils aktuelle FFT-Analyse. Hierdurch wirkt dieser Notch-Filter in allen Throttle-Stellungen und wandert demnach durch das Frequenzband.

Dynamische Notch-Filter haben eine geringere Latenzzeit als LP-Filter. Notch-Filter sind sehr effektiv und bei einer guten Abstimmung kann man sogar auf LPF-Filter verzichten. Dadurch steigert sich die Gesamtperformance des Copters.

LP-Filter aber einfach abschalten sollte wirklich mit bedacht gemacht werden.

Der Dyn-Notch-Filter ist per default eingeschaltet.

Es gibt zwei Notch-Filter für den GYRO. Einer oder beide können bei Bedarf abgestellt werden. 

Default-Values sind auf 200Hz-400Hz. Diese Grenzfrequenzen arbeiten sehr gut und funktionieren bei den meisten Coptern. 

Für eine Feinabstimmung ist es notwendig eine Blackbox- Auswertung durchzuführen (Blackbox-Explorer, PIDToolbox, Plasmatree) 


-------------------------------------------------------------
## _BF - Static-Lowpassfilter_
In Betaflight wird zwischen zwei Static-LPF Filtern unterschieden

* PT1
* BIQUAD

Um einen LPF-Filter zu nutzen muss grundsätzlich die untere Grenzfrequenz (`cutoff`)) angegeben werden. Der Filter beginnt dann ab dieser Frequenz zu arbeiten. Die Cutoff-Frequenz sollte **nicht** unter 80Hz liegen, da hier die normalen Flugfrequenzen liegen.

### PT1
Dieser Filter hat eine etwas sanftere Kurve und ist ein LPF-Filter 1. Ordnung und hat dadurch eine geringere Latenzzeit. Der Nachteil dieses Filters, er filtert nicht so stark Vibrationen aus dem Signal (bedingt durch seine Kurvenausprägung)

!!! note "Tip"
	verwende für den ersten LPF-Filter die Einstellung **PT1**, denn er ist der schnellere Filter.


### BIQUAD
Dieser Filter hat eine deutliche steilere Kurve und filtert besser als ein PT1. Er ist ein Filter 2. Ordnung. Dadurch ist die Latenzzeit schlechter, das Filterergebniss besser.


-------------------------------------------------------------
## _BF - Gyro-RPM-Filter_
Ein mächtiges neues Feature, das mit BF 4.0 eingeführt wurde und in den nachfolgenden Releasen weiter verbessert wurde.
Die RPM Filter wurden weiter oben schon beschrieben, nachfolgend eine Reihe von Detailinformationen.

Siehe auch die [dazugehörigen Parameter](siehe bf_tuning_parameters Kapitel gyro-filter-gyro-rpm-notch-filter)

-------------------------------------------------------------
## _BF - Gyro-LowPass Filter_

-------------------------------------------------------------
## _BF - DTerm LowPass Filter_
Der DTerm verstärkt alles - auch Vibrationssignale - somit können ungefilterte DTerm Werte die direkt zu den Motoren gesendet werden dazu führen, dass die Motoren heiß werden oder sogar überhitzen und zerstört werden.

Hier kommen jetzt die DTerm-Lowpass Filter -> Einstellung wie bei den Static-Lowpassfilter

Allgemeinen ist ein Biquad-Filter das sinnvolle Minimum an Filterung mit einer Frequenz um 100 Hz bis hinunter zu 80 Hz, wenn man heiße Motoren hat.

!!! note "Beachten"
	Den DTERM-LPF solltet ihr grundsätzlich **nicht** in Eurem Setup entfernen!

-------------------------------------------------------------
# Appendix

## Bilder in größerem Format
![][imgPDTNoise1h]
![][imgPDTNoise2h]
![][imgPDTNoise3h]
![][imgPDTNoise4h]
-------------------------------------------------------------


***
[imgLPF]: images/lowpass-filter.png "LowPass-Filter"
[imgDNF]: images/classic_notchfilter_qfactor.png "Notch-Filter"
[imgOszi0]: images/oszillation0.png "Oszillation - Schaubild"
[imgOszi1]: images/oszillation1.png "Blackbox-Explorer - Oszillation"
[imgOszi2]: images/oszillation2.png "Blackbox-Explorer - Oszillation unruhige Motoren"

[imgNoise1]: images/noise1.png "PIDToolbox - Vibrationen (Motor-Noise-Band)"
[imgPDTNoise1]: images/pidtoolbox_snaproll_oszillation1.png
[imgPDTNoise1l]: images/oszillation_lres_0b.png
[imgPDTNoise1h]: images/oszillation_hres_0b.png

[imgPDTNoise2]: images/pidtoolbox_snaproll_vibration_m1.png
[imgPDTNoise2l]: images/pidtoolbox_snaproll_vibration_m1.png
[imgPDTNoise2h]: images/oszillation_hres_1.png
[imgPDTNoise2l]: images/oszillation_lres_1.png
[imgPDTNoise3h]: images/oszillation_hres_1b.png
[imgPDTNoise3l]: images/oszillation_lres_1b.png
[imgPDTNoise4h]: images/oszillation_hres_2a.png
[imgPDTNoise4l]: images/oszillation_lres_2a.png



[^RPM]: [BF-Wiki RPM-Filter](https://github.com/betaflight/betaflight/wiki/Bidirectional-DSHOT-and-RPM-Filter)
[^RPMPARAM]: siehe bf_tuning_parameters
[^JFLIGHT]: [JFlight ESC FW]: (https://jflight.net/index.php)
[^NF]: Notch-Filter = Kerb-Filter 
[^LPF]: Lowpass-Filter (Tiefpassfilter)
[^DTF]: Filter auf den DTerm-Wert
