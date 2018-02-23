---
output:
  pdf_document: default
  html_document: default
---
Ridgeplots 

Load in our libraries

install(tidyverse) # loads in all the tidyverse libraries
library(lubridate) # to make dealing with dates easier
library(ggridges) # the brand new ggjoy package

bugs <- as_data_frame(read.csv("insect_data.csv"))


```r
# load in libraries
library(tidyverse)  # loads in all the tidyverse libraries
```

```
## Warning: package 'tidyverse' was built under R version 3.3.3
```

```
## -- Attaching packages ---------------------------------- tidyverse 1.2.1 --
```

```
## v ggplot2 2.2.1     v purrr   0.2.4
## v tibble  1.4.2     v dplyr   0.7.4
## v tidyr   0.8.0     v stringr 1.2.0
## v readr   1.1.1     v forcats 0.3.0
```

```
## Warning: package 'ggplot2' was built under R version 3.3.3
```

```
## Warning: package 'tibble' was built under R version 3.3.3
```

```
## Warning: package 'tidyr' was built under R version 3.3.3
```

```
## Warning: package 'readr' was built under R version 3.3.3
```

```
## Warning: package 'purrr' was built under R version 3.3.3
```

```
## Warning: package 'dplyr' was built under R version 3.3.3
```

```
## Warning: package 'stringr' was built under R version 3.3.3
```

```
## Warning: package 'forcats' was built under R version 3.3.3
```

```
## -- Conflicts ------------------------------------- tidyverse_conflicts() --
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
library(lubridate)  # to make dealing with dates easier
```

```
## Warning: package 'lubridate' was built under R version 3.3.3
```

```
## 
## Attaching package: 'lubridate'
```

```
## The following object is masked from 'package:base':
## 
##     date
```

```r
library(ggridges)   # the brand new ggjoy package
```

```
## Warning: package 'ggridges' was built under R version 3.3.3
```

```r
## Dataset: 
# I'm using a dataset that contains information about what bugs showed up in a light trap set up  # on the roof of the University of Copenhagen's Zoological Museum and when. 

# read in data & convert it to a tibble
bugs <- as_data_frame(read.csv("insect_data.csv"))

head(bugs)
```

```
## # A tibble: 6 x 7
##   order       family       name              year date1  date2 individuals
##   <fct>       <fct>        <fct>            <int> <fct>  <fct>       <int>
## 1 LEPIDOPTERA ACROLEPIIDAE Acrolepiopsis a~  1994 8/12/~ 8/21~           1
## 2 LEPIDOPTERA ACROLEPIIDAE Acrolepiopsis a~  1994 8/22/~ 8/25~           1
## 3 LEPIDOPTERA ACROLEPIIDAE Acrolepiopsis a~  1996 7/24/~ 7/25~           1
## 4 LEPIDOPTERA ACROLEPIIDAE Acrolepiopsis a~  1998 7/13/~ 7/19~           1
## 5 LEPIDOPTERA ACROLEPIIDAE Acrolepiopsis a~  1999 8/9/99 8/10~           1
## 6 LEPIDOPTERA ACROLEPIIDAE Acrolepiopsis a~  1999 8/30/~ 8/31~           1
```

```r
## Add a coulmn with the month of each observation

# mdy() tells the lubridate package what format our dates are in; i.e., "2003-07-15"
# month() says we only want the month from the date; i.e., 1-12
bugs$month  <- month(mdy(bugs$date1))
```

```r
# list of months for labelling graph
monthList <- c("Jan","Feb","Mar","April","May", "June","July","Aug","Sep","Oct","Nov","Dec")

# remap months from numbers (3:12) to words (March-December)
bugs$month <- plyr::mapvalues(bugs$month, levels(as.factor(bugs$month)), monthList[3:12])
```

```r
# plot the nubmer of bugs caught by month
ggplot(data = bugs, aes(x = month, y = individuals)) + geom_point() + 
  scale_x_discrete(limit=monthList)
```

![](Ridgeplots_files/figure-latex/unnamed-chunk-5-1.pdf)<!-- --> 
Comment:
Most insects show up in the summer. (Denmark is in the Northern Hemisphere, so summer runs from June to September). 


```r
# get the total number of insects observed on each day (binning over years)
bugs$dayInYear <- yday(mdy(bugs$date1))

# joyplot of when insects were observed by order (this is a variable). Scale changeshow tall the peaks are
ggplot(data = bugs, aes(x = dayInYear, y = order)) + geom_density_ridges(scale = 0.9) + 
    theme_ridges()
```

```
## Picking joint bandwidth of 3.36
```

![](Ridgeplots_files/figure-latex/unnamed-chunk-6-1.pdf)<!-- --> 
Comment:
It looks like both orders (beetles & butterflies) tend to show up at roughly the same time

```r
# how events have shifted over time

# ridgeplot of dates on which insects were observed by year of observation
ggplot(data = bugs, aes(x = dayInYear, y = as.factor(year))) + 
    geom_density_ridges(scale = 0.9) + theme_ridges()
```

```
## Picking joint bandwidth of 4.68
```

![](Ridgeplots_files/figure-latex/unnamed-chunk-7-1.pdf)<!-- --> 
Comment: Maybe a little bit of shift.

```r
# look at the variance 
varianceByYear <- bugs %>% group_by(year) %>% summarise(variance = sd(dayInYear))

# plot variance by year
ggplot(varianceByYear, aes(year, variance)) + geom_line() + 
   geom_smooth(method='lm') # this function adds the fitted line (w/ confidence interval)
```

![](Ridgeplots_files/figure-latex/unnamed-chunk-8-1.pdf)<!-- --> 

Comment:
It looks like there been increasing variance in what day of the year insects are observed in this light trap.










