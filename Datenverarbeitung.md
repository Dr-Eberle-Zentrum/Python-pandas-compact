---
title: "Datenverarbeitung"
teaching: 30
exercises: 10
---



:::::::::::::::::::::::::::::::::::::: questions

- Wie wähle ich Zeilen und Werte aus einem Dataframe aus?
- Wie erstelle ich komplexe Datenverarbeitungsworkflows?
- Wie transformiere ich Tabellen?
- Wie bearbeite ich Text in Zellen?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Zugriff auf und Filtern von Zeilen
- Sortieren von Spalten und Entfernen von Duplikaten
- Gruppieren und Aggregieren von Daten
- Ändern der Tabellengestalt von schmal zu breit und umgekehert
- Verändern von Zellinhalten

::::::::::::::::::::::::::::::::::::::::::::::::

## Zeilen via Index auswählen

Auswahl konkreter Zeilen via Index geht mit der Funktion `.loc[]` und der Übergabe eines einzelnen Zeilenindex, eines Indexbereichs oder einer Liste von Zeilenindizes:

- `storms.loc[25]` → die Zeile mit Index 25, also die 26. Zeile, da 0-basierter Index
- `storms.loc[[1, 3, 5]]` → die Zeilen mit Index 1, 3 und 5
- `storms.loc[10:20]` → die Zeilen mit Index 10 bis 19. Achtung: der Index links vom Doppelpunkt ist inklusive, rechts exklusive!
- `storms.loc[100:]` → alle Zeilen ab Zeile mit Index 100
- `storms.loc[:25]` → die ersten 25 Zeilen, entspricht `storms.head(25)` und `storms.loc[0:25]`  
- `storms.head(10)` → die 10 ersten Zeilen
- `storms.tail(10)` → die 10 letzten Zeilen
- `storms[storms.pressure == storms.pressure.max()]` = die Zeile(n) mit dem höchsten Wert in der Spalte `pressure`

Hier ist mehrmals der Begriff Index in Bezug auf die Zeilen eines Dataframe vorgekommen. In einem Dataframe ist jeder Eintrag (Zeile) mit einem Index versehen, der standardmäßig mit 0 beginnt und um 1 erhöht wird. Dieser Index ist eine eindeutige Nummer, die jede Zeile im Dataframe identifiziert. Wenn nun durch Filtern oder andere Operationen Zeilen entfernt werden, bleibt der Index erhalten und wird nicht neu sortiert. Das bedeutet, dass der Index nicht mehr mit der Zeilennummer übereinstimmen muss. Wenn Sie also eine bestimmte Zeile auswählen möchten, sollten Sie den Index verwenden, nicht die Zeilennummer. Um das zu illustrieren:




``` python
persons = pd.DataFrame({
    "name": ["John", "Paula", "Georgia", "Ringo"],
    "age": [45, 17, 20, 24]
})

print(persons)
```

``` output
      name  age
0     John   45
1    Paula   17
2  Georgia   20
3    Ringo   24
```

Wenn wir nun Filtern auf alle Personen, die älter als 22 sind, erhalten wir einen Dataframe mit zwei Zeilen, deren Index 0 und 3 ist:


``` python
older = persons[persons.age > 22]
print(older)
```

``` output
    name  age
0   John   45
3  Ringo   24
```

Um also im Dataframe `older` auf die zweite Zeile (die Person mit Namen Ringo) zuzugreifen, müssen wir - vielleicht etwas unintuitiv - den Index 3 verwenden:


``` python
print(older.loc[3])
```

``` output
name    Ringo
age        24
Name: 3, dtype: object
```

Um über die Zeilennummer und nicht über ihren Index zuzugreifen, können wir entweder den Index zurücksetzen mittels `reset_index()` oder die Methode `iloc[]` aufrufen. Beide liefern das gleiche Ergebnis:


``` python
# Name der Person in der zweite Zeile unabhängig vom Index
print(older.iloc[1]["name"])   # gibt "Ringo" aus

# Index zurücksetzen, dann entspricht der Zeilenindex der Zeilennummer
older.reset_index(inplace=True)
print(older.loc[1]["name"])    # gibt ebenso "Ringo" aus
```

## Filtern von Zeilen

Das Filtern mit gegebenen Bedingungen (formulieren was man behalten will!) macht man in eckigen Klammern, sodass ein Dataframe zurückgegeben wird, der nur jene Zeilen enthält, die den Kriterien entsprechen. Der ursprüngliche Dataframe wird dabei nicht verändert:

- `storms[storms.year == 2020]` → alle Sturmdaten aus dem Jahr 2020
- `storms[(storms.year == 2020) & (storms.month == 6)]` → alle Sturmdaten aus dem Juni 2020. Bitte beachten Sie, dass logisch verknüpfte Bedingungen jeweils in Klammern stehen müssen und die einzelnen Bedingungen mit einfachem `&` (*UND*) oder `|` (*ODER*) verknüpft werden.
- `storms[~pd.isna(storms.category)]` → alle Sturmdaten, bei denen die Kategorie bekannt ist, wobei `~` für Negation steht und `isna()` für *is not available* steht.

## Daten sortieren

Die Sortierung eines Dataframe kann mit der Funktion `sort_values()` erfolgen, die die Daten nach einer oder mehreren Spalten sortiert. Die Sortierung erfolgt standardmäßig aufsteigend, kann aber mit dem Argument `ascending=False` umgekehrt werden:

- `storms.sort_values("year")` → Sturmdaten nach Jahr aufsteigend sortieren
- `storms.sort_values("year", ascending=False)` → Sturmdaten nach Jahr absteigend sortieren
- `storms.sort_values(["year", "month"], ascending=[True, False])` → Sturmdaten aufsteigend nach Jahr sortieren und innerhalb eines Jahres absteigend nach Monat

## Duplikate entfernen

Duplikate, also Zeilen, in denen paarweise die Werte in allen Spalten gleich sind, kann man mit der Funktion `drop_duplicates()` entfernen:

- `storms.drop_duplicates()` → alle Zeilen mit identischen Werten in allen Spalten entfernen
- `storms[["year", "month", "day"]].drop_duplicates()` → alle Zeilen mit gleichen Werten in den Spalten `year`, `month` und `day` entfernen (reduziert die Spalten auf die Ausgewählten)
- `storms.drop_duplicates(["year", "month", "day"])` → alle Zeilen mit gleichen Werten in den Spalten `year`, `month` und `day` entfernen, aber *alle Spalten behalten*

:::::::::::::::::::: challenge
### Stürme vor 1980

*Erstelle eine Tabelle, welche für jeden Sturm vor 1980 neben dessen Namen nur das Jahr und dessen Status beinhaltet und nach Jahr und Status sortiert ist.*

:::::::::::: solution
### Hinweise

Gehen Sie wie folgt vor:

- Reduzieren Sie auf jene Zeilen, in denen das Jahr kleiner als 1980 ist
- Reduzieren Sie auf die Spalten `name`, `year` und `status`
- Durch die Spaltenreduktion sind Duplikate entstanden, entfernen Sie diese
- Sortieren Sie nach Jahr und Status

:::::::::::::::::::::

:::::::::::: solution
### Schrittweise Lösung

Eine Lösung mit Zwischenschritten, bei denen die Arbeitsschritte einzeln ausgeführt und jeweils in der Tabelle `ergebnis` gespeichert werden, könnte so aussehen:


``` python
# Filtern nach Stürmen vor 1980
ergebnis = storms[storms['year'] < 1980]
# Reduzieren auf die Spalten name, year und status
ergebnis = ergebnis[['name', 'year', 'status']]
# Entfernen von Duplikaten
ergebnis = ergebnis.drop_duplicates()
# Sortieren nach Jahr und Status
ergebnis = ergebnis.sort_values(by=['year', 'status'])
```

:::::::::::::::::::::

:::::::::::: solution
### Lösung ohne Zwischenschritte

Eine Lösung, die alle Arbeitsschritte in einer Zeile zusammenfasst, könnte so aussehen:


``` python
ergebnis = storms[storms['year'] < 1980][['name', 'year', 'status']].drop_duplicates().sort_values(by=['year', 'status'])
```

Diese Lösung ist durch Verkettung der Funktionsaufrufe (das sog. *method chaining*) in einer Zeile kürzer und kompakter, kann aber auch schwerer zu lesen. Um das übersichtlicher zu gestalten, kann man die Arbeitsschritte auch in mehreren Zeilen miteinander verketten. In Python setzt man dazu entweder den mehrzeiligen Befehl in eine umgreifende Klammer wie hier:


``` python
(storms
  [storms['year'] < 1980]
  [['name', 'year', 'status']]
  .drop_duplicates()
  .sort_values(by=['year', 'status']))
```

``` output
         name  year          status
30        Amy  1975   extratropical
49    Blanche  1975   extratropical
111     Doris  1975   extratropical
156    Eloise  1975   extratropical
222    Gladys  1975   extratropical
..        ...   ...             ...
716     David  1979  tropical storm
773  Frederic  1979  tropical storm
839     Elena  1979  tropical storm
857    Gloria  1979  tropical storm
899     Henri  1979  tropical storm

[129 rows x 3 columns]
```

... oder aber terminiert jede Zeile - bis auf die letzte - mit einem Backslash:


``` python
storms \
  [storms['year'] < 1980] \
  [['name', 'year', 'status']] \
  .drop_duplicates() \
  .sort_values(by=['year', 'status'])
```

``` output
         name  year          status
30        Amy  1975   extratropical
49    Blanche  1975   extratropical
111     Doris  1975   extratropical
156    Eloise  1975   extratropical
222    Gladys  1975   extratropical
..        ...   ...             ...
716     David  1979  tropical storm
773  Frederic  1979  tropical storm
839     Elena  1979  tropical storm
857    Gloria  1979  tropical storm
899     Henri  1979  tropical storm

[129 rows x 3 columns]
```

Beim Verketten der Arbeitsschritte wird ein Funktionsergebnis jeweils mit der nächsten Funktion weiterverabeitet. Man spart dadurch Zwischenvariablen und die Kette erlaubt es, aufgrund der aneinandergereiten Funktionsnamen den Verarbeitungsworflow sozusagen zu "lesen".

:::::::::::::::::::::

::::::::::::::::::::::::::::::

## Daten gruppieren und aggregieren

Gruppierung von Daten, also "Zerlegung" des Datensatzes in Teiltabellen, für die anschliessende Arbeitsschritte (z.B. Durchschnittswert für jede Gruppe berechnen) unabängig voneinander durchgeführt werden. Wird üblicherweise verwendet, wenn die Aufgabe "pro ..." oder "für jede ..." lautet, z.B. Anzahl der Stürme pro Jahr oder durchschnittliche Windgeschwindigkeit je Sturmkategorie.

- `storms.groupby("year")` → Gruppierung der Sturmdaten nach Jahr (aber noch keine Aggregation!); das ist nur ein Zwischenschritt, der die unterschiedlichen Werte der Jahre ermittelt und für die folgenden Arbeitsschritte bereitstellt
- `storms.groupby("year").agg({"wind": "max", "name": "count"})` → maximale (`max`) Windgeschwindigkeit und Anzahl (`count`) der Datensätze (Zeilen); auch Mittelwert (`mean`), Median (`median`), Summe (`sum`), Minimum (`min`), erste bzw. letze Zeile (`first`/`last`), und andere sind möglich
- `storms[storms['wind'] == storms.groupby('year')['wind'].transform('max')]` → alle Sturmdaten, bei denen die maximale Windgeschwindigkeit des jeweiligen erreicht wurde (keine Zusammenfassung!).

Man kann auch nach mehreren Spalten gruppieren und dann für jede Gruppe aggregieren. Folgendes Beispiel erzeugt einen Dataframe mit durchschnittlicher Windgeschwindigkeit pro Sturmstatus und Jahr


``` python
mean_winds = storms.groupby(["status", "year"]).agg({"wind": "mean"})
print(mean_winds)
```

``` output
                         wind
status        year           
disturbance   1980  20.555556
              2010  37.142857
              2011  33.000000
              2013  25.512821
              2014  23.333333
...                       ...
tropical wave 2005  28.125000
              2011  25.714286
              2012  30.000000
              2017  27.083333
              2018  33.500000

[290 rows x 1 columns]
```

:::::::::::::::::::: challenge
### Gruppiertes Filtern

*Erstelle eine Tabelle, welche für jeden Sturmstatus das Jahr und den Namen des letzten Sturms auflistet.*

:::::::::::: solution
### Erwartete Ausgabe


``` output
                       status  year    name
16953           tropical wave  2018    Kirk
17684  subtropical depression  2020   Dolly
19428             disturbance  2022   Julia
19510           extratropical  2022  Martin
19519       subtropical storm  2022  Nicole
19529               hurricane  2022  Nicole
19533          tropical storm  2022  Nicole
19535     tropical depression  2022  Nicole
19536               other low  2022  Nicole
```

:::::::::::::::::::::

:::::::::::: solution
### Hinweise

Hier gibt es viele Wege, das Ziel zu erreichen. Am einfachsten ist es in diesem Fall, wie folgt vorzugehen:

1. Sortieren des Dataframe die Daten nach Jahr, Monat und Tag
2. Dann entfernen der Duplikate von Status und  jeweils behalten der Zeile je Status mit dem höchsten Datum
3. Auswahl der Spalten für Status, Jahr und Name

:::::::::::::::::::::

:::::::::::: solution
### Lösung 

Hier die Lösung nach dem Verfahren, das unter Hinweise oben angegeben wurde:


``` python
last_storm_of_year = storms \
  # Sortierung nach Jahr, Monat, Tag aufsteigend
  .sort_values(["year", "month", "day"]) \
  # Duplikate von Status entfernen und jeweils die letzte Zeile (keep="last") behalten
  .drop_duplicates("status", keep="last") \
  # Auswahl der Spalten status, year und name
  [["status", "year", "name"]]
```

:::::::::::::::::::::

:::::::::::: solution
### Alternative Lösung

Alternativ kann man das auch mit Gruppieren und Aggregieren lösen. Hierbei sollte zunächst sichergestellt werden, dass die Datumsangaben aufsteigend sortiert werden, dann wird nach `status` gruppiert und für jede Gruppe mittels die letzte Zeile (`year="last"`, also aufgrund der aufsteigenden Sortierung die mit dem höchsten Datum) ausgewählt. Das `reset_index()` am Schluss ist optional, um den Index zurückzusetzen, weil ansonsten noch aus der Gruppierung stammende nicht-numerische Indexeinträge entsprechend des `status` vorhanden sind.


``` python
last_storm_of_year = storms \
  # Sortieren nach Datum aufsteigend
  .sort_values(["year", "month", "day"]) \
  # Gruppierung nach Status
  .groupby("status") \
  # Aggregation: Auswahl der Zeile mit dem höchsten Datum
  .agg({"year": "last", "name": "last"}) \
  # Optional: Index zurücksetzen
  .reset_index()
```

Haben Sie eine andere Lösung gefunden?

:::::::::::::::::::::

::::::::::::::::::::::::::::::

## Tabellengestalt ändern

Die "tidy" Form ist eine spezielle Form von tabellarischen Daten, die für viele Datenverarbeitungsschritte geeignet ist. In der "tidy" Form sind die Daten so organisiert, dass jede Beobachtung (z.B. Messung) in einer Zeile notiert ist und jede Variable (also z.B. Beschreibungungen und Messwerte) eine Spalte definieren. Diese Art der Tabellenform wird auch "long table" oder "schmal" genannt, weil die Daten in einer langen, schmalen Tabelle organisiert sind. Dies ist beispielsweise der Fall bei den Sturmdaten, die wir bisher verwendet haben.

Allerdings werden Rohdaten häufig in einer "ungünstigen" Form vorliegen, die für die weitere Verarbeitung nicht optimal ist. Manuelle Datenerfassung erfolgt oft in grafischen Oberflächen wie MS Excel, worin die Daten i.d.R. in einer  "breiten" oder "wide table" Form gespeichert, in der eine Variable (z.B. Messwert) in mehreren Spalten gespeichert ist. In der "breiten" Form sind die Daten so organisiert, dass Beobachtungen (und ihre Messwerte) in Spalten gruppiert sind, während die Variablen in den Zeilen stehen.

Man kann nun einen Dataframe von der "breiten" in die "schmale" Form umwandeln mit der Funktion `pivot()`. Diese erwartet folgende Parameter:

- `index`: jene Spalte bzw. Spalten des Dataframe, die in der "breiten" Form als Index für die Zeilen dienen sollte; es muss sichergestellt sein, dass die Werte in dieser Spalte bzw. die Kombinationen der Werte der Spalte eindeutig sind
- `columns`: jene Spalte des Dataframe, die in der "breiten" Form für die weiteren Spalten dienen sollte
- `values`: jene Spalte des Dataframe, die in der "breiten" Form für die Werte in den Zellen dienen sollte

Ein Beispiel für die Umwandlung der Sturmdaten von der "breiten" in die "schmale" Form auf Grundlage des Dataframes, der das Ergebnis der letzten vorherigen Aufgabe war:


``` output
                   status  year    name
0           tropical wave  2018    Kirk
1  subtropical depression  2020   Dolly
2             disturbance  2022   Julia
3           extratropical  2022  Martin
4       subtropical storm  2022  Nicole
5               hurricane  2022  Nicole
6          tropical storm  2022  Nicole
7     tropical depression  2022  Nicole
8               other low  2022  Nicole
```

Wir wollen in der pivotierten Tabelle, dass die Zeilen wie gehabt den Sturmstatus entsprechen, die Spalten den Stürmen und die Werte in den Zellen den Jahren, in denen der Sturm der letzte mit diesem Status war. Das erreichen wir mit folgendem Code:


``` python
broad_table = last_storm_of_year \
  .pivot(index="status", columns="name", values="year") \
  .fillna("")

print(broad_table)
```

``` output
name                     Dolly   Julia    Kirk  Martin  Nicole
status                                                        
disturbance                     2022.0                        
extratropical                                   2022.0        
hurricane                                               2022.0
other low                                               2022.0
subtropical depression  2020.0                                
subtropical storm                                       2022.0
tropical depression                                     2022.0
tropical storm                                          2022.0
tropical wave                           2018.0                
```

Hinweis: `fillna("")` füllt leere Zellen mit einem leeren String, um die Ausgabe übersichtlicher zu gestalten. Ansonsten würde da `NaN` stehen, was für *Not a Number* steht.

Eine breite Tabelle in eine schmale tidy Tabelle verwandelt man mit der Funktion `.melt()`. Ein Beispiel für die Rück-Umwandlung der breiten Sturmdaten in schmale Form:


``` python
narrow_table = broad_table
  # Zunächst Index zurücksetzen, um Status als Spalte zu haben
  .reset_index() \
  # Umwandlung in schmale Form mit Spalten status, name, year
  .melt(id_vars="status", var_name="name", value_name="year") \
  # Zeilen mit leeren Werten entfernen
  .dropna()

print(narrow_table)
```

``` output
unexpected indent (<string>, line 3)
```

Hinweis: `.dropna()` am Ende ist hilfreich, da sonst beim Umwandeln die leeren Zellen der breiten Tabelle als `NaN` in der schmalen Tabelle stehen würden. Probieren Sie es gerne ohne `.dropna()` aus, um den Unterschied zu sehen.

::: callout
### Vor- und Nachteile

Während das wide table Format kompakter und damit ggf. besser zur Datenerfassung und -übersicht geeignet ist, ist das long table Format besser für die Datenanalyse und -visualisierung geeignet. In letzterem liegt Information redundant vor, da in jeder Zeile die komplette Information einer Beobachtung vorliegen muss.

:::::::::::

## Fehlende Daten füllen

Manchmal muss man mit Dataframes arbeiten, wo aufeinanderfolgende Zellen einer Spalte fehlende Werte haben. Das kann z.B. passieren, wenn bei der Datenerfassung nicht alle Werte erfasst wurden, oder - was öfter der Fall sein wird - die leeren Zellen einfach bedeuten, dass sich der Wert nicht geändert hat. Betrachten wir folgendes Beispiel des Dataframe `career`, in dem akademische Abschlüsse und Arbeitstiteln von Personen in einer Tabelle nach Jahrenzahlen, in denen sich etwas geändert hat, gespeichert sind. Wir erinnern uns, dass `NaN` inder Ausgabe für einen fehlenden Zellwert steht:


``` output
        degree          title
year                         
2000  Bachelor      Assistent
2001       NaN            NaN
2002       NaN            NaN
2003    Master            NaN
2004       NaN  Gruppenleiter
2005       NaN            NaN
2006       Dr.            NaN
2007       NaN            NaN
2008       NaN    Konzernchef
```

Hier können wir davon ausgehen, dass die Person auch in den Jahren 2001-2003 einen Bachelortitel hatte. Gleiches gilt für die Spalte `degree`. Möchte man das explizit machen im Dataframe, so hilft die Funktion `ffill()`, die man sowohl für den gesamten Dataframe als auch für einzelne Spalten aufrufen kann. Ein Beispiel für das Füllen der fehlenden Werte in allen Spalten des Dataframe:


``` python
career_complete = career.ffill()
print(career_complete)
```

``` output
        degree          title
year                         
2000  Bachelor      Assistent
2001  Bachelor      Assistent
2002  Bachelor      Assistent
2003    Master      Assistent
2004    Master  Gruppenleiter
2005    Master  Gruppenleiter
2006       Dr.  Gruppenleiter
2007       Dr.  Gruppenleiter
2008       Dr.    Konzernchef
```

Möchte man das z.B. nur für die Spalte `title`, so kann man schreiben: `career["title"] = career["title"].ffill()`.

## Tabelleninhalte verändern

Um Datentypen oder Inhalte von Zellen zu verändern, gibt es verschiedene Funktionen, je nachdem welchen Datentyp die jeweilige Spalte des Dataframe hat. 

Datentypen verändert man mit der Funktion `astype()`, die den Datentyp einer Spalte ändert. Die Funktion erwartet als Argument den neuen Datentyp, z.B. `int`, `float`, `str`, `bool`, `datetime`, `category`, etc. Ein Beispiel für die Umwandlung der ganzzahligen Spalte `year` in eine Textspalte:


``` python
storms["year"] = storms["year"].astype(str)
```

Um die Inhalte von Zellen zu verändern, gibt es unter je nach Datentyp verschiedene Funktionen. Am interessantesten dabei die Funktion für die Manupulation von Textspalten. Hier einige nützliche:

- `str.replace()`: ersetzt einen Teil eines Strings durch einen anderen, beispielsweise würde `df["preis"].str.replace("€", "$")` in der Spalte `preis` eines Dataframes alle Euro-Zeichen durch Dollar-Zeichen ersetzen
- `str.lower()`, `str.upper()`: wandelt alle Buchstaben in Klein-/Grossbuchstaben um
- `str.strip()`: entfernt Leerzeichen am Anfang und Ende eines Strings
- `str.extract()`: extrahiert einen Teil eines Strings, der einem gegebenen regulären Ausdruck entspricht. Will man beispielsweise nur die letzten beiden Stellen der Jahreszahl in der Spalte `year` extrahieren: `storms["year"].str.extract("(\\d{2})$")`. Der jeweils gefundene Wert innerhalb der Klammern wird dabei extrahiert, hier also die letzten beiden Ziffern (`(\\d{2})`) ganz am Ende (`$`) der Jahreszahl.
- `str.split()`: teilt einen String an einem bestimmten Trennzeichen auf. Hat man beispielsweise einen Dataframe `persons` in dem in der Spalte `name` der volle Name steht, und möchte diesen in eine neue Vor- und Nachnamenspalte auftrennen, kann man das mit `persons[["vorname", "nachname"]] = persons["name"].str.split(" ", expand=True)` erreichen.

:::::::::::::::::::: challenge
### Reguläre Ausdrücke

*Anstatt wie oben die letzten beiden Ziffern der Jahreszahl zu extrahieren, wollen wir das Jahrhundert extrahieren. Dazu benötigen wir einen regulären Ausdruck, der die ersten beiden Ziffern der Jahreszahl extrahiert. Wie sieht dieser Aufruf dann aus?*

:::::::: solution
### Lösung

Der reguläre Ausdruck, der die ersten beiden Ziffern der Jahreszahl extrahiert, sieht so aus: `storms["year"].str.extract("^(\\d{2})")`. Der Unterschied zum vorherigen regulären Ausdruck ist das `^`, das den Anfang des Strings markiert. Möchte man dann auch noch das extrahierte Jahrhundert in eine Ganzzahl umwandeln, so kann man noch `.astype(int)` anhängen.

:::::::::::::::::

::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: keypoints

- In pandas lassen sich sowohl ganze Zeilen, ganze Spalten, oder einzelne Zellbereich(e) bequem manipulieren
- Oft ist es nötig, die Tabellengestalt zu verändern, etwa von breit zu schmal oder umgekehrt, um Analysen und Transformationen einfacher durchführen zu können
- Versuchen sie die Datenverarbeitung in kleine, leicht verständliche Schritte zu unterteilen.
- Wenn man pandas verwendet, vermeidet man Schleifen und Schachtelungen. Solche Manipulationen macht man in pandas mit dafür geeigneten Funktionsaufrufen. Das meiste lässt sich mit Gruppieren, Aggregieren und Spaltenmanipulation erreichen.
- Das [pandas Cheatsheet](https://pandas.pydata.org/Pandas_Cheat_Sheet.pdf) kann helfen, die Funktionen und Methoden von Pandas zu verstehen und zu nutzen.

::::::::::::::::::::::::::::::::::::::::::::::::
