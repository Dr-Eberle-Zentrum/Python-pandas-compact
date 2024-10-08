---
title: Setup
---


## Daten

Folgende Dateien werden in den Beispielen verwendet:

- [storms-2019-2021.csv](data/storms-2019-2021.csv) - deutsche CSV Datei
- [storms-2019-2021.xlsx](data/storms-2019-2021.xlsx) - Microsoft Excel Datei

## Voraussetzungen

Folgende Installationen werden benötigt:

- R version 4.2.0 oder neuer
- `tidyverse` packages via `install.packages(c("tidyverse", "readxl", "writexl"))`
  - `tibble` - [Cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/dataframe-2.1.pdf) - Tabellendatenstruktur
  - `magrittr` - [Cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/magrittr.pdf) - Pipe Operator `%>%`
  - `readr`, `readxl`, `writexl` - [Cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/data-import.pdf) - Datenimport & -export
  - `dplyr` - [Cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/data-transformation.pdf) - Datentransformation & -verarbeitung
  - `stringr` - [Cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/strings.pdf) - Textmanipulation
  - `tidyr` - [Cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/tidyr.pdf) - Datenbereinigung
  - `ggplot2` - [Cheatsheet](https://raw.githubusercontent.com/rstudio/cheatsheets/master/data-visualization-2.1.pdf) - Visualisierung

Alle Codebeispiele können in RStudio ausgeführt werden, wenn zuvor `tidyverse` geladen wurde.
Zum Beispiel via `library(tidyverse)`.