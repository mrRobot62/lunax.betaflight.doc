# DMin - Handling
## Inhaltsverzeichnis
[TOC]

{{TOC}}

-------------------------------------------------------------
## Allgemeines
Mit DMin ist es nun möglich unterschiedliche Werte für D zu haben, je nachdem was der Copter gerade macht.

Im Normalflug erlaubt uns DMin mit reduzierten D-Werten zu fliegen und der DMax-Wert wird für schnelle Bewegungen genutzt (z.B. bei Fast-Roles, Flips, Propwash)

Außerdem bleiben die Motoren kühler

Wenn DMin im Konfigurator aktiviert wird, wird D in DMax umbenannt und es gibt eine neue Spalte DMin

DMin ist vom Profil abhängig (genau wie der D-Wert)

`d_min_boost_gain`steuert die Empfindlichkeit des Boost-Effekts (also wenn von DMin der DMax Wert verwendet werden soll, gibt der Booster an wie schnell das gehen soll.

Was bringen geringere D-Werte

**Vorteil**

> weniger Vibrationen, kühlere Motoren
> Besseres Verhalten der Motoren bei Vollgas
> D-Wert bezogenes Oszillieren wird verringert

!!! note "Nachteil"
	* mehr Propwash
	* Größeres Überschießen und Bounce-Backs
	* P Oszillation bei schnellen Mannövern
	* Langsame und Lowlevel Oszillationen bei smoothen Flügen

-------------------------------------------------------------
## Setup für den Erstflug
Default für DMin R23, P25, Y0

!!! note "Beachten"
	wenn DMin aktiviert ist, wird der reguläre DMax Wert nur dann genutzt, wenn schnelle Mannöver (z.B. Flips/Rolls) geflogen werden, in langsameren Flügen wird `DMIN` verwendet

-------------------------------------------------------------
## Prüfen des D Wertes im Flug

* Anzeige im OSD, `set debug_mode=D_MIN`und im OSD die Anzeige `debug2 on-screen`. Die Anzeige zeigt dir den 10fachen Wert. Beispiel: Anzeige 350 = 35D
* Über die Log-Aufzeichnung. Auch hier `set debug_mode=D_MIN`DEBUG2 im Blackboxexplorer zeigt dir unittelbar D für die ROLL-Achse, DEBUG3 für PITCH.

`DEBUG2`und `DEBUG3`zeigen den D-Wert vor `TPA`.

`DEBUG0` zeigt die Gyro-Anteil

!!! important "Hinweis"
	Bei Verwendung von `DMin`erhöht sich die CPU-Last ein wenig. Implementiert ist ein Biquad-Filter und ein PT1-Filter.

-------------------------------------------------------------
## Parameter

| Parameter  |BF|  Default | Bezeichnung  |
|---|---|---|---|
| `d_min `| |0/xx | 0 = disabled DMin, Werte > 0 entsprechend dem DMin-Wert. Wenn DMin deaktiviert ist, ist aktuell immer der DMax (D) der genutzte Wert für D | 
| `d_min_advanced`| | 20 | beschleunigt des boost-effekt, wenn sich Änderungen am `setpoint` ergeben (oder Gyro-Veränderungen). Wird unmittelbar bei Veränderungen durchgeführt bevor der Copter überhaupt die Bewegung ausführt. Der Wert kann zum Overshot beitragen, 0 deaktiviert diese Funktion und sollte bei Racern verwendet werden (und bei den meisten Coptern).| 
| `d_min_boost_gain `| |  | Verstärkungsfaktor, wie schnell bei schnellen Bewegungen D angepasst werden soll. 30-35 ist für normale Copter gut geeignet, 40-45 für wirklich sauber gebaute Freestyler. Wenn Propwash das Hauptproblem sind und die Motoren kühl sind, dann DMax erhöhen und den gain-Faktor ebenfalls (moderat!)| 

Fine-Tuning des DMin Wertes geht nur über eine Blackboxauswertung. Für diese Anlayse sollte der Debug-Mode `D-MIN`ausgewählt werden. Nur dann sieht man die aktuellen `D_Min`Werte im Flug


