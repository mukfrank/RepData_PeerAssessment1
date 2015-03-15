## Introduction

It is now possible to collect a large amount of data about personal
movement using activity monitoring devices such as a
[Fitbit](http://www.fitbit.com), [Nike
Fuelband](http://www.nike.com/us/en_us/c/nikeplus-fuelband), or
[Jawbone Up](https://jawbone.com/up). These type of devices are part of
the "quantified self" movement -- a group of enthusiasts who take
measurements about themselves regularly to improve their health, to
find patterns in their behavior, or because they are tech geeks. But
these data remain under-utilized both because the raw data are hard to
obtain and there is a lack of statistical methods and software for
processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring
device. This device collects data at 5 minute intervals through out the
day. The data consists of two months of data from an anonymous
individual collected during the months of October and November, 2012
and include the number of steps taken in 5 minute intervals each day.

## Data

The data for this assignment can be downloaded from the course web
site:

* Dataset: [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) [52K]

The variables included in this dataset are:

* **steps**: Number of steps taking in a 5-minute interval (missing
    values are coded as `NA`)

* **date**: The date on which the measurement was taken in YYYY-MM-DD
    format

* **interval**: Identifier for the 5-minute interval in which
    measurement was taken




The dataset is stored in a comma-separated-value (CSV) file and there
are a total of 17,568 observations in this
dataset.


## Assignment





eproducible Research: Peer Assessment 1

Load useful libraries.
library (plyr)
library (lattice)

Loading and preprocessing the data
Assignment data set is included in the Git repository, forked from https://github.com/rdpeng/RepData_PeerAssessment1, in the file activity.zip. In my repository I’ve manually unpacked that to activity.csv. Load it:
acts <- read.csv ('activity.csv')
The result is usable as is.
What is mean total number of steps taken per day?
Plyr is used to conveniently to add up the steps taken in each day.
spd <- ddply (acts,
              .(date), 
              summarize, 
              stepSum=sum (steps, na.rm=TRUE))
To display the result.
hist (spd$stepSum, breaks=13)

The subject took a mean of 9354.2295082 steps and median of 10395 steps per day.
What is the average daily activity pattern?
To get this result Plyr applied as below: .
spi <- ddply (acts,
              .(interval), 
              summarize, 
              stepMean = mean(steps, na.rm=TRUE))
The result is displayed below:
plot (spi$interval, spi$stepMean, type='l')
The result indicates that the busiest interval of the subject’s day, on average was 835.
Imputing missing values
which <- is.na (acts$steps)
The data set comprises many missing step count values: a total of 2304, or 13 percent of the total.
A two-stage strategy is used for imputing missing values to the data. First stage, an NA at interval i is replaced with the mean number of steps in that interval over the rest of the data set. Unfortunately, many intervals are NA over the entire series; a grand mean is imputed over all non-NA data.
fixie <- acts
fixie$steps[which] <-
  spi$stepMean[spi$interval == fixie$interval[which]]
fixie$steps[is.na (fixie$steps)] <- 
  mean (acts$steps, na.rm=TRUE)
Fixed! Did it change anything? 
To establish whether there is any change we look at the new histogram
fixieSPD <- ddply (fixie,
                   .(date), 
                   summarize, 
                   stepSum=sum (steps))
hist (fixieSPD$stepSum,
      breaks=13)
The outliers at the low end have disappeared. 
Are there differences in activity patterns between weekdays and weekends?
To establish this we, need to distinguish weekdays from weekends.
wd <- weekdays (as.Date (fixie$date))
fixie$dayTypes <- factor (wd,
                          levels <- c ('weekday', 'weekend'))
fixie$dayTypes[] <- 'weekday'
fixie$dayTypes[wd %in% c ('Saturday', 'Sunday')] <- 'weekend'
Next we compute summaries and plot comparing the  two:
spi <- ddply (fixie,
              .(interval, dayTypes), 
              summarize, 
              stepMean = mean(steps, na.rm=TRUE))
xyplot (stepMean ~ interval | dayTypes, 
        data=spi,
        type='l',
        layout=c (1, 2))

 In Conclusion
The resulting plots show a difference between weekend and weekday activity patterns. On weekdays, the subject’s average level rises abruptly just before start of day. Activity levels then became moderate and later fall.

On the other hand, the weekday pattern is different. The general level of activity on a weekday is lower than for the same period of time on a weekend. 


