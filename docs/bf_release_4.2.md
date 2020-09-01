# BF 4.2 - Neuerungen

## Historie
| Version  |  Datum |  Inhalt |
|:-:|---|---|
| 0.1  |  August 2020 | initial  |

## Allgemeine Anpassungen
* BF-Configurator 10.7.0 ist notwendig
* Blackbox-Explorer 3.5.0 für BF 4.2
* Neue Tuning-Möglichkeiten (Slider)
* **Bei einigen Targets downgeloaded werden müssen, daher ist 10.7.0 wichtig.**
* **Keine Konfigurationen aus älteren Releasen importieren** Einige Default-Werte haben sich geändert
* Es wird kein Motorprotokoll automatisch ausgewählt. Dies muss nach einem Update manuell durchgeführt werden.
* Wenn bidirektionales DShot ausgewählt wird, die Geschwindigkeit des Protokolls wird halbiert um die Bidrirektionalität aufrecht zu erhalten. Dies gilt **nicht für DShot600**. Bei `DShot300` muss die PIDLoop auf 4k gesetzt werden. Bei `DShot150`auf 2k. Demnach bei `DShot600` auf 8k
* Bevor gearmt werden kann muss der Accelerometer calibriert werden.
* Die Strommessung ist nun am Throttle-Signal gekoppelt, das ermöglicht besser Vorhersagen zum Stromverbrauch
* 


## Major
* Überarbeitung der Gyro-Loop. Diese läuft nun in der Geschwindigkeit der nativen Beschwindigkeit des Gyros
* zwei neue Rates auswählbar `ACTUAL`und `QUICK` 
* VBat Kompensation, wenn die Batteriespannung nachläßt
* Neuer NFE Race Modus

## Minor
* OSD Logo nun auch beim Armen sichtbar
* Unterstützung für verbesserte OSD-/CMS-Geräte hinzugefügt, wodurch es möglich wurde, das Highligting von Text oder Symbolen zu unterstützen 
* Unterstützung für FrSkyOSD OSD-Geräte hinzugefügt
* Unterstützung für das Redpine RC-Protokoll auf Geräten mit einem über SPI angeschlossenen CC2500-Chip (FrSky SPI) hinzugefügt
