# Reproducible Research: Peer Assessment 1


## Loading and preprocessing the data

Show any code that is needed to

1. Load the data (i.e. read.csv())

2. Process/transform the data (if necessary) into a format suitable for your analysis


```r
  library(ggplot2)
```

```
## Warning: package 'ggplot2' was built under R version 3.0.3
```

```r
  setwd("C:/Users/Yuewei/Documents/R/data/RepData_PeerAssessment1")
  data <- read.csv("activity.csv", na.string = "NA")
  data$date <- as.Date(data$date)
```

## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Make a histogram of the total number of steps taken each day

2. Calculate and report the mean and median total number of steps taken per day


```r
  data1 <- na.omit(data)
  sum_per_day <- tapply(data1$steps, data1$date, sum)
  hist(sum_per_day, breaks = 10, main = "Total Number of Steps Taken Each Day", xlab = "Number of   Steps", ylab = "Counts")
```

![](PA1_template_files/figure-html/sum_date-1.png) 

```r
  mean(sum_per_day)
```

```
## [1] 10766.19
```

```r
  median(sum_per_day)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
  average_per_interval <- tapply(data1$steps, data1$interval, mean)
  plot(names(average_per_interval), average_per_interval, type = "l", main = "Avarage Number of Steps Taken Per 5-Minute Interval", xlab = "Interval", ylab = "Number of Steps")
```

![](PA1_template_files/figure-html/average_interval-1.png) 

```r
  which.max(average_per_interval)
```

```
## 835 
## 104
```

## Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

3. Create a new dataset that is equal to the original dataset but with the missing data filled in.

4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
  length(which(is.na(data)))
```

```
## [1] 2304
```

```r
  data_new <- data
  data_new$steps[is.na(data_new$steps)] <- average_per_interval[as.character(data_new$interval)]
```

```
## Warning in data_new$steps[is.na(data_new$steps)] <-
## average_per_interval[as.character(data_new$interval)]: number of items to
## replace is not a multiple of replacement length
```

```r
  new_sum_per_day <- tapply(data_new$steps, data_new$date, sum)
  hist(new_sum_per_day, breaks = 10, main = "Total Number of Steps Taken Each Day with Missing Data Filled", xlab = "Number of   Steps", ylab = "Counts")
```

![](PA1_template_files/figure-html/missing_value-1.png) 

```r
  mean(new_sum_per_day)
```

```
## [1] 10766.19
```

```r
  median(new_sum_per_day)
```

```
## [1] 10766.19
```

Conclusion: The values from the new dataset with missing values filled in are similar to the estimates from the first part of the assignment. 

## Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
  data_new$day[weekdays(data_new$date) %in% c("Saturday", "Sunday")] <- "weekend"
  data_new$day[!weekdays(data_new$date) %in% c("Saturday", "Sunday")] <- "weekday"
  average_interval_weekday <- aggregate(data_new$steps, by=list(data_new$interval, data_new$day), FUN=mean)
  names(average_interval_weekday) <- c("interval", "day", "steps")
  g <- ggplot(average_interval_weekday, aes(interval, steps))
  g + geom_line(color="blue") + facet_grid(day ~.) + labs(title="Avarage Number of Steps Taken Per 5-Minute Interval") + labs(x="Interval", y="Number of Steps")
```

![](PA1_template_files/figure-html/weekday_vs_weekend-1.png) 
