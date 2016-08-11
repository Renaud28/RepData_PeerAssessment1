# Reproducible Research - Peer Graded Assignment: Course Project 1
Renaud  
11. elokuuta 2016  

This is the Markdown document addressing the Course Project 1 assignment of the Reproducible Research Course.

Read the activity file with read.csv()

```r
activity<-read.csv("C:/Users/Renaud/Rcourses/reproducibleresearch/week2/project/activity.csv")
```

Use aggregate() to aggregate by day and plot the histogram

```r
sum_steps<-aggregate(activity$steps, list(activity$date), sum)
hist(sum_steps$x, main="Total number of steps taken each day - Histogram", col="limegreen", xlab="")
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png)<!-- -->

Mean and median per day

```r
activity_clean<-na.omit(activity)
mean_steps<-aggregate(activity_clean$steps, list(activity_clean$date), mean)
mean_steps$Group.1<-as.Date(mean_steps$Group.1)
median_steps<-aggregate(activity_clean$steps, list(activity_clean$date), median)
median_steps$Group.1<-as.Date(median_steps$Group.1)

plot(mean_steps, type="l", col="limegreen", xlab="Date", ylab="Steps per day", main="Mean (green) and Median (yellow), steps per day")
lines(median_steps, type="l", col="gold")
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

Mean per five minutes interval

```r
mean_5min<-aggregate(activity_clean$steps, list(activity_clean$interval), mean)
colnames(mean_5min)<-c("interval","meansteps")
plot(mean_5min, col="limegreen", type="l", xlab="5 minutes interval", ylab="Mean steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

the 5 minutes interval with maximum number of steps is given with code below:

```r
subset(mean_5min, mean_5min$meansteps==max(mean_5min$meansteps))$interval
```

```
## [1] 835
```

missing values NA calculated with nrow()

```r
nrow(subset(activity, is.na(activity$steps)))
```

```
## [1] 2304
```

We use the mean for that 5minutes interval to fill in the values missing in orginal data set.
We plot the new data using same code as above just changing input data set

```r
activity_mod<-activity
for (i in 1:nrow(activity_mod))
{
        if (is.na(activity_mod$steps[i])){
                step<-subset(mean_5min, mean_5min$interval==activity[i,3])$meansteps
                activity_mod$steps[i]<-step
        }
}

sum_steps<-aggregate(activity_mod$steps, list(activity_mod$date), sum)
hist(sum_steps$x, main="Total number of steps taken each day - Histogram", col="limegreen", xlab="")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

```r
mean_steps<-aggregate(activity_mod$steps, list(activity_mod$date), mean)
mean_steps$Group.1<-as.Date(mean_steps$Group.1)
median_steps<-aggregate(activity_mod$steps, list(activity_mod$date), median)
median_steps$Group.1<-as.Date(median_steps$Group.1)

plot(mean_steps, type="l", col="limegreen", xlab="Date", ylab="Steps per day", main="Mean (green) and Median (yellow), steps per day")
lines(median_steps, type="l", col="gold")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-2.png)<!-- -->

Comparing those plots with earlier plots, it can be noted that inputing missing data (average of 5minutes interval) can have some impact on the result. Interestingly the average line of steps per day seems quite unchanged.



We now want to compare weekdays and weekends.
We first use function weekdays() to provide actual week day. (Note R is providing days in  Finnish language... "lauantai" and "sunnuntai" are respectively "saturday" and "sunday")

```r
day<-weekdays(as.Date(activity_mod$date))

for (i in 1:length(day)){
        if (day[i]=="lauantai" || day[i]=="sunnuntai")
                day[i]<-"weekend"
        else
                day[i]<-"weekday"
}

activity_mod<-cbind(activity_mod, day)
weekday<-subset(activity_mod, activity_mod$day=="weekday")
weekend<-subset(activity_mod, activity_mod$day=="weekend")

mean_5min_wd<-aggregate(weekday$steps, list(weekday$interval), mean)
colnames(mean_5min_wd)<-c("interval","meansteps")

mean_5min_we<-aggregate(weekend$steps, list(weekend$interval), mean)
colnames(mean_5min_we)<-c("interval","meansteps")

plot(mean_5min_wd, col="limegreen", type="l", xlab="5 minutes interval", ylab="Mean steps weedays")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

```r
plot(mean_5min_we, col="limegreen", type="l", xlab="5 minutes interval", ylab="Mean steps weekend")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-2.png)<!-- -->



