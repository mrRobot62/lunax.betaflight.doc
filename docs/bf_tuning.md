# BF Allgemeine Tuning Tips 

## Allgemeines
Um eine möglichst gut abgestimmten Copter zu besitzen sollte nachfolgende Reihenfolge versucht werden einzuhalten

1. **_Sauber bauen_**. Vermeide schlackernde Kabel. Der FC[^FC] sollte vibrationsgedämpft verbaut sein. Prüfe ob den Gyro etwas berührt (**strikt vermeiden**). Sind alle Schrauben fest

2. **Versuche auf die aktuelleste BF-Version aufzusetzen.** Mache vor Deinen Änderungen ein Backup der aktuellen FW. Sichere Deine Konfiguration mit `diff all`. Neue BF-Version versprechen Bugfixings und häufig verbesserte Filter/Tunig-Möglichkeiten. Leider ist der Update immer mit Arbeit verbunden.

3. **Sender kalibrieren**. Im Receiver-Tab sollten für alle drei Achsen die Einstellungen zwischenn **1000** und **2000** liegen. Hintergrund ist, liegen die aktuellen Werte unter-/oberhalb wird das RC-Signal beschnitten oder gespreizt, beides sorgt dafür, dass die Signalverarbeitung nicht optimal ist.

4. **Prüfe im BF-Configurator die Lage des Copters.** Der grüne Pfeil symbolisiert **Vorne**. Neige den Copter nach unten (PITCH-Forward), der simulierte Copter **muss** sich ebenfalls nach unten neigen. Wiederholen für alle Achsen. Der simulierte Copter **muss exakt** das gleiche tun als der echte Copter.
Funktioniert das nicht - so kann man es prüfen:
	- Die Gyro-Lage in BF-Configurator stimmt nicht mit dem überein, wie Dein Gyro tatsächlich verbaut ist. 
	- Gehe schrittweise vor und ändere immer in **90 Grad** Schritten
	- Beginne mit der PITCH-Achse (Querachse) und ändere die Einstellung in 90Grad Schritten solange bis der simulierte Copter das tut, was Dein Copter in der Hand auch tut. 
	- Dann die Roll-Achse (Längsachse). Hier genauso vorgehen. Kippst du Copter in Flugrichtung nach links, dann muss der simulierte Copter auch nach links kippen, gleiches gilt für rechts.
	- YAW-Achse (Hochachse): drehe den Copter um Hochachse. Der simulierte Copter muss sich nun auch nach links/rechts beweg

	> Das ist eine **sehr, sehr wichtige** Funktionsprüfung, ansonsten wird der Copter beim Erststart vermutlich direkt einen Salto schlagen

5. **Motordrehrichtung prüfen** 
	* Motor 2+3 müssen CCW[^CWCCW] drehen
	* Motor 1+4 müssen CW drehen

![][imgMOTOREN]
6. Prop-Montage, darauf achten, dass die Props in der korrekten weise montiert werden. Grundsätzlich gilt: Motor 1+3 und Motor 2+4 haben immer die gleichen Props montiert.

![][imgProps]
## Filter reduzieren
Eigentlich votiere ich zu filtern, das heißt aber nicht "einfach alle Filter einschalten" sondern Filter mit bedacht aktivieren und andere Filter deaktivieren.

Das Reduzieren der Filter erhöht deutlich die Performance in Betaflight und die Latenzzeiten verringern sich. Dies resultiert in einem besseren "Stick-Gefühl", irgendwie direkter.

Andererseits geben Filter eine weicheres Gefühl, der Copter liegt irgendwie ruhiger, alles ist etwas gedämpfter (gemütlicher ;-) )

Betaflight ist aber für 1000ende von Piloten konzipiert und von Race bis Smooth-Cruiesen für HD-Qualitätsaufnahmen - und somit stehen auch eine Vielzahl an Setups zur Verfügung.

Also für jeden etwas.

Das im Vorwege.

Meine Setups die ich für meine Copter einstelle sind eher direkter - als Freestyler mag ich das lieber und ist mir auch wichtig.

Ich lebe mit Propwash und kleineren anderen Vibrationen. Filtere mich daher nicht "zu Tode" und vielleicht sind meine PID-Werte auch nicht optimal - aber ich fühle mich wohl mit meinen Coptern.

## Tuning in a Nutshell
> * 1: fliegen ist es was wir wollen

> * 2: Konfiguriere Dicht nicht zu Tode

> * 3: Baue so gut und sauber wie möglich

> * 4: Das Setup sollte wenn möglich für Deine Copter gleicher Klasse mehr oder minder "kopierbar" sein. 

> * 5: beginne mit den Default-Einstellungen von PIDs und Filter

> * 6: Erstflug - und prüfen wie das Setup ist.

> * 7: Erstelle dir ein Tuning-Logbuch indem du pro Flug dir die Einstellungen/Änderungen merkst.

> * 8: zum Tunen ist der Blackbox-Explorer, PIDToolbox unerläßlich.

> * 9: Bevor du an den PID-Werten arbeitest, optimiere die Filter.

> * 10: Nutze DN-Filter[^DNF] mehr als LPF-Filter[^LPF] 

> * 11: behalte immer die Motor-Temperaturen im Auge, wenn du tunest

> * 12: Filter sind ok, dann PID-Tuning durchführen. 

> * 13: Die Schritte 9-12 iterativ an Deinen Copter adaptieren und Schrittweise verbessern. Einen Schritt nach dem anderen. Nicht 2 Dinge gleichzeitig ändern.

> * 98: **Tunen kostet viel Zeit**
> * 99: **weniger tunen kann mehr sein**

Mit diesen Punkten baue und fliege ich meine 5"er 


## Heiße Motoren
Im allgemeinen gilt **warme Motoren** sind in Ordnung, werden sie heiß, dann muss etwas am Setup geändert werden.

Heiße Motoren sind ein Indiz dafür, das hohe Anteile an Rauschen(Noise)[^NOISE] Signale an die Motoren gesendet werden. Das sollte unter allen Umständen vermieden werden.

Mögliche Ursachen für heiße Motoren:
* alte Motoren
* Lagerschaden
* Filter sind schlecht eingestellt
* DTerm zu hoch
* PID-Abstimmung im allgemeinen schlecht

### DTerm-Problem
DTerm-Filterung ist die Empfehlung. Beginne mit der Reduzierung des DTerm-LPF-Filter in 20hz Schritten. Gehe **nicht** unter eine Cutoff-Frequenz von 80Hz.

Bei zwei DTerm-Filter setze den ersten auf eine minimale Grenzfrequenz von 100Hz, den zweiten Filter dann Stufenweise immer um 20Hz ab 100Hz erhöhen.

Immer wieder 20-30sec Fliegen und die Motor-Temperatur prüfen.

1. DTerm-LPF[^LPF2] Filter auf PT1 setzen
2. DTerm-LPF Filter auf BIQUAD setzen

Temperatur prüfen, prüfen prüfen

!!! danger "Beachten"
	**Ohne Sinn & Verstand am DTerm rumspielen oder an DTerm-LPF Änderungen durchführen, kann zur vollständigen Zerstörung der Motoren führen**

![Baustelle][imgInWork]


[imgInWork]: images/inwork.png "In-Arbeit"
[imgMOTOREN]: images/quadcopter_top.png "Motor-Drehrichtungen"
[imgProps]: images/prop_direction.jpg "Prop Drehrichtugn"


[^CWCCW]: CW = clockwise = rechts, CCW = counter clockwise = links
[^FC]: Flight-Controller
[^DNF]: siehe : bf_filter.md#notch-filter
[^LPF]: [Lowpass-Filter](bf_filter.md#lowpass-filter)
[^LPF2]: siehe : bf_filter.md#bf-static-lowpassfilter
[^NOISE]: [Noise/Vibrationen]()