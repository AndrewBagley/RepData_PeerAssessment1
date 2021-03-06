# Reproducible Research: Peer Assessment 1

## Loading and preprocessing the data
######From Instructions:
######Show any code that is needed to
###### 1. Load the data (i.e. read.csv())


```r
activityData <- read.csv("activity.csv")
```
###### 2. Process/transform the data (if necessary) into a format suitable for your analysis
###### None needed

## What is mean total number of steps taken per day?
######For this part of the assignment, you can ignore the missing values in the dataset.
###### 1. Make a histogram of the total number of steps taken each day

- Creating dataset containing the total number of steps taken each day:

  
  ```r
  dailyStepSum <- aggregate(activityData$steps, list(activityData$date), sum)
  colnames(dailyStepSum) <- c("Date", "Steps")
  ```
  
- Creating histogram of the daily step data:
  
  ```r
  with(dailyStepSum, {
      par(oma=c(2,0,0,0), mar=c(6.75,6.75,3,0), mgp=c(5.75,0.75,0), las=2)
      barplot(
        height=Steps,
        main="Total Steps taken per Day",
        xlab="Dates",
        ylab="Steps per Day",
        names.arg=Date,
        space=c(0)
      )
  })
  ```
  
  ![](PA1_template_files/figure-html/unnamed-chunk-3-1.png) 
  
###### 2. Calculate and report the mean and median total number of steps taken per day
- Calculate the mean and median values (ignoring NA values):

  1. Mean
      
      ```r
      dailyStepMean <- mean(dailyStepSum$Steps, na.rm=TRUE)
      ```
      
      ```
      ## [1] 10766.19
      ```
  2. Median
      
      ```r
      dailyStepMedian <- median(dailyStepSum$Steps, na.rm=TRUE)
      ```
      
      ```
      ## [1] 10765
      ```

## What is the average daily activity pattern?
######From Instructions:
######What is the average daily activity pattern?
###### 1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)
  
  
  ```r
  intervalSteps <- aggregate(
      data=activityData,
      steps~interval,
      FUN=mean,
      na.action=na.omit
  )
  colnames(intervalSteps) <- c("Interval", "AvgStepsAvgAcrossDay")
  ```

  
  ```r
  with(intervalSteps, {
      plot(
        x=Interval,
        y=AvgStepsAvgAcrossDay,
        type="l",
        main="Time-Series of Average Steps against Interval",
        xlab="5-minute Interval",
        ylab="Average Steps, Average across all Days"
        
      )
  })
  ```
  
  ![](PA1_template_files/figure-html/unnamed-chunk-9-1.png) 

###### 2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
- Calculate Interval Max
  
  ```r
  intervalMax <- intervalSteps[intervalSteps$AvgStepsAvgAcrossDay==max(intervalSteps$AvgStepsAvgAcrossDay),]
  ```
- Interval Max:
  
  ```
  ##     Interval AvgStepsAvgAcrossDay
  ## 104      835             206.1698
  ```
- The interval between **835** and  **840** minutes has the maximum number of steps.


## Inputing missing values
######Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.
###### 1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)
- Total number of rows with NA values in original data.

  
  ```r
  countNA <- nrow(subset(activityData, is.na(activityData$steps)))
  ```
  
  ```
  ## [1] 2304
  ```

###### 2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.
- The average 5-minute interval values from the prevous section is used to replace the NA values of the original data and a new dataset will be generated.

- Decimal values will be rounded up to a whole number.
 
  
  ```r
  stepValues <- data.frame(activityData$steps)
  stepValues[is.na(stepValues),] <- ceiling(tapply(X=activityData$steps,INDEX=activityData$interval,FUN=mean,na.rm=TRUE))
  newData <- cbind(stepValues, activityData[,2:3])
  colnames(newData) <- c("Steps", "Date", "Interval")
  ```

###### 3. Create a new dataset that is equal to the original dataset but with the missing data filled in.
- The total number of steps taken each day is generated using this new dataset.

  
  ```r
  newDailyStepSum <- aggregate(newData$Steps, list(newData$Date), sum)
  colnames(newDailyStepSum) <- c("Date", "Steps")
  ```
   A portion of the new dataset is as follows:
  
  ```
  ##          Date Steps
  ## 1  2012-10-01 10909
  ## 2  2012-10-02   126
  ## 3  2012-10-03 11352
  ## 4  2012-10-04 12116
  ## 5  2012-10-05 13294
  ## 6  2012-10-06 15420
  ## 7  2012-10-07 11015
  ## 8  2012-10-08 10909
  ## 9  2012-10-09 12811
  ## 10 2012-10-10  9900
  ## 11 2012-10-11 10304
  ## 12 2012-10-12 17382
  ## 13 2012-10-13 12426
  ## 14 2012-10-14 15098
  ## 15 2012-10-15 10139
  ## 16 2012-10-16 15084
  ## 17 2012-10-17 13452
  ## 18 2012-10-18 10056
  ## 19 2012-10-19 11829
  ## 20 2012-10-20 10395
  ```

###### 4. Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?
- A histogram of the above data is created as a form of visual representation.

  
  ```r
  with(newDailyStepSum, {
      par(oma=c(2,0,0,0), mar=c(6.75,6.75,3,0), mgp=c(5.75,0.75,0), las=2)
      barplot(
        height=Steps,
        main="Graph of Total Steps taken per Day",
        xlab="Dates",
        ylab="Steps per Day",
        names.arg=Date,
        space=c(0)
      )
  })
  ```
  
  ![](PA1_template_files/figure-html/unnamed-chunk-17-1.png) 

- Calculate the mean and median values of this new dataset (NA values replaced with mean).

  1. Mean
      
      ```r
      newDailyStepMean <- mean(newDailyStepSum$Steps)
      ```
      
      ```
      ## [1] 10784.92
      ```
  2. Median
      
      ```r
      newDailyStepMedian <- median(newDailyStepSum$Steps)
      ```
      
      ```
      ## [1] 10909
      ```
      
- Calculating the mean for the NA values has caused both the mean and median values to increase.

  1. Mean:
  
      10766 to 10784
  2. Median:
  
      10765 to 10909


## Are there differences in activity patterns between weekdays and weekends?
######For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.
###### - Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.
###### - Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:

1.  Add a new column indicating whether the date is a weekday or a weekend dataset created in the previous section.

  
  ```r
  dateDayType <- data.frame(sapply(X=newData$Date, FUN=function(day) {
    if (weekdays(as.Date(day)) %in% c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday")) {
      day <- "weekday"
    }
    else {
      day <- "weekend"
    } 
  }))
  
  newDataWithDayType <- cbind(newData, dateDayType)
  colnames(newDataWithDayType) <- c("Steps", "Date", "Interval", "DayType")
  ```

2. Seperate the data into weekday or weekend and the mean (average) number of steps taken for each 5-minute interval.

  
  ```r
  dayTypeIntervalSteps <- aggregate(
      data=newDataWithDayType,
      Steps ~ DayType + Interval,
      FUN=mean
  )
  ```

3. Create a panel plot of both weekend and weekday graphs.

  
  ```r
  library("lattice")
  
  xyplot(
      type="l",
      data=dayTypeIntervalSteps,
      Steps ~ Interval | DayType,
      xlab="Interval",
      ylab="Number of steps",
      layout=c(1,2)
  )
  ```
  
  ![](PA1_template_files/figure-html/unnamed-chunk-24-1.png) 
