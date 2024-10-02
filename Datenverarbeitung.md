---
title: "Datenverarbeitung"
teaching: 30
exercises: 10
---




:::::::::::::::::::::::::::::::::::::: questions

TODO
- Wie baue ich komplexe Workflows mit pipes?
- Wie transformiere ich Tabellen?
- Wie bearbeite ich Text?
- Wie führe ich mehrere Datensätze zusammen?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

TODO
- Überblick über die Möglichkeiten der Datenverarbeitung mit `tidyverse` Paketen

::::::::::::::::::::::::::::::::::::::::::::::::



## Workflows mittels Piping

In vielen Datenverarbeitungssprachen ist der Pipe-Operator ein nützliches Werkzeug, um Arbeitsschritte zu verketten, sodass das Abspeichern von Zwischenergebnisse in Variablen vermieden werden kann.

Seit R 4.1.0 ist der Pipe-Operator `|>` standardmäßig in R enthalten.
Alternativ bietet das `magrittr` package den Pipe-Operator `%>%`, welcher eine etwas umfangreichere Funktionalität bietet.
Wer sich für die Unterschiede interessiert, kann diese im  [Blogbetrag dazu](https://www.tidyverse.org/blog/2023/04/base-vs-magrittr-pipe/) nachlesen.
Für die meisten Anwendungen ist der Standard-Pipe-Operator `|>` ausreichend und äquivalent zu `%>%` verwendbar.

Der Pipe-Operator ist ein nützliches Werkzeug, um den Code lesbarer zu machen und die Arbeitsschritte in einer logischen Reihenfolge zu verketten.
Hierbei wird das Ergebnis des vorherigen Arbeitsschrittes als erstes Argument des nächsten Arbeitsschrittes verwendet.
Da das erste Argument von (den meisten) Funktionen der Datensatz ist, ermöglicht dies die Verknüpfung von Arbeitsschritten zu einem Workflow ohne Zwischenergebnisse speichern zu müssen oder Funktionsaufrufe zu verschachteln.

Zum Beispiel:


``` r
# einfaches Beispiel zur Verarbeitung eines Vektors mit base R Funktionen

# verschachtelte Funktionsaufrufe
# entspricht der Leserichtung von rechts nach links
# und einer Ergebnisbeschreibung ala "Wurzel der Summe ohne NA von 1, 2, 3, NA"
sqrt( sum( c(1, 2, 3, NA), na.rm=TRUE ) )

# mit Pipe-Operator
# entspricht der Leserichtung von links nach rechts
# und einer Beschreibung der Arbeitsschritte in ihrer Reihenfolge
# ala "nehme 1, 2, 3, NA, summiere ohne NA und davon die Wurzel"
c(1, 2, 3, NA) %>% 
  sum(na.rm = TRUE) %>% 
  sqrt()

# ok, hier nicht zwingend nötig, aber zur Veranschlaulichung des Prinzips.. ;)
```

Pipe-basierte workflows sind ...

- intuitiv lesbar, da sie der normalen Leserichtung entsprechen.
- einfacher zu schreiben, da die umschließende Klammerung von Funktionsaufrufen entfällt. Funktionsargumente sind direkt den jeweiligen Funktionen zuordenbar.
- einfach zu erweitern, da neue Arbeitsschritte einfach an das Ende der Kette angehängt werden können (z.B. `write_csv("bla.csv")` oder bestehende Arbeitsschritte ausgetauscht werden können.
- reduzieren oftmals die Anzahl der temporären Variablen, die im globalen Workspace herumliegen und die Lesbarkeit des Codes beeinträchtigen.
- einfach zu debuggen, da Zwischenergebnisse einfach ausgegeben werden können (einfach `view()` oder `print()` an das Ende der entsprechenden Zeile anhängen).
- einfach wiederzuverwenden, da der gesamte Workflow in einer Zeile zusammengefasst ist und immer als Block aufgerufen wird. Dadurch können keine Zwischenschritte vergessen werden und der Workflow ist immer konsistent.

::: callout

## Mehrzeilige R Kommandos

**Wichtig:** R Kommandos können über mehrere Zeilen verteilt werden, wie im obigen Beispiel zu sehen ist, was die Übersichtlichkeit des Codes erhöht.

Hierzu wird z.B. der Pipe-Operator `|>` ans Ende der Zeile geschrieben und der nächste Arbeitsschritt in der nächsten Zeile fortgesetzt.
Gleiches gilt für jeden Operator (`+`,  `==`, `&`, ..) sowie unvollständige Funktionsaufrufe, bei denen die Klammerung noch nicht geschlossen ist (d.h. schließende Klammer wird in der nächsten oder einer späteren Zeile geschrieben).

:::::::::::


## Tabellen verarbeiten

Die Transformation von Daten erfolgt i.d.R. mittels des `dplyr` packages.

*Grundlegend gilt: das erste Argument einer Funktion ist immer der Datensatz!*

Innerhalb von `dplyr` Funktionen können *Spaltennamen ohne Anführungzeichen* (quoting) verwendet werden.

Basistransformationen sind:

-   Filtern von Zeilen mit gegebenen Kriterien (formulieren was man BEHALTEN will!) macht man in eckigen Klammern
    -   `storms[storms.year == 2020]` = alle Sturmdaten aus dem Jahr 2020
    -   `storms[(storms.year == 2020) & (storms.month == 6)]` = alle Sturmdaten aus dem Juni 2020. Bitte beachten, dass logisch verknüpfte Bedingungen in Klammern stehen müssen und die einzelnen Bedingungen mit `&` (UND) oder `|` (ODER) verknüpft werden.
    -   `storms[~pd.isna(storms.category)]` = alle Sturmdaten, bei denen die Kategorie bekannt ist, wobei `~` für Negation steht und `pd.isna()` kurz für *is not available* ist.

- Konkrete Zeilenauswahl (via Index oder Anzahl) geht mit der Funktion `.loc[]` und der Übergabe eines einzelnen Zeilenindex oder einer Liste von Zeilenindizes
    -   `storms.loc[25]` = die Zeile 25
    -   `storms.loc[[1, 3, 5]]` = die Zeilen 1, 3 und 5
    -   `storms.head(10)` = die 10 ersten Zeilen
    -   `storms.tail(10)` = die 10 letzten Zeilen    
    -   `storms[storms.pressure == storms.pressure.max()]` = die Zeile(n) mit dem höchsten Wert in der Spalte `pressure`

-   Sortieren von Zeilen
    -   `storms.sort_values("year")` = Sturmdaten nach Jahr aufsteigend sortieren
    -   `storms.sort_values("year", ascending=False)` = Sturmdaten nach Jahr absteigend sortieren
    -   `storms.sort_values(["year", "month"], ascending=[True, False])` = Sturmdaten aufsteigend nach Jahr sortieren und innerhalb eines Jahres absteigend nach Monat

-   Duplikate entfernen
    -   `storms.drop_duplicates()` = alle Zeilen mit identischen Werten in allen Spalten entfernen
    -   `storms[["year", "month", "day"]].drop_duplicates()` = alle Zeilen mit gleichen Werten in den Spalten `year`, `month` und `day` entfernen (reduziert die Spalten auf die Ausgewählten)
    -   `storms.drop_duplicates(["year", "month", "day"])` = alle Zeilen mit gleichen Werten in den Spalten `year`, `month` und `day` entfernen, aber *alle Spalten behalten*

-   Auswählen/Entfernen von Spalten
    -   `storms[["year", "month", "day"]]` = nur Spalten mit Zeitinformation und Namen der Stürme behalten
    -   `storms.drop(columns=["year", "month", "day"])` = alle Datumsspalten entfernen

-   Umbenennen von Spalten
    -   `storms.rename(columns={"name": "sturmname"})` = Spalte `name` in `sturmname` umbenennen

-   Zusammenfassen von Daten: nur eine Zeile mit aggregierten Informationen (z.B. Mittelwert, Summe, Anzahl, etc.) pro Gruppe
    -   `storms.agg({"wind": "max", "name": "count"})` = maximale (`max`) Windgeschwindigkeit und Anzahl (`count`) der Datensätze (Zeilen); auch Durchschnitt (`avg`), Summe (`sum`) und Minimum (`min`) sind möglich

-   Gruppierung von Daten = "Zerlegung" des Datensatzes in Teiltabellen, für die anschliessende Arbeitsschritte unabängig voneinander durchgeführt werden. Wird i.d.R. verwendet, wenn die Aufgabe "pro ..." oder "für jede ..." lautet.
    -   `storms.groupby("year")` = Gruppierung der Sturmdaten nach Jahr (aber noch keine Aggregation!); das ist nur ein Zwischenschritt, der die unterschiedlichen Werte der Jahre ermittelt und für die folgenden Arbeitsschritte bereitstellt
    -   `storms.groupby("year").agg({"wind": "max", "name": "count"})` = maximale Windgeschwindigkeit und Anzahl der Datenzeilen *pro Jahr*
    -  `storms[storms['wind'] == storms.groupby('year')['wind'].transform('max')]` = alle Sturmdaten, bei denen die maximale Windgeschwindigkeit des jeweiligen erreicht wurde (keine Zusammenfassung!). `transform
    - Grouping ist ein extrem mächtiges Werkzeug, das in vielen Situationen verwendet wird, um Daten zu transformieren. Allerdings braucht es etwas Übung, um zu verstehen, wie es funktioniert.

- Spalten hinzufügen/ausrechnen oder bestehende Spalten verändern (z.B. Einheiten umrechnen)
    -   `storms["wind_kmh"] = storms.wind * 1.852` = Windgeschwindigkeit in Knoten nach km/h konvertieren und als *neue Spalte hinzufügen* (hier darf für die Benennung der neuen Spalte nicht die Punktnotation verwendet werden)
    -   `storms.wind = storms.wind * 1.852` = Windgeschwindigkeit in Knoten nach km/h konvertieren und *bestehende Spalte überschreiben*    

- Spalten entfernen
    -   `storms.pop("wind_kmh")` = die Spalte `wind_kmh` entfernen; als Ergebnis wird die Spalte zurückgegeben, mit der dann weitere Operationen außerhalb des DataFrame durchgeführt werden können, wie z.B. hier:
    -   `storms.insert(2, "wind_kmh", storms.pop("wind_kmh"))` = die Spalte `wind_kmh` an die dritte Stelle (nullbasierter Index ist 2) verschieben durch Entfernen mit `pop()` und Wiedereinfügen mit `insert` an der gewünschten Stelle mit dem gleichen Namen `wind_kmh`

:::::::::::::::::::: challenge

## Stürme vor 1980

*Erstelle eine Tabelle, welche für jeden Sturm vor 1980 neben dessen Namen nur das Jahr und dessen Status beinhaltet und nach Jahr und Status sortiert ist.*

:::::::::::: solution

# Hinweise

Gehen Sie wie folgt vor:
- Reduzieren Sie auf jene Zeilen, in denen das Jahr kleiner als 1980 ist
- Reduzieren Sie auf die Spalten `name`, `year` und `status`
- Durch die Spaltenreduktion sind Duplikate entstanden, entfernen Sie diese
- Sortieren Sie nach Jahr und Status

:::::::::::::::::::::

:::::::::::: solution

# Lösung

Eine Lösung mit Zwischenschritten, bei denen die Arbeitsschritte einzeln ausgeführt und jeweils in der Tabelle `ergebnis` gespeichert werden, könnte so aussehen:


``` python
# Filtern nach Stürmen vor 1980
ergebnis = storms[storms['year'] < 1980]
```

``` output
NameError: name 'storms' is not defined
```

``` python
# Reduzieren auf die Spalten name, year und status
ergebnis = ergebnis[['name', 'year', 'status']]
```

``` output
NameError: name 'ergebnis' is not defined
```

``` python
# Entfernen von Duplikaten
ergebnis = ergebnis.drop_duplicates()
```

``` output
NameError: name 'ergebnis' is not defined
```

``` python
# Sortieren nach Jahr und Status
ergebnis = ergebnis.sort_values(by=['year', 'status'])
```

``` output
NameError: name 'ergebnis' is not defined
```

Eine Lösung, die alle Arbeitsschritte in einer Zeile zusammenfasst, könnte so aussehen:


``` python
ergebnis = storms[storms['year'] < 1980][['name', 'year', 'status']].drop_duplicates().sort_values(by=['year', 'status'])
```

``` output
NameError: name 'storms' is not defined
```

Diese Lösung ist durch die Verkettung der Arbeitsschritte in einer Zeile kürzer und kompakter, aber auch schwerer zu lesen. Um das übersuchtlich zu halten, kann man die Arbeitsschritte auch in mehreren Zeilen schreiben. In Python setzt man dazu entweder den mehrzeilige Befehl in eine umgreifende Klammer wie hier:


``` python
(storms
  [storms['year'] < 1980]
  [['name', 'year', 'status']]
  .drop_duplicates()
  .sort_values(by=['year', 'status']))
```

``` output
NameError: name 'storms' is not defined
```

... oder aber terminiert jede Zeile mit einem Backslash:


``` python
storms \
  [storms['year'] < 1980] \
  [['name', 'year', 'status']] \
  .drop_duplicates() \
  .sort_values(by=['year', 'status'])
```

``` output
NameError: name 'storms' is not defined
```

:::::::::::::::::::::

::::::::::::::::::::::::::::::


## Gruppieren und Aggregieren

Eine der wichtigsten Funktionen in `dplyr` ist das Gruppieren von Daten und das Aggregieren von Werten innerhalb dieser Gruppen.
Dies wird in der Regel mit den Funktionen `group_by()` und `summarize()` durchgeführt.

`group_by()` teilt den Datensatz in Gruppen (imaginäre Teiltabellen) auf, basierend auf den Werten in einer oder mehreren Spalten.
Im Anschluß wird, vereinfacht gesagt, für jede Gruppe eine separate Berechnung durchgeführt.

Die Funktion `summarize()` ist die am häufigsten verwendete Funktion, um Werte innerhalb dieser Gruppen zu aggregieren.

Ein einfaches Beispiel:


``` r
storms |>
  # Tabelle nach Jahr gruppieren
  group_by(year) |>
  # maximale Windgeschwindigkeit pro Jahr berechnen
  summarize(max_wind = max(wind))
```

``` error
Error in summarize(group_by(storms, year), max_wind = max(wind)): could not find function "summarize"
```

In diesem Beispiel wird die Tabelle `storms` nach dem Jahr gruppiert und für jede Gruppe (d.h. jedes Jahr) die maximale Windgeschwindigkeit berechnet.
Das Ergebnis ist eine Tabelle mit zwei Spalten: `year` und `max_wind`.

::: callout

## Achtung

Zu beachten ist, dass Gruppierungen in `dplyr` nur virtuell sind und nicht zu einer physischen Aufteilung des Datensatzes führen.
Allerdings wird diese Gruppierung aufrecht erhalten, bis sie explizit aufgehoben wird (z.B. durch `ungroup()`) oder dies implizit durch eine andere Funktion geschieht (z.B. "schließt" `summarize()` die letzte Gruppierung).

:::::::::::


:::::::::::::::::::: challenge

## Gruppiertes Filtern

*Erstelle eine Tabelle, welche für jeden Sturmstatus das Jahr und den Namen des letzten Sturms auflistet.*


:::::::::::: solution

### Erwartete Ausgabe


``` error
Error in select(ungroup(filter(mutate(group_by(storms, status), date = parse_date_time(str_c(year, : could not find function "select"
```

:::::::::::::::::::::

:::::::::::: solution

## Hinweise

- Gruppieren sie die Tabellendaten nach `status`, um für jeden Sturmtyp eine "Teiltabelle" zu erhalten
- Um den letzten Sturm zu finden
  - Möglichkeit 1: neue Spalte mit Zeitinformation zusammensetzen und damit die Zeile mit maximalen Datum (pro Teiltabelle) extrahieren
  - Möglichkeit 2: Teiltabellen bzgl. Zeitspalten sortieren und erste bzw. letzte Zeile extrahieren

:::::::::::::::::::::

:::::::::::: solution

## Lösungen 

### Alternative 1


``` r
storms |>
  # decompose table by storm status
  group_by(status) |>
  # encode time information of each entry in a single column
  mutate(date = parse_date_time(str_c(year,month,day,hour,sep="-"), "%Y-%m-%d-%H")) |>
  
  # filter for the latest date
  filter(date == max(date)) |>
# ALTERNATIVELY cut out row with latest date
  # slice_max(date, n=1) |>
  
  # rejoin table information and undo grouping
  ungroup() |> 
  select(status, year, name)
```

### Alternative 2


``` r
storms |>
  # decompose table by storm status
  group_by(status) |>
  
  # sort ascending by date (hierarchical sorting)
  arrange(year,month,day,hour) |>
  # pick last row w.r.t. sorting
  slice_tail(n=1) |>
# ALTERNATIVELY pick row with last index
  # slice( n() ) |> 
  
# OR do both (sort+pick) directly with slice_max
  # slice_max( tibble(year,month,day,hour), n=1, with_ties = F ) |> 
  
  # rejoin table information and undo grouping
  ungroup() |> 
  select(status, year, name) 
```
:::::::::::::::::::::

::::::::::::::::::::::::::::::

Eine Zusammenfassung der wichtigsten Funktionen in `dplyr` finden sich in dessen Cheat Sheet.


[![dplry cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/pngs/thumbnails/data-transformation-cheatsheet-thumbs.png){width="100%" alt="CLICK TO ENLARGE: cheat sheet for dplyr ackage"}](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-transformation.pdf)



## Tabellengestalt ändern

Die "tidy" Form ist eine spezielle Form von tabellarischen Daten, die für viele Datenverarbeitungsschritte geeignet ist. In der "tidy" Form sind die Daten so organisiert, dass jede Beobachtung (z.B. Messung) in einer Zeile notiert ist und jede Variable (also z.B. Beschreibungungen und Messwerte) eine Spalte definieren.
Diese Art der Tabellenform wird auch "long table" oder "schmal" genannt, weil die Daten in einer langen, schmalen Tabelle organisiert sind.

Allerdings werden Rohdaten häufig in einer "ungünstigen" Form vorliegen, die für die weitere Verarbeitung nicht optimal ist. 
Manuelle Datenerfassung erfolgt oft in grafischen Oberflächen wie MS Excel, worin die Daten i.d.R. in einer  "breiten" oder "wide table" Form gespeichert, in der eine Variable (z.B. Messwert) in mehreren Spalten gespeichert ist. 
In der "breiten" Form sind die Daten so organisiert, dass Beobachtungen (und ihre Messwerte) in Spalten gruppiert sind, während die Variablen in den Zeilen stehen.

Hier ein Beispiel aus dem Tutorial [Introduction to R/tidyverse for Exploratory Data Analysis](https://tavareshugo.github.io/r-intro-tidyverse-gapminder/)

![](https://github.com/tavareshugo/r-intro-tidyverse-gapminder/blob/e08446803314d9c8d59f309e3dee7b1cdd9a3158/fig/07-tidyr_pivot.png?raw=true){alt="wide and long table examples and respective pivoting calls" width="500px"}

Das obige Beispiel zeigt eine "breite" Tabelle (rechts) und eine "lange" Tabelle (links) mit den gleichen Daten.
Die Transformation zwischen beiden Formaten kann durch die Funktionen `pivot_longer()` und `pivot_wider()` erreicht werden.
Beachten sie im Beispiel, dass die Spaltennamen des wide table Formats in beide Transformationsrichtungen verarbeitet werden können.

::: callout

## Vor- und Nachteile

Während das wide table Format kompakter und damit ggf. besser zur Datenerfassung und -übersicht geeignet ist, ist das long table Format besser für die Datenanalyse und -visualisierung geeignet.
In letzterem liegt Information redundant vor, da in jeder Zeile die komplette Information einer Beobachtung vorliegen muss.

:::::::::::

Die tidyverse Bibliothek `tidyr` bietet Funktionen, um Daten zwischen diesen beiden Formen zu transformieren. Dies wird auch als "reshaping" oder "pivotieren" bezeichnet.
Hiermit können Spalten in Zeilen und umgekehrt umgeformt werden. 

Das Beispiel hierfür ist etwas länger, um für Anschauungszwecke die Datentabelle erst etwas einzukürzen.


``` r
storms |>
  filter(name == "Arthur" & year == 2020) |> # speziellen Sturm auswählen
  select(name, year, month, day, wind, pressure) |> # (zur Vereinfachung) nur spezifische Spalten
  # Verteile Wind und Druck in separate Zeilen mit entsprechenden Labels in einer neuen Spalte "measure"
  pivot_longer(cols = c(wind, pressure), names_to = "measure", values_to = "value") |>
  slice_head(n=4) # nur die ersten 4 Zeilen anzeigen
```

``` error
Error in slice_head(pivot_longer(select(filter(storms, name == "Arthur" & : could not find function "slice_head"
```

Details und weitere Funktionen und Beispiele sind im Cheat Sheets des `tidyr` Paketes übersichtlich zusammengefasst.

[![tidyr cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/pngs/thumbnails/tidyr-thumbs.png){width="100%" alt="CLICK TO ENLARGE: cheat sheet for tidyr ackage"}](https://raw.githubusercontent.com/rstudio/cheatsheets/main/tidyr.pdf)


## Textdaten verändern und verwenden

Um Textdaten (*Strings*) zu verändern, gibt es verschiedene Funktionen des `stringr` Paketes, die in Kombination mit `mutate()` oder anderen Funktionen verwendet werden können.
Die meisten Funktionen des `stringr` Paketes sind sehr intuitiv und haben eine ähnliche Syntax wie die Funktionen des `dplyr` Paketes.
Zudem sind viele mit regulären Ausdrücken verwendbar, um Textdaten aufgrund von allgemeineren Textmustern zu erkennen und zu verändern.
Alle Funktionen des `stringr` Paketes beginnen mit `str_` und sind in der [Dokumentation](https://stringr.tidyverse.org/reference/index.html) aufgeführt.
Einige wichtige sind:

- `str_c()`: Verkettet mehrere Strings zu einem, analog to `paste0()`
- `str_detect()`: Prüft, ob ein String einen bestimmten Textteil enthält
- `str_replace()`: Ersetzt einen Teil eines Strings durch einen anderen
- `str_to_lower()`/`str_to_upper`: Wandelt alle Buchstaben in Klein-/Grossbuchstaben um
- `str_sub()`: Extrahiert einen Teil eines Strings
- `str_trim()`: Entfernt Leerzeichen am Anfang und Ende eines Strings
- `str_extract()`: Extrahiert einen Teil eines Strings, der einem bestimmten Muster entspricht
- `str_remove()`: Entfernt einen Teil eines Strings, der einem bestimmten Muster entspricht
- `str_split()`: Teilt einen String an einem bestimmten Trennzeichen auf

Die meisten Funktionen liefern nur einen Treffer oder verändern nur den ersten Treffer.
Daher gibt es von vielen Funktionen Varianten, die alle Treffer finden und/oder verarbeiten, z.B. `str_detect_all()`, `str_replace_all()`, `str_extract_all()`.

Die Funktionen sind im [`stringr` Cheat Sheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/strings.pdf) zusammengefasst.

[![stringr cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/pngs/thumbnails/strings-cheatsheet-thumbs.png){width="100%" alt="CLICK TO ENLARGE: cheat sheet for stringr ackage"}](https://raw.githubusercontent.com/rstudio/cheatsheets/main/strings.pdf)


Beispiele für deren Verwendung mit `mutate()` und Co. sind:


``` r
# Beispiel: Grossbuchstaben in Spalte "name" in Kleinbuchstaben umwandeln
# (und nur eindeutige Einträge in Spalte "name" anzeigen)
mutate(storms, name = str_to_lower(name)) |> distinct(name)
# Beispiel: Jahrhundert aus Spalte "year" extrahieren
mutate(storms, century = str_sub(year, 1, 2)) |> distinct(name, century)
 # oder via regulärem Ausdruck
mutate(storms, century = str_extract(year, "^\\d{2}"))
mutate(storms, century = str_remove(year, "..$"))
# Beispiel: Nur Stürme mit "storm" im Status behalten
filter(storms, str_detect(status, "storm")) |> distinct(name)
```

Beachte:

1.  wenn die (Daten)Eingabe einer `stringr` Funktion ein *Vektor* ist, wird die Funktion auf jedes Element des Vektors angewendet und gibt einen Vektor mit den Ergebnissen zurück. (= *vektorisierte Prozessierung* = zentrales Verarbeitungsprinzip in R)
2.  wenn die Eingabe *kein* String ist (z.B. eine Zahl), wird die Eingabe in einen String umgewandelt und die Funktion auf den entstandenen String angewendet. (= *automatische Typumwandlung*, sogenanntes *coercion* in R)


## Reguläre Ausdrücke

Für komplexe Textverarbeitung werden oft reguläre Ausdrücke verwendet.
Diese erlauben es, Textmuster zu definieren, die in einem Text gesucht, ersetzt oder extrahiert werden können.

Für eine Einführung in reguläre Ausdrücke siehe [RegexOne Tutorial](https://www.regexone.com/) oder [RStudio RegEx Cheat Sheet (pdf)](https://rstudio.github.io/cheatsheets/regex.pdf).


:::::::::::::::::::: challenge

## Reguläre Ausdrücke

Betrachten sie noch einmal die beiden `mutate()` Beispiele von oben, in denen das Jahrhundert aus der Spalte "year" mit Hilfe von regulären Ausdrücken extrahiert wird.

*Was genau definieren die beiden regulären Ausdrücke `^\\d{2}` und `..$`?*


:::::::: solution

### Lösung

- `^\\d{2}` = "die ersten zwei (`{2}`) Ziffern (`\\d`) am Textanfang (`^`)"
  - hier genutzt um das Jahrhundert mit `str_extract()` zu extrahieren
- `..$` = "die letzten zwei beliebigen Zeichen (`..`) am Ende des Textes (`$`)"
  - hier genutzt um die letzten zwei Zeichen (Jahrzehnt) mit `str_remove()` zu entfernen

:::::::::::::::::

::::::::::::::::::::::::::::::




## Daten zusammenführen

Oftmals liegen daten in mehreren Tabellen vor, die zusammengeführt werden müssen, um die Daten zu analysieren.
Zum Beispiel können Daten in einer Tabelle die Anzahl der Sturmtage pro Jahr enthalten und in einer anderen Tabelle die Kosten für Sturmschäden pro Jahr.


``` r
# Datensatz mit Sturmschäden pro Jahr (zufällige Werte) für 2015-2024
costs <-
  tibble(year = 2015:2024, # Jahresbereich festlegen
         costs = runif(length(year), 1e6, 1e8)) # Zufallsdaten gleichverteilt erzeugen

# Anzahl der Sturmtage pro Jahr
stormyDays <-
  storms |>
  # Datensatz in Einzeljahre aufteilen
  group_by(year) |>
  # nur eine Zeile pro Monat/Jahr Kombination (pro Jahr) behalten
  distinct(month, day) |>
  # zählen, wie viele Zeilen pro Jahr noch vorhanden sind
  count() |>
  ungroup()


# Beispiel (1): Sturmtaginformation (links) mit Kosteninformation (rechts) ergänzen
# BEACHTE: für Jahre ohne Eintrag in `costs` wird `NA` eingetragen !
left_join(stormyDays, costs, by = "year") |>
  # nur die ersten und letzten 3 Zeilen anzeigen
  slice( c(1:3, (n()-2):n()) )

# Beispiel (2): nur Datensätze für die beides, d.h. Sturmtage als auch Kosten, vorhanden ist
inner_join(stormyDays, costs, by = "year")
```


:::::::::::::::::::: challenge

## Spaltennamen

Studieren sie die [Hilfeseite der join Funktionen](https://dplyr.tidyverse.org/reference/mutate-joins.html) und finden sie heraus, *wie sie die Spaltennamen der beiden Tabellen angeben können*, um die Daten zu verbinden, wenn die gemeinsamen Daten in unterschiedlich benannten Spalten stehen.

:::::::: solution

## Lösung

Das mapping der Spalten erfolgt über einen *benannten Vektor*, d.h. die Spaltennamen der linken Tabelle werden als Elementnamen für die entsprechenden Spaltennamen der rechten Tabelle verwendet:


``` r
stormyDays |> 
  rename( Jahr = year ) |> # Jahresspalte exemplarisch umbenannt
  filter( Jahr >= 2013 ) |> 
  # geänderte Sturmtaginformation (links) aus der pipe
  # mit Kosteninformation (rechts) aus expliziter Tabelle "costs" ergänzen
  left_join( costs, 
      # Explizites mapping von costs$year auf die "Jahr" Spalte der linken Tabelle
      # Beachten sie das quoting der Spaltennamen!
             by = c(Jahr = "year")
      # Alternativ kann das mapping auch über eine Funktion erfolgen:
      # Hier sind keine Anführungszeichen notwendig:
      #  by = join_by(Jahr = year)
      ) 
```

:::::::::::::::::

::::::::::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: keypoints

- Speichern sie Daten nur in Variablen zwischen, wenn sie diese Daten mehrfach benötigen.
- Verwenden sie Pipes (`|>`) um Daten durch eine Reihe von Transformationen zu leiten.
- Versuchen sie die Datenverarbeitung in kleine, leicht verständliche Schritte zu unterteilen.
- Vermeiden sie unnötige Schleifen und Schachtelungen, das meiste lässt sich mit Grouping, vektorisierten Operationen und Joins kompakter und eleganter lösen.
- Auch explizite Elementzugriffe (z.B. `df$col`) und -operationen können i.d.R. effizient durch `dplyr` Funktionen ersetzt werden.
- Zusammenfassungen der Pakete:
  - [`dplry` Cheat Sheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-transformation.pdf)
  - [`tidyr` Cheat Sheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/tidyr.pdf)
  - [`stringr` Cheat Sheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/strings.pdf)

::::::::::::::::::::::::::::::::::::::::::::::::
