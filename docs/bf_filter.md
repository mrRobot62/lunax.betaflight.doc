# _FILTER Ein mal Eins_
## Inhaltsverzeichnis

[TOC]

{{TOC}}

## Historie
| Version  |  Datum |  Inhalt |
|:-:|---|---|
| 0.1  |  August 2020 | initial  |

# Allgemeines
## Typische Frequenzen
Vibrationen die am Copter auftreten lassen sich in verschiede Frequenzbereiche unterteilen

* 0 ~ 20Hz: normaler Frequenzbereich während des Fliegens (keine Filterung der Signale)
* 20 ~ 80Hz: Vibrationen niedriger Frequenz (häufig Propwash)
* 80 ~ 1000Hz: hochfrequente Vibrationen (Noise) verursacht durch
	- Motorvibrationen & Prop-Resonanzen. Hier sieht man auch das typische Bild des Motor-Vibrations-Bandes [Harmonics](#harmonics)
	- Frame-Resonanzen
	- ...


## _Noise / Vibrationen_
Ist eine Bezeichnung für Rauschen bzw. Gyro-Signale die nicht zu den gewünschten/benötigten Signalen sind, die durch den FC verarbeitet werden müssen. Also Messwerte die irreguläre sind und die Performance des Copters negativ beeinflussen. 
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

**Hinweis:** 

Der DTerm-Anteil des PID-Controllers verstärkt Vibrationen deutlich. Daher ist eine Abstimmung der Filter mit dem DTerm gut abzustimmen.


Um Vibrationen zu analysieren musst du eine Blackbox-Analyse durchführen oder Tools wie `Blackbox-Explorer`, `PIDToolbox`oder `Plasmatree` verwenden.

Um einen ersten Eindruck von Vibrationen zu erhalten empfehle ich eine [Spektral-Analyse](https://github.com/mrRobot62/PIDtoolbox/wiki/SpectralAnalyzer).  Hier sieht man sehr deutlich in welchem Frequenzband Vibrationen an Deinem Copter auftreten.

Filter helfen, diese Vibrationen möglichst aus den Signalen für den Flight-Controller herauszufiltern um anschließend den Motoren möglichst saubere Signale zu übermitteln.

**Tip**
> Bevor du damit startetest die PID-Werte anzupassen, stelle Deine Filter optimal auf Deinen Copter ein. Erst dann fängst du mit den PID-Werten an.

Versuche durch Deine Filter ein maximales Gyro & DTerm Delay von <5ms zu erreichen. Du kannst das sehr einfach mit `PIDToolbox` erkennen.

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
Dies läßt sich nur bedingt durch Filter berreinigen, sondern über die PID-Einstellungen.
![Oszillation2][imgOszi2]


Nachfolgende Bildfolge zeigt die obigen beschriebenen Vibrationen der Motoren bei einem Punch. Analyse über PIDToolbox[^PDT]

|Roll | Info |
:---:|:---:|
| ![][imgPDTNoise1] | Snap-Roll, deutlich sieht man die Oszillation beim Erreichen des Setpoints. Interessant sind auch die Ausschläge kurz vor der Snap-Roll. Diese Ausschläge sind vermutlich durch Vibrationen der Motoren indiziert|
| ![ ][imgPDTNoise2] | Dieses Bild zeigt Motor1 beim Punch kurz vor der Snap-Roll, man sieht das Vibrationen den PTerm und DTerm beeinflussen, was in Schwankungen der Motordrehzahl zeigt. Dadurch entstehen auch Vibrationen. |

<div style="page-break-after: always;"></div>

_Harmonics_
-------------------------------
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
	
	LS[Loop-Start<br>d/t]:::LP --> GY(Gyro)
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
	M --> LE[Loop-End]:::LP
	
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

**Beachten**
> Ein fundamentaler Aspekt ist, je niedriger der Schwellwert(`cutoff`) für den Lowpass Filter ist - umso mehr muss gefiltert werden.

> Das wirkt sich auf dem Gesamtperformance aus. Daher gibt es mehrere andere Filter die andere Algorithmen verwenden und das System effizienter gestalten. 


## _Notch-Filter_
Notch-Filter[^NF] eigenen sich hervorragend zur Unterdrückung von Rauschen in einem sehr spezifischen Frequenzband.

Notch-Filter sind in der Regel effektiver zur Reduzierung von Motorrauschen als LPF- Filter aber ggf. müssen manuelle Abstimmungen durchgeführt werden um die Bandbreite und die Mittenfrequenz zu bestimmen (Notch=Kerbe) 

Weiterhin können Notch-Filter zur Reduzierung von Propwash genutzt werden.

![NotchFilter][imgDNF]

Der `Q-Faktor`(Quality-Factor) gibt die Güte des Notch-Filters an und beschreibt die Weite des Filters. Je größer der Q-Faktor, desto schmaler der Notch-Filter.

**Beispiel:**

Es wird festgestellt das wir ein Vibrationsspitze bei ca 260Hz haben (z.B. hervorgerufen durch Propwash), dann kann der Notch-Filter `cutoff` bei ca. 200Hz beginnen und bei 300Hz enden


## _Static-Filter_
Im Rahmen von Betaflight werden statische Filter als LowPass-Filter oder als Notch-Filter genutzt

## _Dynamic-Filter_
Dynamic Filter reduzieren ebenfalls Rauschen, wenn die entsprechenden Parameter richtig eingestellt sind. Schwingungen, Motorgeräusche, ... können durch die Dynamic-Filter reduziert werden. 

Ein Dynamic-Filter ist ein Algorithmus, der die Frequenz des Rauschens erkennen kann und er kann den Notch-Filter verwenden, um ihn automatisch zu reduzieren. 

**Nachteil** von Dynamic-Filters ist die Erhöhung der CPU-Last und die Delays. 

Betaflight-Filter
=========================================================
## _RPM-Filter_
RPM-Filter [^RPM] sind eine besondere Filtertechnik in Betaflight. RPM-Filter basieren auf mehreren [Notch-Filter](#bf-dynamic-notchfilter) die präzise darauf ausgerichtet sind Motorvibrationen und ihre `Harmonics` zu filtern und möglichst komplett zu elimenieren. Für jeden Motore können bis zu drei Notch-Filter (max 3 Harmonics) generiert werden (Pro Achse). Das heißt insgesamt steht eine Bank von 36 Notch-Filtern zur Verfügung. 3 Achsen * 3 `Harmonics` * 4 Motoren = 36 Notch-Filter.

Die RPM-Filter basieren auf die Drehzahl der Motoren und dem bidrektionalen DShot-Protokoll in Zusammenspiel mit den Gyrodaten - daher auch `Gyro-RPM-Filter`

**BEACHTEN**
> RPM-Filter benötigen für BLHeli32 ESC die aktuellste Firmware `>= 32.7`

> Für BLHeli_S ESCs empfehle ich die FW von (auch wenn sie Geld kosten) JFlight[^JFLIGHT]


## _BF - Dynamic-Lowpassfilter_
Neu ab [BF 4.0] (https://github.com/betaflight/betaflight/wiki/4.0-Tuning-Notes#Dynamic-lowpass-filtering).
BF bietet nun die Möglichkeit, die Grenzfrequenz des LPF[^LPF] bei Vollgas im Vergleich zu 0-Throttle sanft auf einen höheren Wert zu verschieben. Die dem ersten Gyro und dem D-Lowpass-Filter zugewiesene Grenzfrequenz steigt nun dynamisch mit zunehmender Drosselung entlang einer Kurve an, die effektiv die Motordrehzahl beeinflusst. Dadurch wird die Verzögerung bei Vollgas verringert, und der Dynamische-Notch Filter [^NF] kann die Motor-Vibrations-Peaks besser verfolgen.

## _BF - Static Notchfilter_
Statische Notchfilter werden explizit mit der Center-Frequenz auf auf die Herzzahl der höchsten Vibrationen (Peak) gesetzt.
In BF kann man zwei dieser Notch-Filter aktivieren. Angegeben werden jeweils die cutoff-Frequenz und die Center-Frequenz.

## _BF - Dynamic-Notchfilter_
Ab `BF 3.1` kann man auch `Dynamic Notch Filter`[^NF]  einsetzen. Dieser hocheffiziente Dynamische Notch-Filter setzt sich automatisch auf den Motor-Peak - also den aktuelle höchste Frequenz der Motor-Vibrationen, basieren auf die jeweils aktuelle FFT-Analyse. Hierdurch wirkt dieser Notch-Filter in allen Throttle-Stellungen und wandert demnach durch das Frequenzband.

Dynamische Notch-Filter haben eine geringere Latzenzzeit als LP-Filter. Notch-Filter sind sehr effektiv und bei einer guten Abstimmung kann man sogar auf LPF-Filter verzichten. Dadurch steigert sich die Gesamtperformance des Copters.

LP-Filter aber einfach abschalten sollte wirklich mit bedacht gemacht werden.

Der Dyn-Notch-Filter ist per default eingeschaltet.

Es gibt zwei Notch-Filter für den GYRO. Einer oder beide können bei Bedarf abgestellt werden. 

Default-Values sind auf 200Hz-400Hz. Diese Grenzfrequenzen arbeiten sehr gut und funktionieren bei den meisten Coptern. 

Für eine Feinabstimmung ist es notwendig eine Blackbox- Auswertung durchzuführen (Blackbox-Explorer, PIDToolbox, Plasmatree) 


## _BF - Static-Lowpassfilter_
In Betaflight wird zwischen zwei Static-LPF Filtern unterschieden

* PT1
* BIQUAD

Um einen LPF-Filter zu nutzen muss grundsätzlich die untere Grenzfrequenz (`cutoff`)) angegeben werden. Der Filter beginnt dann ab dieser Frequenz zu arbeiten. Die Cutoff-Frequenz sollte **nicht** unter 80Hz liegen, da hier die normalen Flugfrequenzen liegen.

### PT1
Dieser Filter hat eine etwas sanftere Kurve und ist ein LPF-Filter 1. Ordnung und hat dadurch eine geringere Latzenzzeit. Der Nachteil dieses Filters, er filtert nicht so stark Vibrationen aus dem Signal (bedingt durch seine Kurvenausprägung)

**TIP**
> verwende für den ersten LPF-Filter, denn er ist der schnellere Filter.

### BIQUAD
Dieser Filter hat eine deutliche steilere Kurve und filtert besser als ein PT1. Er ist ein Filter 2. Ordnung. Dadurch ist die Latzenzzeit schlechter, das Filterergebniss besser.

## _BF - Gyro-RPM-Filter_
Ein mächtiges neues Feature, welches mit BF 4.0 eingeführt wurde und in den nachfolgenden Releasen weiter verbessert wurde.
Die RPM Filter wurden weiter oben schon beschrieben, nachfolgend eine Reihe von Detailinformationen.



## _BF - Gyro-LowPass Filter_

## _BF - DTerm LowPass Filter_
Der DTerm verstärkt alles - auch Vibrationssignale - somit können ungefilterte DTerm Werte die direkt zu den Motoren gesendet werden dazu führen, dass die Motoren heiß werden oder sogar überhitzen und zerstört werden.

Hier kommen jetzt die DTerm-Lowpass Filter -> Einstellung wie bei den Static-Lowpassfilter

Allgemeinen ist ein Biquad-Filter das sinnvolle Minimum an Filterung mit einer Frequenz um 100 Hz bis hinunter zu 80 Hz, wenn man heiße Motoren hat.

**Beachten:**
> Den DTERM-LPF solltet ihr grundsätzlich **nicht** entfernen in Eurer Konfiguration!

***
[imgLPF]: images/lowpass-filter.png "LowPass-Filter"
[imgDNF]: images/classic_notchfilter_qfactor.png "Notch-Filter"
[imgOszi0]: images/oszillation0.png "Oszillation - Schaubild"
[imgOszi1]: images/oszillation1.png "Blackbox-Explorer - Oszillation"
[imgOszi2]: images/oszillation2.png "Blackbox-Explorer - Oszillation unruhige Motoren"
[imgNoise1]: images/noise1.png "PIDToolbox - Vibrationen (Motor-Noise-Band)"
[imgPDTNoise1]: images/pidtoolbox_snaproll_oszillation1.png
[imgPDTNoise2]: images/pidtoolbox_snaproll_vibration_m1.png


[^RPM]: [BF-RPMFilter](https://github.com/betaflight/betaflight/wiki/Bidirectional-DSHOT-and-RPM-Filter)
[^JFLIGHT]: https://jflight.net/index.php
[^NF]: Notch-Filter = Kerb-Filter 
[^LPF]: Lowpass-Filter (Tiefpassfilter)
[^DTF]: Filter auf den DTerm-Wert
