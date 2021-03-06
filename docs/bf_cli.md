# Betaflight - CLI

![LunaX](./images/lunax_logo.png)

## Inhaltsverzeichnis
- [Betaflight - CLI](#betaflight---cli)
  - [Inhaltsverzeichnis](#inhaltsverzeichnis)
  - [Allgemeines](#allgemeines)
  - [Liste der meist gebrauchten CLI-Befehle](#liste-der-meist-gebrauchten-cli-befehle)

## Allgemeines
CLI steht für `Command line interface`und kann für das manuelle setzen und löschen von Parametern genutzt werden.

Eine Konfigurationen können ausschließlich über das CLI durchgeführt werden (z.B. FC-Port rekonfigurationen, Dumps erstellen, ...)

Groß/Kleinschreibung wird nicht beachtet (bzw. ignoriert)

**TIP**
> Bevor Änderungen vorgenommen werden, **erstelle immer ein Backup** des aktuellen Setups `diff all`und dann über den Button "Save" als Datei speichern

## Liste der meist gebrauchten CLI-Befehle

**Aufbau der Befehle**

`[command] [conf-param] = [value]`

**Beispiel:**

`set roll_expo = 0`


| Befehle | Beispiel | Info |
|:---:|---|---|
|version|`version`|Zeigt die aktuelle BF-Version auf dem FC|
|status|`status`|Zeigt den aktuellen Status des FC|
| save | `save` | speichert die aktuellen Änderungen im Speicher des FC und führt einen Reboot durch. **WICHTIG:** sämtliche Änderungen müssen explizit mit `save`gespeichert werden, ansonsten gehen alle Änderungen verloren (Häufig gemachter Fehler) |
|diff all|`diff all`|Auflistung aller Konfiguraitonsparameter, welche vom Default-BF Setup abweichen.|
|dump |`dump`|Ausgabe sämtlicher Konfigurationsparameter.|
|set |`set tpa_rate = 10`| setzt einen Konfigurationsparameter auf einen Wert |
|get |`get rc_smooting_type=`| Liest den angegebenen Konfigurationsparameter. **TIP**: wenn nur ein Fragment des Konfigurationsparameters angegeben wird, zeigt BF eine Liste an Parametern an, die mit dem angegebenen Parameter beginnen.|
|resource|||
|tasks|`tasks`|Zeigt eine Übersicht der laufenden Prozesse im FC. Hier kannst du abschätzen warum Deine CPU x% ausgelastet ist.|
||||
||||



![Baustelle][imgInWork]



[imgInWork]: images/inwork.png "In-Arbeit"

