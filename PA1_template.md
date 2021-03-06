# Reproducible Research Project Work 1
```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE, warning = FALSE)
```

## 1. Code for reading in the dataset and/or processing the data
```{r}
activity_data <-  read.csv("activity.csv")
data_cl <- !is.na(activity_data$steps)
data_cl1 <- activity_data[data_cl,]
sum <- as.numeric(sapply(split(data_cl1$steps, data_cl1$date), sum))
date <- unique(data_cl1$date)
date1 <- as.Date(date , "%Y-%m-%d")
data_fin <- cbind.data.frame(date1 , sum)
names(data_fin) <- c("Date", "Steps")
library(dplyr)
```
## 2. Histogram of the total number of steps taken each day
```{r}
hist(data_fin$Steps, breaks = seq(0,25000, by = 2500), col = "lightblue", 
     main = "Total number of steps taken per day", xlab = "Total steps taken per day",
     ylim = c(0,20))
```

## 3. Mean and median number of steps taken each day
```{r}
summary(data_fin)
mean(data_fin$Steps)
median(data_fin$Steps)
```

## 4. Time series plot of the average number of steps taken
```{r}
mean <- as.numeric(sapply(split(data_cl1$steps, data_cl1$interval), mean))
interval_mean <- unique(data_cl1$interval)
data_fin <- cbind.data.frame(interval_mean , mean)
plot(interval_mean, mean, type="l", xlab="Interval", col="red",
     ylab="Number of Steps",main="Average Number of Steps per Day by Interval" )
```

## 5. The 5-minute interval that, on average, contains the maximum number of steps
```{r}
data_fin[which.max(data_fin$mean),1]
```

## 6. Code to describe and show a strategy for imputing missing data
### Strategy is to impute the missing values with th mean of the interval
```{r}
data_na <- is.na(activity_data$steps)
data_na1 <- activity_data[data_na,]
data_nonna <- !is.na(activity_data$steps)
data_nonna1 <- activity_data[data_nonna,]
mean <- as.numeric(sapply(split(data_nonna1$steps, data_cl1$interval), mean))
interval <- unique(data_nonna1$interval)
data_nonna1_grp <- cbind.data.frame(interval , mean)
merge <- left_join(data_na1,data_nonna1_grp, by="interval" )
merge1 <- merge[,2:4]
names(merge1) <- c("date", "interval", "steps")
fin_dataset <- rbind(data_nonna1,merge1)
```
## 7. Histogram of the total number of steps taken each day after missing values are imputed
```{r}
sum <- as.numeric(sapply(split(fin_dataset$steps, fin_dataset$date), sum))
date <- unique(fin_dataset$date)
date1 <- as.Date(date , "%Y-%m-%d")
data_fin <- cbind.data.frame(date1 , sum)
hist(data_fin$sum, breaks = seq(0,25000, by = 2500), col = "lightblue", 
     main = "Total number of steps taken per day", xlab = "Total steps taken per day",
     ylim = c(0,25))
```

## 8. Panel plot comparing the average number of steps taken per 5-minute interval across weekdays and weekends

```{r}
data_weekday <- subset(fin_dataset, weekdays(as.Date(date)) %in% 
                c("Monday" , "Tuesday", "Wednesday", "Thursday", "Friday")) 
data_weekend <-  subset(fin_dataset, weekdays(as.Date(date)) %in% 
                c("Saturday", "Sunday")) 

mean_weekday <- as.numeric(sapply(split(data_weekday$steps, data_weekday$interval), mean))
interval_mean_day <- unique(data_weekday$interval)
data_fin_weekday <- cbind.data.frame(interval_mean_day , mean_weekday)
mean_weekend <- as.numeric(sapply(split(data_weekend$steps, data_weekend$interval), mean))
interval_mean_end <- unique(data_weekend$interval)
data_fin_weekend <- cbind.data.frame(interval_mean_end , mean_weekend)
par(mfrow = c(2,1))
plot(data_fin_weekday$interval_mean, data_fin_weekday$mean_weekday, type="l", xlab="Interval", col="red",
     ylab="Number of Steps",main="Average Number of Steps per Day by Interval on Weekday" )

plot(data_fin_weekend$interval_mean_end, data_fin_weekend$mean_weekend, type="l", xlab="Interval", col="red",
     ylab="Number of Steps",main="Average Number of Steps per Day by Interval on Weekend" )
```

