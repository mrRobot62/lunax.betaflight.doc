# **Betaflight / Deutsch - als Ascii-Markdown Dateien**

## Historie
| Version  |  Datum |  Inhalt |
|:-:|---|---|
| 0.1  |  August 2020 | initial  |
| 0.2 | September 2020 | Material Theme, vereinzelte Updates, Mermaid2 Plugin |
| 0.3 | September 2020 | Mermaid Extension für Chrome, zusätzliche Installations-Informationen rund um MKDocs |
## Info
Dies hier ist eine Sammlung an Dokumenten, die im Laufe meiner kurzen FPV-Fliegerei entstanden sind und auch weiter entstehen bzw. weiter fortgeschrieben werden.

Hintergrund für diese Ansammlung der Dokumente ist, dass ich mehr Verständnis für mich selbst schaffen möchte und dadurch ein besseres Verständnis über die Zusammenhänge und dem Flugverhalten von FPV-Coptern zu erhalten.

Weiterhin möchte ich kein endloses Wiki in deutsch erstellen sondern einzelne Themenbereiche herauslösen - auch wenn es dadurch Redundanzen gibt. Für mich hat es den Vorteil, dass ich bestimmte Dokumente ausdrucken kann und mit zum Fliegen nehmen kann, wenn ich einen Copter optimieren möchte.

Tja und zum Schluß - ich kann mir einfach nicht alles im Kopf behalten, daher lese ich gerne nach und dann helfen mir kleinere Dokumente mehr als große.

Die einzelnen Seiten stehen auch zum Download als PDF zur Verfügung.

Leider habe ich keine Extension für Markdown gefunden, mit der man einfach ein Wort-Index aufbauen kann aber es gibt eine gute Alternative über [MKDocs]() und dem Theme welches ich nutzte.

Hier kann man über die Suchfunktion Wörter im Gesamten repository suchen -** sehr sehr hilfreich** -

**TIP**

	Installiert Euch lokal MKDocs und cloned dieses Repository und 
	startet dann die Dokumente über `mkdocs serve`. 
	
	Aufruf über Euren Browser über `http:127.0.0.1:8000`

Sieht dann wie folgt aus:
![](docs/images/mkdocs_overview1.png)

Vielleicht hilft es dem ein oder Anderen

Du kannst alle Seiten als PDF downloaden über .....

Viel Spass und propwash freies Fliegen

LunaX - August 2020

P.S. Einige Kapitel sind noch in der Entstehung :-O und enthalten noch keinen Text

## Handhabung dieses Github-Repositories
Dies Repository basiert auf [MKDocs](https://mkdocs.org). Alle geschriebenen Seiten wurden als einfache Markdown-Dateien erstellt.


```
	mkdocs.yml (Konfigurationsdatei für MKDocs)
	docs/
		xxxx.md Dateien (einzelne Markdown-Dateien zu den Themen)
		css/ CSS Dateien
		js/ Javascript Dateien
		images/ Bilder die verwendet wurden
		
	pdf-export/
		xxxxx.pdf Markdown-Datei als PDF konvertiert.
	sites/
		pdf/combined.pdf (hier liegt die Gesamt-PDF-Datei)
		<folder> (diverse Subfolder, enthalten HTML-Seiten und PDF (Einzeldateien) zum Offline-Lesen)

```

# Online-Lesen in GitHub
Wer online lesen möchte sollte sich für Chrome folgende Extension [Mermaid](https://github.com/Redisrupt/mermaid-diagrams) installieren, dann werden auch Diagramme generiert und nicht nur der Mermaid-Code wird angezeigt.

**Beachten**

	**Fehler:** Beim Erstladen einer Markdown-Datei die Mermaid Code enthält kommt es häufig vor, dass nicht sofort das gerenderte Bild angezeigt wird - man sieht den Code 
	
	**Lösung:** Refresh der Seite, dann klappt es

In einigen Dokumenten werden solche Diagramme genutzt (z.B. Filter)

Wenn alles sauber funktioniert solltet ihr hier ein Diagramm sehen

```mermaid
graph LR
	GY(Gyro) --> FLT[Filter]
	RC(RC-Command) -->FLT
	FLT --> PID(PID-Controller)
	PID --> MIX(Mixer)
	MIX --> MOT((Motoren))
	MOT --> RPM[RPM-Filter BF 4.x]
	RPM --> FLT

```

# Offline-Lesen
## via MKDocs
Du möchtest Dir gerne alles lokal auf Deine Rechner installieren, somit hast du immer Zugriff auf alle Daten selbst wenn Du den Rechner mit zum Fliegen nimmst und "mal eben" was nachschauen möchtest?

**Tip 1**

	Mach Dir die Mühe und installiere [MKDocs](), dann hast du eine perfekte Übersicht und es funktioniert einwandfrei.
	
	MKDocs funktioniert auf MacOS, Linux und Windows (leider nicht auf iOS)
	

**Tip 2**

	Getestet habe ich unter Python 3.6, sollte aber auch unter 3.7 funktionieren.
	
	Ich empfehle Dir die Installation in einer virtuellen Python-Umgebung (z.B. virtualenv oder über Anaconda.

### 26.11.2020 Nachtrag
Leider habe ich festgestellt, dass ich vergessen habe zu erwähnen. Zusätzlich zu MKDOCS muss eine zusätzliche Bibliothek installiert werden
`markdown_katex`.
Erhaltet ihr nachdem ihr `mkdocs serve`startet folgenden Fehler:
```
INFO    -  Building documentation... 
ERROR   -  Config value: 'markdown_extensions'. Error: Failed loading extension "markdown_katex". 
```
dann fehlt genau diese Bibliothek.

*Installation:*

```
$ >pip install markdown-katex
...
Requirement already satisfied: six in /Users/bernhardklein/opt/anaconda3/lib/python3.8/site-packages (from pathlib2->markdown-katex) (1.15.0)
Installing collected packages: markdown-katex
Successfully installed markdown-katex-202009.1026
(base) 

$ >mkdocs serve

INFO    -  Building documentation... 
INFO    -  Cleaning site directory 
INFO    -  Documentation built in 0.83 seconds 
[I 201127 16:25:13 server:335] Serving on http://127.0.0.1:8000
INFO    -  Serving on http://127.0.0.1:8000
[I 201127 16:25:13 handlers:62] Start watching changes
INFO    -  Start watching changes
[I 201127 16:25:13 handlers:64] Start detecting changes
INFO    -  Start detecting changes
```

## via static HTML-Sites
Clone dieses Repository und kopiere es irgendwo auf Deinen Rechner.

Im Verzeichnis `site` und in den Unterverzeichnissen sind alle `HTML`und `PDF`Seiten vorhanden.

Die Navigation ist allerdings nicht so möglich wie unter MKDocs.




