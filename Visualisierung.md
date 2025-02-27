---
title: "Visualisierung"
teaching: 15
exercises: 10
---




``` output
ModuleNotFoundError: No module named 'pandas'
```

``` output
ModuleNotFoundError: No module named 'plotnine'
```


:::::::::::::::::::::::::::::::::::::: questions

- Wie visualisiere ich Daten?

::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::: objectives

- Datenvisualisierung mit Dataframes und `plotnine` in Python

::::::::::::::::::::::::::::::::::::::::::::::::

Die Visualisierung von Daten ist ein wichtiger Bestandteil der Datenanalyse, da sie es ermöglicht, Muster und Zusammenhänge in den Daten zu erkennen und zu kommunizieren. In Python gibt es mehrere Pakete für Datenvisualisierung. Ein beliebtes, das wir auch hier vorstellen werden, ist `plotnine`, das auf *ggplot* und der [*Grammar of Graphics*](https://r4ds.had.co.nz/data-visualisation.html) basiert. Hierbei müssen die Daten in tabellarischer Form vorliegen, d.h. jede Zeile entspricht einem Datensatz und jede Spalte einer Variable ("tidy data").

Die Visualisierung von Daten wird in verschiedene Schichten (z.B. Punkte, Linien, Balken) und Eigenschaften (z.B. x-Achse, y-Achse, Farbe, Form) unterteilt. Die Verküpfung von Tabellenspalten mit den Ebenen und Eigenschaften (d.h. Welche Information wird wie fürs Plotting verwendet) erfolgt über die Funktion `aes()`, was für *aesthetic mapping* steht. Geometrische Schichten (z.B. Balken, Linien, Punkte) werden mit `geom_*()` Funktionen hinzugefügt.

Im Folgenden wird ein umfangreiches Beispiel für die Visualisierung von Daten mit `plotnine` gezeigt, bei dem die Anzahl der Stürme pro Jahr visualisiert wird.


``` python
import pandas as pd
```

``` output
ModuleNotFoundError: No module named 'pandas'
```

``` python
# Alle Funktionen des Pakets plotnine importieren:
from plotnine import *
```

``` output
ModuleNotFoundError: No module named 'plotnine'
```

``` python
# Sturmdaten aus dem Internet laden
storms = pd.read_csv("https://raw.githubusercontent.com/tidyverse/dplyr/master/data-raw/storms.csv")
```

``` output
NameError: name 'pd' is not defined
```

``` python
# Zunächst einen Dataframe mit der Anzahl der Stürme pro Jahr erstellen
storms_per_year = (storms
    # Duplikate entfernen um nur die Spalten "year" und "name" behalten
    .drop_duplicates(["year", "name"])
    # Gruppieren nach Jahr
    .groupby("year")
    # Anzahl der Stürme ermitteln
    .agg({"name": "count"})
    # Index zurücksetzen
    .reset_index()
    # Anzahl-Spalte umbenennen in "storm_count"
    .rename(columns={"name": "storm_count"})
)
```

``` output
NameError: name 'storms' is not defined
```

``` python
# Plot erstellen
plot = (ggplot(storms_per_year, mapping=aes(x="year", y="storm_count"))
    # Diagrammtitel und Achsenbeschriftung
    + labs(title="Anzahl der Stürme pro Jahr", x="Jahr", y="Anzahl")
    # grundlegende Diagrammformatierung
    + theme_minimal()
    # Balkendiagramm mit Anzahl der Stürme pro Jahr
    + geom_bar(fill="skyblue", stat="identity")
    # gepunktete horizontale Linie bei 20
    + geom_hline(yintercept=20, linetype="dotted", color="red")
    # Highlighting der Jahre mit mehr als 20 Stürmen
    + geom_vline(
        data=lambda x: x[x["storm_count"] > 20],
        mapping=aes(xintercept="year"),
        color="red"     # Schriftfarbe rot
    )
    # Jahreszahlen der Jahre mit mehr als 20 Stürmen in schräger Textausrichtung
    + geom_text(
        data=lambda x: x[x["storm_count"] > 20],
        mapping=aes(label="year", x="year", y="storm_count"),
        angle=70,       # Schräge Textausrichtung
        nudge_y=1,      # Text leicht nach oben versetzen
        nudge_x=-0.8,   # Text leicht nach links versetzen
        size=8,         # Schriftgröße 8
        color="red"     # Schriftfarbe rot
    )    
)
```

``` output
NameError: name 'ggplot' is not defined
```

``` python
# Plot anzeigen
plot.show()
```

``` output
NameError: name 'plot' is not defined
```


``` python
# optional: Diagramm als Bilddatei speichern
plot.save("storms_per_year.png", width=10, height=10, dpi=300)
```

Als Nachschlagewerk für alle verfügbaren und im Beispiel verwendeten Funktionen empfiehlt sich die [plotnine Dokumentation](https://plotnine.org/reference), in der die Funktionen gruppiert nach deren Zweck gelistet und detailliert beschrieben sind.

Einen empfehlenswerten Cheatsheet für plotnine gibt es leider nicht, aber es gibt ein [Cheatsheet für ggplot2](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-visualization.pdf), das einen guten Überblick über die ggplot-Funktionen enthält. Das Cheatsheet enthält einige für die Programmiersprache R spezifische Zeilen, aber die meisten Funktionen sind auch in plotnine verfügbar mit gleicher oder ähnlicher Syntax in Python:

[![ggplot2 cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/pngs/thumbnails/data-visualization-cheatsheet-thumbs.png){width="100%" alt="Klicken zum Herunterladen: Cheatsheet für ggplot2"}](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-visualization.pdf)

::::::::::::: challenge
## Sturmposition visualisieren

Zeichnen sie ein Punktdiagram, welches für folgenden Datensatz 

- Längengrad (x-Achse) und Breitengrad (y-Achse) der Messung zeigt und die Punkte anhand des Sturmstatus einfärbt,
- die Achsen mit "Längengrad" und "Breitengrad" beschriftet, und
- den Diagrammtitel "Sturmposition" hat.



``` python
mesaurements = storms.drop_duplicates(["name", "year"], keep="last")
plot = (ggplot(
    # TODO: Plot erstellen
  )
)
plot.show()
```


:::::: solution
## Lösung


``` python
mesaurements = storms.drop_duplicates(["name", "year"], keep="last")
```

``` output
NameError: name 'storms' is not defined
```

``` python
plot = (ggplot(mesaurements, mapping=aes(x="long", y="lat", color="status"))
    + geom_point()
    + labs(
        title="Sturmposition", 
        x="Längengrad", 
        y="Breitengrad",
        color="Status" # ansonsten wird status kleingeschrieben
    )
)   
```

``` output
NameError: name 'ggplot' is not defined
```

``` python
plot.show()
```

``` output
NameError: name 'plot' is not defined
```

Alternativ zu `+ labs()` können die se Informationen auch separat mit den Funktionen `geom_title()`, `geom_xlab()`, `geom_ylab()` und `geom_color()` hinzugefügt werden. Sie können auch versuchen, alle Sturmmessungen zu visualisieren, anstatt nur die letzten Messungen pro Sturm zu verwenden, dann sind die Sturmpfade sichtbar.

:::::::::::::::

:::::::::::::::::::::::


::::::::::::::::::::::::::::::::::::: keypoints

- `plotnine` benötigt einen pandas Dataframe als Eingabe, der "tidy" ist (d.h. eine Zeile pro Beobachtung und eine Spalte pro Variable).
- Das `mapping` Argument ermöglicht mittels der `aes()` Funktion die Verknüpfung von Variablen des Datensatzes (d.h. Spaltennamen) mit visuellen Eigenschaften (z.B. x-Achse, Farbe, Größe).
- `geom_*` Funktionen fügen dem Plot Schichten hinzu (z.B. Punkte, Linien, Balken).
- `labs()` ermöglicht die Anpassung von Diagrammtitel und Achsenbeschriftung.
- `theme_*` Funktionen ermöglichen die Anpassung genereller Diagrammformatierungen (z.B. Hintergrundfarben, Schriftarten).
- Es gibt viele weitere Funktionen und Argumente, um die Darstellung von Diagrammen zu verfeinern (z.B. `facet_wrap()`, `scale_*()`, `coord_*()`).
- Diagramme können mit `save()` in beliebigem Dateiformat (PNG, PDF, SVG, ..) gespeichert werden.
- Zusammenfassung im [`ggplot2` Cheat Sheet](https://raw.githubusercontent.com/rstudio/cheatsheets/main/data-visualization.pdf)

::::::::::::::::::::::::::::::::::::::::::::::::
