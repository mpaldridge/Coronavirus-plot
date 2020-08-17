Make your own coronavirus plot
================
[Matthew Aldridge](https://mpaldridge.github.io)

12 August 2020

Data collection
---------------

We can collect the data from [this Government Excel spreadsheet](https://assets.publishing.service.gov.uk/government/uploads/system/uploads/attachment_data/file/908755/COVID-19_Death_Series_20200812.xlsx). But I neatened it up a bit first in the file `new-cov.csv`.

``` r
govdata <- read.csv("https://raw.githubusercontent.com/mpaldridge/coronavirus-plot/master/new-cov.csv")
```
The reporting date is in fact a date.

``` r
govdata$Date <- as.Date(govdata$Date)
```

Moving average
--------------

The data for reported deaths shows a strong "weekend effect", so it makes sense to smooth out the data with a 7-day moving average.

``` r
MovAv <- function(x, n = 7) as.vector(filter(x, rep(1 / n, n), sides = 2))

govdata$No.restriction.av   <- MovAv(govdata$No.restriction)
govdata$X60.days.or.cert.av <- MovAv(govdata$X60.days.or.cert)
govdata$X28.days.av         <- MovAv(govdata$X28.days)
```

Plot a graph
------------

We can now plot a graph of the data.

``` r
plot(No.restriction.av ~ Date, data = govdata,
     main = "COVID-19 related deaths in England",
     ylim = c(5, 1200), log = "y",
     xlab = "Date", ylab = "Daily reported deaths (7-day average)",
     type = "l", col = "black", lwd = "2")
points(X60.days.or.cert.av ~ Date, data = govdata,
       type = "l", col = "blue", lwd = "2")
points(X28.days.av ~ Date, data = govdata,
       type = "l", col = "red", lwd = "2")
abline(h = 10^(0:4), col = "grey", lty = 3)
legend("topright",
       legend = c("No restriction", "60 days or death cert", "28 days"),
       col = c("black", "blue", "red"), lwd = 2)
```

![](/plot-1.png)
