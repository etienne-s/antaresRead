[![Travis Build Status](https://travis-ci.com/rte-antares-rpackage/antares-rpackageRead.svg?token=WaVoXyXk2rzKrq8scC1K&branch=master)](https://travis-ci.com/rte-antares-rpackage/antares-rpackageRead)
[![Appveyor Build status](https://ci.appveyor.com/api/projects/status/4xo13npbnexxfrvs?svg=true)](https://ci.appveyor.com/project/rte-antares-rpackage/antares-rpackageread)
[![codecov](https://codecov.io/gh/rte-antares-rpackage/antares-rpackageRead/branch/master/graph/badge.svg?token=UuWN3YaVEq)](https://codecov.io/gh/rte-antares-rpackage/antares-rpackageRead)
[![CRAN_Status_Badge](http://www.r-pkg.org/badges/version/antaresRead)](https://cran.r-project.org/package=antaresRead)


# Read data from an Antares study with R package 'antaresRead'


## Installation

You can install the package from CRAN:
```r
install.packages("antaresRead")
```

You can also install the last development version from Github:
```r
devtools::install_github("rte-antares-rpackage/antaresRead", ref ="develop")
```

To display the help of the package and see all the functions it provides, type:
```r 
help(package="antaresRead")
```

To see a practical example of use of the package, look at the vignette :
```r
vignette("antares")
```

Finally, you can download a cheatsheet that summarize in a single page how to use the package: https://github.com/rte-antares-rpackage/antaresRead/raw/master/cheat_sheet/antares_cheat_sheet_en.pdf .


## Initialisation

Load the package

```r
library(antaresRead)
```

Select an Antares simulation interactively.

```r
setSimulationPath()
```

You can also select it programmatically:

```r
setsimulationPath("study_path", simulation)
```

The parameter `simulation` can be the name of a simulation, the name of the folder containing the simulation results, or the index of the simulation. `1` corresponds to the oldest simulation, `-1` to the newest one, 0 to the inputs.


## Read data from a simulation

Most data from a simulation can be imported in the R session with function `readAntares()`. It has many parameters that control what data is imported. Here are a few examples: 

```r
# Read synthetic results of all areas of a study with hourly time step.
areaData <- readAntares(areas = "all")

# Same but with a daily time step:
areaData <- readAntares(areas = "all", timeStep = "daily")

# Read all Monte Carlo scenarios for a given area.
myArea <- readAntares(areas = "my_area", mcYears = "all")

# Same but add miscelaneous production time series to the result 
myArea <- readAntares(areas = "my_area", mcYears = "all", misc = TRUE)

# Read only columns "LOAD" and "MRG. PRICE"
areaData <- readAntares(areas = "all", select = c("LOAD", "MRG. PRICE"))
```

Functions `getAreas` and `getLinks` are helpful to create a selection of areas or links of interest. Here are a few examples:

```r
# select areas containing "fr"
myareas <- getAreas("fr")

# Same but remove areas containing "hvdc"
myareas <- getAreas("fr", exclude = "hvdc")

# Get the links that connect two of the previous areas
mylinks <- getLinks(myareas, internalOnly = FALSE)

# Get the results for these areas and links
mydata <- readAntares(areas = myareas, links = mylinks)
```

## Work with the imported data

When only one type of elements is imported (only areas or only links, etc.) `readAntares()` read antares returns a `data.table` with some extra attributes. A `data.table` is a table with some enhanced capacities offered by package `data.table`. In particular it provides a special syntax to manipulate its content:

```r
name_of_the_table[filter_rows, select_columns, group_by]
```

Here are some examples:

```r
# Select lines based on some criteria
mydata[area == "fr" & month == "JUL"]

# Select columns, and compute new ones
mydata[, .(area, month, load2 = LOAD^2)]

# Aggregate data by some variables
mydata[, .(total = sum(LOAD)), by = .(month)]

# All three operations can be done with a single line of code
mydata[area == "fr", .(total = sum(LOAD)), by = .(month)]

help(package = "data.table")
```

If you are not familiar with package `data.table`, you should have a look at the documentation and especially at the vignettes of the package:

```r
help(package="data.table")
vignette("datatable-intro")
```
## Contributing:

Contributions to the library are welcome and can be submitted in the form of pull requests to this repository.

The folder test_case contains a test Antares study used to run automatic tests. If you modifies it, you need to run the following command to include the modifications in the tests:

```r
tar(
  tarfile = "inst/testdata/antares-test-study.tar.gz", 
  files = "test_case", 
  compression = "gzip"
)
```

## License Information:

Copyright 2015-2016 RTE (France)

* RTE: http://www.rte-france.com

This Source Code is subject to the terms of the GNU General Public License, version 2 or any higher version. If a copy of the GPL-v2 was not distributed with this file, You can obtain one at https://www.gnu.org/licenses/old-licenses/gpl-2.0.en.html.
