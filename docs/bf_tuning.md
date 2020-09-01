# BF Allgemeine Tuning Tips 
## Inhaltsverzeichnis
[TOC]

{{TOC}}

## Allgemeines
Um den Copter zu tunen sollte nachfolgende Reihenfolge versucht werden einzuhalten

1. **_Sauber bauen_**. Vermeide schlackernde Kabel. Der FC sollte vibrationsgedämpft verbaut sein. Prüfe ob den Gyro etwas berührt (**strikt vermeiden**). Sind alle Schrauben fest

2. **Versuche auf die aktuelleste BF-Version aufzusetzen.** Mache vor Deinen Änderungen ein Backup der aktuellen FW. Sichere Deine Konfiguration mit `diff all`. Neue BF-Version versprechen Bugfixings und häufig verbesserte Filter/Tunig-Möglichkeiten. Leider ist der Update immer mit Arbeit verbunden.

3. **Sender kalibrieren**. Im Receiver-Tab sollten für alle drei Achsen die Einstellungen zwischenn **1000** und **2000** liegen. Hintergrund ist, liegen die aktuellen Werte unter-/oberhalb wird das RC-Signal beschnitten oder gespreizt, beides sorgt dafür, dass die Signalverarbeitung nicht optimal ist.

4. **Prüfe im BF-Configurator die Lage des Copters.** Der grüne Pfeil symbolisiert **Vorne**. Neige den Copter nach unten (PITCH-Forward), der der simulierte Copter **muss** sich ebenfalls nach unten neigen. Wiederholen für alle Achsen. Der simulierte Copter **muss exakt** das gleiche tun als der echte Copter.
> Das ist eine **sehr, sehr wichtige** Funktionsprüfung, ansonsten wird der Copter beim Erststart vermutlich direkt einen Salto schlagen

5. 



![Baustelle][imgInWork]


[imgInWork]: images/inwork.png "In-Arbeit"