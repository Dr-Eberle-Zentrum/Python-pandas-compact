---
title: "Datenimport aus Dateien"
teaching: 20
exercises: 10
---



:::::::::::::::::::::::::::::::::::::: questions

- Wie importiere ich Daten?
- Auf was muss ich achten?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Datenimport via `pandas`
- Kodierung von Zahlen und Texten
- Pfadangaben

::::::::::::::::::::::::::::::::::::::::::::::::

Mit Python können Daten aus verschiedenen Quellen und von verschiedenen Orten eingelesen werden.

Die gängigsten Quellen sind:

- Textdateien (z.B. `.csv`, `.tsv`, `.txt`)
- Excel-Dateien (z.B. `.xlsx`, `.xls`)
- Datenbanken (z.B. MySQL, PostgreSQL, SQLite)

Die gängigsten Orte sind:

- lokale Dateien
- URLs, also direkter Datenimport aus dem Internet oder lokalem Netzwerk

Im Folgenden einige Beispiele zum Einlesen von Daten aus Dateien in Python.
Die lokalen Dateien müssen sich im *Arbeitsverzeichnis* befinden, also in dem gleichen Verzeichnis in dem das Python-Skript ausgeführt wird, oder über den relativen bzw. absoluten Dateipfad adressiert werden (siehe weiter unten).
Um den folgenden Beispielcode mit lokalen Dateien auszuführen, müssen daher die Dateien [storms-2019-2021.csv](data/storms-2019-2021.csv) und [storms-2019-2021.xlsx](data/storms-2019-2021.xlsx) erst heruntergeladen und ins aktuelle Arbeitsverzeichnis kopiert werden.



``` python
# zunächst importieren wir das Paket pandas und benennen es um in "pd", das ist gängige Praxis und findet sich in fast allen Beispielen so wieder
import pandas as pd

# CSV-Datei aus dem Internet laden und in die Variable web_data speichern
web_data = pd.read_csv("https://raw.githubusercontent.com/tidyverse/dplyr/master/data-raw/storms.csv")

# Geben wir die Daten aus
print(web_data)

# Lokale Excel-Datei laden mit expliziter Angabe des Tabellenblatts
excel_data = pd.read_excel("storms-2019-2021.xlsx", sheet_name="storms-2020")
print(excel_data)

# Lokale CSV-Datei laden. Achtung: die oben aus dem Internet geladene Datei verwendet gemäß CSV-Standard ein Komma als Trennzeichen, die lokale Datei jedoch ein Semikolon. Die Zahlen in dieser CSV-Datei sind auch mit westeuropäischem Zahlenformat ("," als Dezimaltrennzeichen und "." als Tausendertrennzeichen) gespeichert. Daher müssen hier beim Laden die Parameter sep, decimal und thousands angepasst werden. Auch das Encoding der Datei kann angegeben werden, vor allem wenn es nicht UTF-8 ist.
csv_data = pd.read_csv("storms-2019-2021.csv", sep=";", decimal=",", thousands=".", encoding="UTF-8")
print(csv_data)
```

Beachten sie im letzten Beispiel das explizite Setzen des Textencodings, um sicherzustellen, dass Umlaute korrekt eingelesen werden (hier im Beispiel wird `latin1` verwendet, was für westeuropäische Länder wie Deutschland oder Frankreich üblich ist bzw. war).
Dies ist vor allem beim *Import von alten Daten* wichtig, die in einem anderen Zeichensatz kodiert sein könnten.
Heutzutage wird meist `UTF-8` verwendet, was ein internationaler Standard ist und alle Zeichen korrekt darstellt.
Dies ist auch das Standardencoding für die meisten Funktionen wie z.B. `read_csv`.

Im dem Beispiel zu `read_csv` werden auch das Dezimaltrennzeichen und das Tausendertrennzeichen explizit angegeben, da z.B. westeuropäische Länder wie Deutschland oder Frankreich diese Trennzeichen vertauscht haben im Vergleich zu englischsprachigen Ländern (welche die Standardspezifikationen für CSV-Dateien sind).

Es gibt noch viele weitere nützliche optionale Parameter der Funktionen `read_csv()` die in der [Dokumentation](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html) dieser Funktion beschrieben sind, wie beispielweise die einzulesenden Zeilen limitieren, Startzeile festlegen, usw.

Das Speichern der Daten in einer Datei erfolgt analog, indem einfach `to_` anstelle von `read_` verwendet wird, also z.B. `to_csv()`. Bei `to_csv()` können analog die Spaltentrennzeichen usw. angegeben werden. Da in einem DataFrame jede Zeile einen Index hat, wird dieser standardmäßig auch in die Datei geschrieben. In der Regel möchten wir das nicht, so können wir den Parameter `index=False` setzen, z.B. mit `csv_data.to_csv("output.csv", index=False)`.

Die Funktionen für das Einlesen und Speichern von Daten mit dem `pandas` Paket sind in der [Pandas-Dokumentation zu IO-Tools](https://pandas.pydata.org/docs/user_guide/io.html) zusammengefasst.

::::::::::::::::::::::::::::::::::::: challenge

## Frage zu CSV Import

*Was sind bei `read_csv()` die Standardwerte für `sep` (Spaltentrennzeichen), `decimal` (Dezimaltrennzeichen), `thousands` (Tausendertrennzeichen) und `encoding` (Zeichenkodierung), wenn sie nicht explizit beim Aufruf der Funktion angegeben werden?*

:::::::::::::::::::::::: solution

### Antwort

Die Standardwerte sind: Komma für Spaltentrennzeichen, Punkt für Dezimaltrennzeichen, kein Tausendertrennzeichen, und UTF-8 für die Zeichenkodierung. Die Parameter müssen nur angegeben werden, wenn die CSV-Datei von diesen Standardwerten abweicht.

:::::::::::::::::::::::::::::::::


## Aufgabe: Datenimport

Führen sie die obigen Beispiele aus und passen sie sie an, um die Dateien `storms-2019-2021.csv` und `storms-2019-2021.xlsx` einzulesen. Hierzu müssen diese Dateien ggf. zuvor heruntergeladen werden (oder sie verwenden direkt die URL zu den online verfügbaren Dateien).

*Wieviele Zeilen haben die jeweiligen Datensätze? Und wie erklärt sich der Unterschied?*

:::::::::::::::::::::::: solution

## Antwort

Die Datensätze haben unterschiedliche Größen:

- `storms-2019-2021.csv` hat 19 Zeilen, es umfasst die Jahre 2019-2021.
- `storms-2019-2021.xlsx` hat 10 Zeilen, es umfasst nur das Jahr 2020 da nur ein einzelnes Datenblatt importiert wurde.

:::::::::::::::::::::::::::::::::
::::::::::::::::::::::::::::::::::::::::::::::::

## Pfade und Arbeitsverzeichnis

Dateien können auch *relativ* zum Arbeitsverzeichnis geladen werden. Das Arbeitsverzeichnis ist der Ordner, in dem das aktuelle Python-Skript ausgeführt wird. Dies erlaubt das Laden von Dateien ohne Angabe des vollständigen Pfades, auch wenn sich die Dateien nicht im Arbeitsverzeichnis befinden. Der große Vorteil ist, dass der Code portabler wird, da er nicht mehr von absoluten Pfaden (also wo die Daten auf ihrem Computer genau liegen) abhängt.

Zum Beispiel, wenn sich die Datei `storms.csv` im Unterordner `data` des Arbeitsverzeichnisses befindet, kann sie mit `read_csv("data/storms.csv")` geladen werden.

Wenn nötig, können auch vollständige Pfade verwendet werden, um Dateien von beliebigen Orten auf dem Computer zu laden. Das nennt man dann einen *absoluten Pfad*. Ein Beispiel auf einem Windows-System wäre `read_csv("C:/Users/Username/Documents/data.csv")`.

Wenn sie die letzte Pfadangabe mit der Standard-Windows-Schreibweise vergleichen, werden sie feststellen, dass diese normalerweise mit Backslashes `\` arbeitet, also hier `"C:\Users\Username\Documents\data.csv"`.
Das Problem ist, dass der Backslash in Python als Escape-Zeichen verwendet wird, um spezielle Zeichen (wie z.B. Zeilenumbruch oder Tabulator) zu kodieren. Daher wird der Backslash in Pfadangaben in R oft verdoppelt, um dies zu umgehen. Also würde der Pfad in R so aussehen: `"C:\\Users\\Username\\Documents\\data.csv"`.
Alternativ können sie auch in Windows (wie im obigen Beispiel) die "Linux-Schreibweise" verwenden, die mit Schrägstrichen `/` arbeitet, die in R ohne Probleme verwendet werden können.


::::::::::::::::::::::::::::::::::::: keypoints

- Dateinamen sind Textinformation und müssen in Anführungszeichen gesetzt werden.
- Pfade können absolut oder relativ zum Arbeitsverzeichnis angegeben werden. Letzteres ist portabler und empfohlen.
- Unter Microsoft Windows können Pfade in Pyhton auch mit Schrägstrichen `/` statt Backslashes `\` geschrieben werden.
- Achten sie auf die Angabe der korrekten Kodierung von Textdateien, um Umlaute und Sonderzeichen korrekt einzulesen.
- Denken sie daran, dass deutsche CSV-Dateien oft Semikolon (`;`) als Spaltentrennzeichen und Komma `,` als Dezimaltrenner verwenden. Auch Excel verwendet für den CSV-Export das Semikolon als Spaltentrennzeichen.
- Excel-Dateien können mehrere Blätter (*sheets*) enthalten, die einzeln importiert werden müssen.
- Zusammenfassung der Lese- und Schreiboperationen in der [Pandas-Dokumentation zu IO-Tools](https://pandas.pydata.org/docs/user_guide/io.html).

::::::::::::::::::::::::::::::::::::::::::::::::