---
title: "PA1_template"
output: html_document
---

###Introduction

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a Fitbit, Nike Fuelband, or Jawbone Up. These type of devices are part of the "quantified self" movement -- a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

###Loading the data
```{r}
    #Reads the data
    data = read.csv('activity.csv')
    data$date = as.Date(data$date, "%Y-%m-%d")
    #Splits the data per date
    splitDay = split(data, data$date)
```

### total number of steps per day

```{r}
    #Total number of steps/Median per day
    totalSteps = sapply(splitDay, function(x) sum(x$steps))
    medianSteps = sapply(splitDay, function(x) median(x$steps))
    hist(totalSteps,main="Histogram of Frequency of TotalSteps/Day")
```  
  
Total number of steps/day
```{r, echo=FALSE}
    totalSteps

```
  
Median number of steps/day
```{r, echo=FALSE}

    medianSteps
```
  
###Avg daily activity pattern
```{r}
    #Preallocate vector
    interval = seq(0,2355,5)
    steps = numeric()
    for(i in 1:472) {
        steps[i] = 0
    }
    avgSteps = data.frame(interval, steps)
    names(avgSteps) = c('interval','avgsteps')
    
    #Adds the mean data in for the 5 minute interval
    for (i in 1:472){
        avgSteps$avgsteps[i] = mean(data$steps[data$interval == interval[i]], na.rm=TRUE)
    }
    
    #Plots the avgsteps vs time interval
    plot(avgSteps, type='l', main='Avg. Steps over 5 minute intervals',xlab='Interval(minutes)',
         ylab='Average Steps')
    
    #Finds the interval with the highest avg
    avgSteps$interval[which.max(avgSteps$avgsteps)]
```
  

##Inputing missing values

```{r}
    #Counts number of na values in steps
    numNa = sum(is.na(data$steps))
```
  

```{r}
    #Replaces NA with 0
    noNa = data
    noNa[is.na(noNa)]=0
    #Splits the data per date
    splitNa = split(noNa, noNa$date)
    #Average steps/median
    totalNa = sapply(splitNa, function(x) sum(x$steps))
    #Plots histogram of the number of total steps per day
    hist(totalNa,main="Histogram of Frequency of TotalSteps/Day", xlab="Total number of Steps")
    #Average/mean across all values
    avgNa = sapply(splitNa, function(x) mean(x$steps))
    medNa = sapply(splitNa, function(x) median(x$steps))
```
  
```{r, echo=FALSE}
    avgNa
```
  
```{r, echo=FALSE}
    medNa
```

### differences in activity patterns 
```{r}
    #Shows data of weekday and weekend values
    weekData = data
    weekData[is.na(weekData)]=0    
    weekData$days = weekdays(weekData$date)
    weekData$days = as.factor(ifelse(weekdays(weekData$date) %in% c("Saturday","Sunday"), 
                                      "Weekend", "Weekday")) 

    avgEnd = mean(weekData$steps[weekData$days == 'Weekend'])
    avgDay = mean(weekData$steps[weekData$days == 'Weekday'])

    #Subset weekday and weekend values
    weekdays = subset(weekData, weekData$days == 'Weekday')
    weekends = subset(weekData, weekData$days == 'Weekend')
    
    #Preallocate vector
    weekend = numeric()
    weekday = numeric()
    for(i in 1:472) {
        weekday[i] = 0
        weekend[i] = 0
    }
    avgWeek = data.frame(interval, weekend, weekday)
    names(avgWeek) = c('interval','avgsteps.weekend','avgsteps.weekday')
    
    #Adds the mean data in for the 5 minute interval
    for (i in 1:472){
        avgWeek$avgsteps.weekday[i] = mean(weekdays$steps[weekdays$interval == interval[i]], na.rm=TRUE)
        avgWeek$avgsteps.weekend[i] = mean(weekends$steps[weekends$interval == interval[i]], na.rm=TRUE)
    }
```
```{r}
    #Creates the plots
    par(mfrow=c(2,1))
    plot(avgWeek$interval, avgWeek$avgsteps.weekend, type='l', main ="Weekend vs Avg Steps",
         xlab="Interval(steps)", ylab="Average Steps")
    plot(avgWeek$interval, avgWeek$avgsteps.weekday, main ="Weekday vs Avg Steps", 
         xlab="Interval(steps)", ylab="Average Steps", type='l')
```


