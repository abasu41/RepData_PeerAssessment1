# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
The data file named activity.csv must be in the current directory. Use
the data.table and ggplot2 packages to process and plot the data. The date column is read as a factor and must be converted to a date

```r
library('data.table')
library('ggplot2')
```

```r
df <- read.csv('activity.csv', na.strings = 'NA')
dt <- data.table(df)
dt$date <- as.POSIXct(dt$date, format='%Y-%m-%d')
```

## What is mean total number of steps taken per day?
To draw the histogram for the total steps taken each day, using a by function on the data table.

```r
dtSumByDate <- dt[, list(sumSteps=sum(steps)), by=date]
hist(dtSumByDate$sumSteps, main='Histogram of Total Number of Steps for each day', xlab='Total Steps')
```

![](./PA1_template_files/figure-html/summarize-data-1.png) 

```r
mn <- mean(dtSumByDate$sumSteps, na.rm=TRUE)
md <- median(dtSumByDate$sumSteps, na.rm=TRUE)
```
The mean is 10766.18868 and the median is 10765.00000. 

## What is the average daily activity pattern?
First we average the steps across all days for each interval to produce a dataset with interval, averageSteps

```r
dtAvgByInt <- dt[, list(avgSteps=mean(steps, na.rm=TRUE)), by=interval]
x <- seq(0, 2355, 25)
plot(dtAvgByInt$interval,dtAvgByInt$avgSteps,  type='l', xlab='Interval', ylab='Average Steps', xaxt='n')
axis(1, at=x, cex.lab=1)
```

![](./PA1_template_files/figure-html/average-daily-1.png) 

```r
maxInterval <- dtAvgByInt[which.max(dtAvgByInt$avgSteps),]$interval
maxSteps <- dtAvgByInt[which.max(dtAvgByInt$avgSteps),]$avgSteps
```
The maximum average steps of 206.1698113 is found at interval 835.

## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
