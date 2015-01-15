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
1. The total number of missing values is calculated by adding missing values for each column (even though only the steps column has any missing values, we use the other columns just to be sure)

```r
missingValues <- sum(is.na(df$steps)) + sum(is.na(df$date)) + sum(is.na(df$interval))
```
The total number of missing values in the dataset is 2304


2. To fill the missing values, we just use the mean of that interval. Earlier we had calculated the mean of each interval. We use the values from that data frame to populate any missing values. For example, if the interval 0 is missing a steps value, we look up the value of the mean from the dtAvgByInt table for interval 0 and replace the NA with that value.

3. The new dataset is created using the following code:

```r
df2 <- data.table(df)
for ( i in 1:nrow(df2)) {
    if (is.na(df2[i, ][,steps])) {
      inter = df2[i,]$interval
      df2[i,]$steps = as.integer(dtAvgByInt[interval==inter]$avgSteps)
    }
}
```
We then calculate the new histogram of the data set using the same steps as in the first question

```r
dt2SumByDate <- df2[, list(sumSteps=sum(steps)), by=date]
hist(dt2SumByDate$sumSteps, main='Histogram of Total Number of Steps for each day (No NAs)', xlab='Total Steps')
```

![](./PA1_template_files/figure-html/summarize-without-na-1.png) 

```r
mn2 <- mean(dt2SumByDate$sumSteps, na.rm=TRUE)
md2 <- median(dt2SumByDate$sumSteps, na.rm=TRUE)
```
The new mean after replacing the NA with a daily average for that interval is 10749.77049 and the median is 10641.00000.
As can be seen by comparing the values to the first answer, the overall shape of the histogram remains the same and there is only a very small shift in the value of the new mean towards the left of the original mean. 
## Are there differences in activity patterns between weekdays and weekends?
