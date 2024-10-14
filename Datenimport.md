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

## Datenquellen einlesen

Mit pandas können Daten aus verschiedenen Quellen und von verschiedenen Orten eingelesen werden. Die gängigsten Quellen sind:

- Textdateien (z.B. `.csv`, `.tsv`, `.txt`)
- Excel-Dateien (z.B. `.xlsx`, `.xls`)
- Datenbanken (z.B. MySQL, PostgreSQL, SQLite)

Die gängigsten Orte sind:

- lokale Dateien
- URLs, also direkter Datenimport aus dem Internet oder lokalem Netzwerk

Bevor wir loslegen, müssen wir das pandas Paket importieren. Dies wird in der Regel mit dem Befehl `import pandas as pd` gemacht. Das `as pd` ist eine gängige Abkürzung, um das Paket in der weiteren Verwendung kürzer zu schreiben:


``` python
import pandas as pd
```

Im Folgenden einige Beispiele zum Einlesen von Daten aus Dateien mit pandas in Python. Im Fall von CSV- oder TSV-Dateien wird die Funktion `read_csv()` verwendet, für Excel-Dateien `read_excel()`. Die Funktionen haben viele optionale Parameter, die in der [Dokumentation](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html) beschrieben sind. 

Hier ein Beispiel, in dem eine CSV-Datei aus dem Internet geladen und der entsprechende von pandas erzeugte Dataframe ausgegeben wird:


``` python
# CSV-Datei aus dem Internet laden und in die Variable web_data speichern
web_data = pd.read_csv("https://raw.githubusercontent.com/tidyverse/dplyr/master/data-raw/storms.csv")

# Geben wir den Dataframe aus
print(web_data)
```

Werden stattdessen Dateien vom lokalen Computer geladen, so müssen sich diese im *Arbeitsverzeichnis* befinden, wenn sie nur mit dem Dateinamen adressiert werden. Das Arbeitsverzeichnis ist jenes Verzeichnis, in dem das Python-Skript ausgeführt wird. Wenn die Dateien nicht im Arbeitsverzeichnis liegen, müssen sie über den relativen bzw. absoluten Dateipfad adressiert werden (siehe weiter unten). 

Um den folgenden Beispielcodes mit lokalen Dateien auszuführen, müssen daher die Dateien [storms-2019-2021.csv](data/storms-2019-2021.csv) und [storms-2019-2021.xlsx](data/storms-2019-2021.xlsx) erst heruntergeladen und ins aktuelle Arbeitsverzeichnis kopiert werden.

Laden eines Tabellenblattes aus einer Excel-Datei:


``` python
# Lokale Excel-Datei laden mit expliziter Angabe des Tabellenblatts
excel_data = pd.read_excel("storms-2019-2021.xlsx", sheet_name="storms-2020")
print(excel_data)
```

Hier der Code zum Laden einer CSV-Datei vom lokalen Computer, die sich im aktuellen Arbeitsverzeichnis befindet. Hier werden einige der optionalen Parameter der Funktion `read_csv()` verwendet. Der Grund: die oben aus dem Internet geladene Datei verwendet gemäß CSV-Standard das Komma als Spaltentrennzeichen, die lokale Datei verwendet hierfür jedoch das Semikolon (Strichpunkt). Die Zahlen in der lokalen CSV-Datei sind auch mit westeuropäischem Zahlenformat (`,` als Dezimaltrennzeichen und `.` als Tausendertrennzeichen) gespeichert. Daher müssen hier beim Laden die Parameter `sep`, `decimal` und `thousands` angepasst werden. Auch die Zeichenkodierung (`encoding`) der Datei kann angegeben werden, vor allem wenn sie nicht UTF-8 ist.


``` python
# Lokale CSV-Datei laden. 
csv_data = pd.read_csv("storms-2019-2021.csv", sep=";", decimal=",", thousands=".", encoding="latin1")
print(csv_data)
```

Die angegebene Zeichenkodierung beim Aufruf von `read_csv()` muss der Zeichencodierung der Datei entsprechen oder damit kompatibel sein, um sicherzustellen, dass beispielsweise Umlaute und Sonderzeichen korrekt eingelesen werden (hier im Beispiel wird `latin1` verwendet, was für westeuropäische Länder wie Deutschland oder Frankreich üblich ist bzw. war). Dies ist vor allem beim *Import von alten Daten* wichtig zu beachten, die in einem anderen Zeichensatz kodiert sein könnten. Heutzutage wird meist `UTF-8` verwendet, was ein internationaler Standard ist und alle bekannten Zeichen korrekt darstellen kann. `UTF-8` ist auch die Standardenkodierung für die meisten Funktionen wie z.B. `read_csv()`, wenn kein `encoding` beim Aufruf mitgegeben wird. 

Es gibt noch viele weitere nützliche optionale Parameter der Funktionen `read_csv()`, die in der [Dokumentation dieser Funktion](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html) beschrieben sind, beispielweise um festzulegen, ab welcher Zeile das Einlesen der CSV-Datei starten soll und wie viele Zielen eingelesen werden sollen:


``` python
# Die ersten 10 Zeilen einer CSV-Datei einlesen
csv_data = pd.read_csv("irgendein.csv", nrows=10)

# Die CSV-Datei ab der 11. Zeile einlesen
csv_data = pd.read_csv("irgendein.csv", skiprows=10)

# Man kann auch angeben, in welcher Zeile (0-basiert) die Spaltenüberschriften stehen, das Einlesen beginnt dann ab da
csv_data = pd.read_csv("irgendein.csv", header=4)

# Diese Parameter kann man auch kombinierne, z.B. die ersten 10 Zeilen der überspringen und die nächsten 10 Zeilen einlesen
csv_data = pd.read_csv("irgendein.csv", skiprows=10, nrows=10)
```

Das Speichern der Daten in einer Datei erfolgt analog, indem einfach `to_` anstelle von `read_` verwendet wird, also z.B. `to_csv()`. Bei `to_csv()` können analog die Spaltentrennzeichen usw. angegeben werden. Da in einem Dataframe jede Zeile einen Index hat, wird dieser standardmäßig auch in die Datei geschrieben, was üblicherweise eine zusätzliche Spalte an erster Stelle der CSV-Datei erzeugt, die einfach die Zeilen der Datei durchnummeriert bei 0 beginnend. In der Regel möchten wir das nicht, so können wir den Parameter `index=False` setzen, z.B. mit `csv_data.to_csv("output.csv", index=False)`.

Alle Funktionen für das Einlesen und Speichern von Daten mit dem Paket `pandas` sind in der [Pandas-Dokumentation zu IO-Tools](https://pandas.pydata.org/docs/user_guide/io.html) zusammengefasst.

::::::::::::::::::::::::::::::::::::: challenge

## Frage zu CSV Import

*Was sind bei `read_csv()` die Standardwerte für `sep` (Spaltentrennzeichen), `decimal` (Dezimaltrennzeichen), `thousands` (Tausendertrennzeichen) und `encoding` (Zeichenkodierung), wenn sie nicht explizit beim Aufruf der Funktion angegeben werden?*

:::::::::::::::::::::::: solution

### Antwort

Die Standardwerte sind: Komma für Spaltentrennzeichen, Punkt für Dezimaltrennzeichen, kein Tausendertrennzeichen, und UTF-8 für die Zeichenkodierung. Ein Parameter muss nur angegeben werden, wenn dessen Ausprägung in der CSV-Datei vom jeweiligen Standardwert abweicht.

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

## Einfache Spaltenmanipulation

Nachdem man eine CSV-Datei in einen Dataframe eingelesen hat, kommt man zunächst oft in die Situation, dass einige Spalten umbenannt, gelöscht, oder anders angeordnet werden sollen für die weitere Verarbeitung. Hier sind einige Beispiele, wie dies in pandas gemacht werden kann:

**Entfernen von Spalten**: Es gibt mehrere Möglichkeiten, eine Spalte aus einem Dataframe zu entfernen. `drop()` entfernt eine oder mehrere Spalten. Möchte man allerdings die entfernte Spalte weiterverwenden, so empfiehlt sich die Verwendung der Funktion `pop()`. Diese entfernt die Spalte und gibt sie zurück, so dass sie in einer neuen Variable gespeichert werden kann. Hier Beispielcode für beide Methoden:


``` python
# Entfernen der Spalte "diameter" aus dem Dataframe "df" mit drop()
df.drop(columns=["diameter"], inplace=True)

# Entfernen der Spalten "status" und "wind" aus dem Dataframe "df" mit drop()
df.drop(columns=["status", "wind"], inplace=True)

# Entfernen der Spalte "diameter" aus dem Dataframe "df" mit pop()
diameter = df.pop("diameter")  # diameter enthält jetzt die Werte der entfernten Spalte "diameter"
```

Achtung: Der Parameter `inplace=True` bedeutet, dass die Änderung direkt im Dataframe `df` gemacht wird. Wird dieser Parameter nicht so angegeben (standardmäßig ist er `False`), wird eine Kopie des Dataframes mit der gewünschten Änderung zurückgegeben, während der Dataframe `df` unverändert bleibt. Dies gilt für fast alle Dataframe-Manipulationen in pandas! Alternativ zur Angabe dieses Parameters kann das Ergebnis des Aufrufs der Funktion auch in `df` oder einer neuen Variable gespeichert werden, z.B. `df = df.drop(columns=["wind"])`

**Umbenennen von Spalten**: Im folgenden Beispiel wird im Dataframe `df` die Spalte `wind` in `windspeed` und die Spalte `tropicalstorm_force_diameter` in `diameter` umbenannt.


``` python
df.rename(columns={
    "wind": "windspeed", 
    "tropicalstorm_force_diameter": "diameter"
}, inplace=True)
```

**Veränderung der Spaltenreihenfolge**: Im folgenden Beispiel wird die Reihenfolge der Spalten im Dataframe `df` geändert, indem die Spalte `diameter` an die dritte Stelle (0-basierter Index ist also 2) verschoben wird. Dies wird erreicht, indem die Spalte mit `pop()` zunächst entfernt und mit `insert()` an der gewünschten Stelle wieder eingefügt wird. Auch hier wird die Änderung direkt im Dataframe gemacht, da `inplace=True` gesetzt ist. Beachten Sie, dass diese beiden Funktionen den Dataframe `df` direkt verändern und keinen neuen Dataframe zurückgeben. Falls Sie diesbezüglich im Unklaren sind beim Aufruf einer Funktion, sehen Sie in der Dokumentation der entsprechenden Funktion nach oder probieren Sie es einfach aus!


``` python
# Verschieben der Spalte "diameter" an die dritte Stelle (Spaltenindex 2)
df.insert(2, df.pop("diameter"))
```

**Einfügen neuer Spalten**: Neue Spalten können einfach hinzugefügt werden, indem sie mit einem Wert oder einer Liste von Werten (üblicherweise auf Grundlage der Werte einer anderen Spalte) initialisiert werden: 


``` python
# Neue Spalte "category" mit dem Wert "storm" für alle Zeilen hinzufügen
df["category"] = "storm"

# Neue Spalte "wind_kmh" mit den Werten der Spalte "wind" umgerechnet von Knoten in km/h hinzufügen
df["wind_kmh"] = df["wind"] * 1.852
```

Weiterführende Manipulationsmöglichkeiten für Dataframes behandeln wir im folgenden Kapitel zur Datenverarbeitung.

::::::::::::::::::::::::::::::::::::: keypoints

- Dateinamen sind Textinformation und müssen in Anführungszeichen gesetzt werden.
- Pfade können absolut oder relativ zum Arbeitsverzeichnis angegeben werden. Letzteres ist portabler und empfohlen.
- Unter Microsoft Windows können Pfade in Pyhton auch mit Schrägstrichen `/` statt Backslashes `\` geschrieben werden.
- Achten sie auf die Angabe der korrekten Kodierung von Textdateien, um Umlaute und Sonderzeichen korrekt einzulesen.
- Denken sie daran, dass deutsche CSV-Dateien oft Semikolon (`;`) als Spaltentrennzeichen und Komma `,` als Dezimaltrenner verwenden. Auch Excel verwendet für den CSV-Export das Semikolon als Spaltentrennzeichen.
- Excel-Dateien können mehrere Blätter (*sheets*) enthalten, die einzeln importiert werden müssen.
- Zusammenfassung der Lese- und Schreiboperationen in der [Pandas-Dokumentation zu IO-Tools](https://pandas.pydata.org/docs/user_guide/io.html).

::::::::::::::::::::::::::::::::::::::::::::::::
