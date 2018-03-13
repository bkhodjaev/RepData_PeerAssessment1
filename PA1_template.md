Reproducible research course. Project 1
========================================
# Loading and preprocessing the data
### 1. Load the data

```r
data <- read.csv("activity.csv", header = TRUE, sep = ",", dec = ".",colClasses = c("numeric", "Date", "integer"))
```
### 2. Process/transform the data (if necessary) into a format suitable for your analysis

## *What is mean total number of steps taken per day?*
### 1. Calculate the total number of steps taken per day

```r
steps_daily <- tapply(data$steps, data$date, FUN = sum, na.rm=TRUE)
```
### 2. Make a histogram of the total number of steps taken each day

```r
hist(steps_daily, col = 3, ylab = "Frequency", xlab = "Steps each day", main = "Total number of steps per day", freq = TRUE, breaks = 20)
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3-1.png)
### 3. Calculate and report the mean and median of the total number of steps taken per day

```r
steps_daily_mean <- mean(steps_daily, na.rm = TRUE)
steps_daily_median <- median(steps_daily, na.rm = TRUE)
steps_daily_mean
```

```
## [1] 9354.23
```

```r
steps_daily_median
```

```
## [1] 10395
```
## What is the average daily activity pattern?
### 1. Make a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
steps_interval <- tapply(data$steps, data$interval, mean, na.rm=TRUE)
plot(names(steps_interval), steps_interval, type = "l", xlab = "5 min intervals", ylab = "Average number of steps", main = "Daily activity")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png)
### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
names(which.max(steps_interval))
```

```
## [1] "835"
```
## Imputing missing values
### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with 𝙽𝙰s)

```r
missing_values <- sum(is.na(data$steps))
missing_values
```

```
## [1] 2304
```
### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in

```r
library(zoo)
data_new <- data
data_new$steps <- ave(data_new$steps, data_new$interval, FUN=na.aggregate)
```
### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
steps_daily_new <- tapply(data_new$steps, data_new$date, FUN = sum, na.rm=TRUE)
hist(steps_daily_new, col = 3, ylab = "Frequency", xlab = "Steps each day", main = "Total number of steps per day", freq = TRUE, breaks = 20)
```

![plot of chunk unnamed-chunk-9](figure/unnamed-chunk-9-1.png)

```r
steps_daily_mean_new <- mean(steps_daily_new, na.rm = TRUE)
steps_daily_median_new <- median(steps_daily_new, na.rm = TRUE)
steps_daily_mean_new
```

```
## [1] 10766.19
```

```r
steps_daily_median_new
```

```
## [1] 10766.19
```
## Are there differences in activity patterns between weekdays and weekends?
### 1. Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
data_new$day <- ifelse(weekdays(data_new$date) == "Saturday" | weekdays(data_new$date) == "Sunday", "weekend", "weekday")
```
### 2. Make a panel plot containing a time series plot (i.e. 𝚝𝚢𝚙𝚎 = "𝚕") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
library(ggplot2)
data_new_day <- aggregate(steps ~ interval + day, data=data_new, mean)
ggplot(data_new_day, aes(interval, steps))+geom_line()+facet_grid(day ~ .)+xlab("5 min intervals")+ylab("Average number of steps")
```

![plot of chunk unnamed-chunk-11](figure/unnamed-chunk-11-1.png)
