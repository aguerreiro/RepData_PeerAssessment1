PeerAssessment1 - Activity Monitoring
========================================================

- Loading and preprocessing the data


```r
echo = TRUE
# load library
pkgTest <- function(x) {
    if (!require(x, character.only = TRUE)) {
        install.packages(x, dep = TRUE)
        if (!require(x, character.only = TRUE)) {
            stop("Package not found")
        } else {
            library(x)
        }
    }
}
pkgTest("data.table")
```

```
## Loading required package: data.table
```

```r
pkgTest("timeDate")
```

```
## Loading required package: timeDate
```

```r
pkgTest("date")
```

```
## Loading required package: date
```

```r
# read file to dataframe
f <- read.csv("activity.csv", colClasses = "character", stringsAsFactors = FALSE)
f[[2]] <- strftime(f[[2]], "%Y-%m-%d", tz = "", usetz = FALSE)
# convert Character to date required library(date)
f$date <- as.date(f$date, order = "ymd")
f$steps <- as.integer(f$steps)
# ignore the missing values in the dataset
f1 <- subset(f, !is.na(f[, 1]), select = c(1, 2))
# load data to datatable required library(data.table)
DT1 <- as.data.table(f1)
# index on date
setkey(DT1, "date")
grp = 0L
DT1[, `:=`(id, grp <- grp + 1L), by = key(DT1)]  # enumerate number of days
```

```
##        steps  date id
##     1:     0 19268  1
##     2:     0 19268  1
##     3:     0 19268  1
##     4:     0 19268  1
##     5:     0 19268  1
##    ---               
## 15260:     0 19326 53
## 15261:     0 19326 53
## 15262:     0 19326 53
## 15263:     0 19326 53
## 15264:     0 19326 53
```

```r
# index on id
setkey(DT1, "id")
sbyday <- as.data.frame(DT1[, sum(steps), by = id])
colnames(sbyday)[2] <- "TotalSteps"
hist(sbyday$TotalSteps, main = "Steps", xlab = "Steps by day")
abline(v = mean(sbyday$TotalSteps), col = "red", lwd = 4)
abline(v = median(sbyday$TotalSteps), col = "yellow", lwd = 2)
legend("topright", pch = "-", col = c("red", "yellow"), legend = c("mean", "median"))
```

![plot of chunk unnamed-chunk-1](figure/unnamed-chunk-11.png) 

```r
f$interval <- as.integer(f$interval)
f2 <- subset(f, !is.na(f[, 1]), select = c(1, 3))
DT2 <- as.data.table(f2)
setkey(DT2, "interval")
meanbyi <- DT2[, mean(steps), by = interval]
plot(meanbyi, type = "l")
```

![plot of chunk unnamed-chunk-1](figure/unnamed-chunk-12.png) 

```r
dev.off()
```

```
## null device 
##           1
```

```r
setkey(meanbyi, "V1")
# interval for max mean steps
meanbyi[, interval[[which.max(V1)]]]
```

```
## [1] 835
```

```r
countna <- sum(is.na(f$steps))
# count NA
countna
```

```
## [1] 2304
```

```r
setkey(meanbyi, "interval")
meanbyi$V1 <- as.integer(meanbyi$V1)
```

