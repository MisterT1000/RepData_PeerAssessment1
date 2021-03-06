# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data
1. Load the data (i.e. read.csv())
2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
unzip("activity.zip")
AMD <- read.csv(file = "activity.csv", colClasses=c("integer", "Date", "integer"), na.strings = "NA")
```

## What is mean total number of steps taken per day?
1. Calculate the total number of steps taken per day
2. If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day
3. Calculate and report the mean and median of the total number of steps taken per day


```r
library(ggplot2)
AMDagg <- aggregate(AMD$steps, by = list(AMD$date), FUN = sum)
hist(AMDagg$x)
```

![](PA1_template_files/figure-html/histSteps-1.png)<!-- -->


```r
summary(AMDagg)
```

```
##     Group.1                 x        
##  Min.   :2012-10-01   Min.   :   41  
##  1st Qu.:2012-10-16   1st Qu.: 8841  
##  Median :2012-10-31   Median :10765  
##  Mean   :2012-10-31   Mean   :10766  
##  3rd Qu.:2012-11-15   3rd Qu.:13294  
##  Max.   :2012-11-30   Max.   :21194  
##                       NA's   :8
```

## What is the average daily activity pattern?
1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
AMDavg <- aggregate(AMD$steps, by = list(AMD$interval), FUN = mean, na.rm = TRUE)
plot(AMDavg, type="l", ylab = "Average Steps By Interval", xlab = "All Days")
```

![](PA1_template_files/figure-html/avgAct-1.png)<!-- -->

## Imputing missing values
1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
3. Create a new dataset that is equal to the original dataset but with the missing data filled in.
4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
sum(is.na(AMD$steps))
```

```
## [1] 2304
```
The missing values are filled in with the mean for the 5-minute interval.

```r
AMD2 <- AMD
AMDavg2 <- rep(AMDavg$x,61)
AMD2$steps[is.na(AMD2$steps)] <- as.integer(AMDavg2)[is.na(AMD$steps)]
AMDagg2 <- aggregate(AMD2$steps, by = list(AMD2$date), FUN = sum)
hist(AMDagg2$x)
```

![](PA1_template_files/figure-html/missVal2-1.png)<!-- -->

## Are there differences in activity patterns between weekdays and weekends?
1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.
2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
AMD2$day <- weekdays(AMD2$date)
AMD2$Weekend <- ifelse(AMD2$day == "Saturday" | AMD2$day == "Sunday","Weekend", "Weekday")
AMD2avg <- aggregate(AMD2$steps, by = list(AMD2$interval, AMD2$Weekend), FUN = mean, na.rm = TRUE)
names(AMD2avg) <- c("interval","Weekend","steps")
qplot(interval, steps, data=AMD2avg, geom=c("line"))+facet_wrap(~Weekend, ncol=1)
```

![](PA1_template_files/figure-html/patterns-1.png)<!-- -->
