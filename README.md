Skip to content
Search or jump to…
Pull requests
Issues
Marketplace
Explore
 
@DHighchief 
DHighchief
/
My-first-R-project-
Public
Code
Issues
Pull requests
Actions
Projects
Wiki
Security
Insights
Settings
My-first-R-project-/Project.Rmd
@DHighchief
DHighchief First commit
Latest commit f122c04 on Jun 22
 History
 1 contributor
439 lines (332 sloc)  14.3 KB

---
title: "Capstone Project Case study"
author: "By: Victor Chidimma Okafor"
date: '2022-06-16'
output: html_document
---

![Bellabeat logo](C:\Users\user\Downloads\bellabeat logo.jpg)

## About the Company
Urška Sršen and Sando Mur founded Bellabeat, a high-tech company that manufactures health-focused smart products.
Sršen used her background as an artist to develop beautifully designed technology that informs and inspires women around
the world. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower women with
knowledge about their own health and habits. Since it was founded in 2013, Bellabeat has grown rapidly and quickly
positioned itself as a tech-driven wellness company for women. More on [the website.](bellabeat.com)

#### Business Task
* Analyze trends of smart device usage.
* Provide high-level content recommendation on marketing strategy for a Bellabeat product. 

#### About the data
FitBit Fitness Tracker Data (CC0: Public Domain, dataset made available through Mobius): This Kaggle data set
contains personal fitness tracker from thirty fitbit users. Thirty eligible Fitbit users consented to the submission of
personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. It includes
information about daily activity, steps, and heart rate that can be used to explore users’ habits.

#### Key stakeholders

**Primary Stakehoders:**

* Urška Sršen: Bellabeat’s cofounder and Chief Creative Officer.
* Sando Mur: Mathematician and Bellabeat’s cofounder; key member of the Bellabeat executive team.

**Secondary Stakeholders:**

* Bellabeat marketing analytics team.


### Loading packages
```{r echo=FALSE}
library(tidyr)
library(readr)
library(ggplot2)
library(skimr)
library(janitor)
library(lubridate)
library(dplyr)
```


### Importing datasets
```{r show_col_types=FALSE, echo=FALSE}
dailyActivity_merged <- read_csv("C:/Users/user/Downloads/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")
dailyCalories_merged <- read_csv("C:/Users/user/Downloads/Fitabase Data 4.12.16-5.12.16/dailyCalories_merged.csv")
dailyIntensities_merged <- read_csv("C:/Users/user/Downloads/Fitabase Data 4.12.16-5.12.16/dailyIntensities_merged.csv")
dailySteps_merged <- read_csv("C:/Users/user/Downloads/Fitabase Data 4.12.16-5.12.16/dailySteps_merged.csv")
sleepDay_merged <- read_csv("C:/Users/user/Downloads/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")
library(readr)
hourlyIntensities_merged <- read_csv("C:/Users/user/Pictures/Fitabase Data 4.12.16-5.12.16/hourlyIntensities_merged.csv")
```


### Assigning new names to datasets
```{r}
dailyActivity<- (dailyActivity_merged)
dailyCalories<- (dailyCalories_merged)
dailyIntensities<- (dailyIntensities_merged)
dailySteps<- (dailySteps_merged)
dailySleep<- (sleepDay_merged)
intensities<- (hourlyIntensities_merged)
```


## Data Cleaning
I will  start the initial process of data cleaning by finding the number of distinct customer Ids in the data set. This will help me check if it (number of Ids) corresponds with the information i have about the data set.  

```{r}
n_distinct(dailyActivity$Id)
```

```{r}
n_distinct(dailyCalories$Id)
```

```{r}
n_distinct(dailyIntensities$Id)
```

```{r}
n_distinct(dailySleep$Id)
```

```{r}
n_distinct(dailySteps$Id)
```

```{r}
n_distinct(intensities$Id)
```

##### Result
This shows that dailyActivity, dailyCalories, dailyIntensities, intensities and dailySteps 
all have 33 distinct IDs. while dailySleep has 24 disntinct IDs.

#### Checking for duplicates

```{r}
sum(duplicated(dailyActivity))
sum(duplicated(dailyCalories))
sum(duplicated(dailyIntensities))
sum(duplicated(intensities))
sum(duplicated(dailySteps))
sum(duplicated(dailySleep))
sum(duplicated(intensities))
```

```{r}
dailySleep<- dailySleep %>% 
  distinct() %>% 
  drop_na()
```

To verify that duplicates has been removed
```{r}
sum(duplicated(dailySleep))
```

#### Catch a view of the cleaned data sets

```{r}
head(dailyActivity)
```

```{r}
head(dailyCalories)
```

```{r}
head(dailyIntensities)
```

```{r}
head(dailySleep)
```

```{r}
head(dailySteps)
```

```{r}
head(intensities)
```



#### Formating

```{r warning=FALSE}
dailyActivity <- dailyActivity %>%
  rename(date = ActivityDate) %>% 
  mutate(date = as_date(date, format = "%m/%d/%Y"))
dailySleep <- dailySleep %>%
  rename(date = SleepDay) %>% 
  mutate(date = as_date(date, format ="%m/%d/%Y %I:%M:%S %p" , tz=Sys.timezone()))
```


### Analysis and Visualization

We will first start by writing a  code to gain high level information in the data.  
```{r summary of dailyActivity}
dailyActivity %>%  
  select(TotalSteps,
         TotalDistance,
         SedentaryMinutes, Calories,
         VeryActiveMinutes,
         LightlyActiveMinutes) %>%
  summary()
```


```{r summary of dailyCalories}
dailyCalories %>% 
  select(Calories) %>% 
  summary()
```

```{r dailyIntensities}
dailyIntensities %>% 
  select(VeryActiveMinutes,
         SedentaryMinutes,
         VeryActiveDistance) %>% 
  summary()
```

```{r dailySteps}
dailySteps %>% 
  select(StepTotal) %>% 
  summary()
```

```{r dailySleep}
dailySleep %>% 
  select(TotalTimeInBed,
         TotalMinutesAsleep,
         TotalSleepRecords) %>% 
  summary()
```
#### Result
This summary of  four data sets (dailyActivties, dailyIntensities, dailyCalories 
and dailySteps) show a lot of correspondence in terms of average, maximum and 
minimum input of selected variables . The only data set with marked difference 
is the dailySleep data set which does not have corresponding variables except
for date and Id.

#### Observations
The average total steps taken by the participants is 7,638.
The average total distance traveled  by the participants is 5.590m.
The average sedentary minutes by the participants is 991.2mins (16 hrs), 68% of
the day.
The average very active minutes by the participants is 21.16 (1.4% of the day).
The average total minutes spent asleep by the participants is 419.5mins(6.9hrs)
The average total number of sleep time per day is 1 (one sleep per day)

#### Summary
The participants were largely inactive. Which could mean that they are either 
really aged or white collar workers, but a visualization could help us prove or
disprove this theory.

**Merging datasets**

we will inner join the dailyActivity and  dailySleep data frames. The combination 
of both tables contain a lot information which will be easy to access from the new table.
```{r}
dailyActivitySleep <- merge(dailyActivity, dailySleep, by=c("Id", "date"))
glimpse(dailyActivitySleep)
head(dailyActivitySleep)
```


```{r}
dailyAverage <- dailyActivitySleep %>% 
  group_by(Id) %>% 
  summarise(mean_daily_steps=mean(TotalSteps), mean_daily_calories=mean(Calories), mean_daily_sleep=mean(TotalMinutesAsleep))
head(dailyAverage)
```

```{r}
userType<- dailyAverage %>% 
  mutate(userActivity=case_when(
    mean_daily_steps<5000 ~ "Sedentary",
    mean_daily_steps>=5000 & mean_daily_steps < 7500 ~ "Lightly Active",
    mean_daily_steps>=7501 & mean_daily_steps < 10000 ~ "Moderately Active",
    mean_daily_steps>=10001 ~ "Very Active"
    ))
head(userType)
```

We will add a new column to our table. The new column will show the percentage of participants by the level of activity which will help us understand the data better on a visualization.

```{r}
userTypePercent<- userType %>% 
  group_by(userActivity) %>% 
   summarise(total = n()) %>%
  mutate(totals = sum(total)) %>%
  group_by(userActivity) %>%
  summarise(percentTotal = total / totals) %>%
  mutate(percentage = scales::percent(percentTotal))
head(userTypePercent) %>% arrange(desc(percentage))
  
```

## Visualizations

```{r}
userTypePercent %>% 
  ggplot(aes(x="", y=percentage, fill=userActivity))+
  geom_bar(stat = "identity", width = 1)+
  coord_polar(theta = "y",  start=0)+
  theme_minimal()+
  theme(axis.title.x = element_blank(),
        axis.title.y = element_blank(),
        panel.border = element_blank(),
        panel.grid = element_blank(),
        axis.ticks = element_blank(),
        axis.text.x = element_blank(),
        plot.title = element_text(hjust = 0.5, size=14, face = "bold"))+
  scale_fill_manual(values = c("dark blue", "blue", "grey", "light blue"))+
  geom_text(aes(label = percentage), 
            position = position_stack(vjust = 0.5))+
  labs(title = "User type distribution", subtitle = "This chart shows the distribution of the participants by how active they are." )
```

#### Observation
The chart above shows the activity of participants. Only 21% of the participants 
of this evaluation are very active.
The other activity levels ranging from sedentary to moderately active take up the
other 79% of the users. 
```{r}
ggplot(data = dailyActivity)+
  geom_point(mapping = aes(x=TotalSteps, y=TotalDistance, color ="orange"))+
  geom_smooth(mapping = aes(x=TotalSteps, y=TotalDistance))+
  labs(title = "Total Steps vs Total Distance", subtitle = "This visualization captures the relationship between the number of steps taken and the distance covered.", x="Total Steps", y="Total Distance")
```


#### Observation
It can be observed from the above graph that there is a positive relationship
between steps and distance.



```{r}
ggplot(data = dailyActivity)+
  geom_point(mapping = aes(x=TotalSteps, y=SedentaryMinutes, color='orange'))+
  geom_smooth(mapping = aes(x=TotalSteps, y=SedentaryMinutes))+
  labs(title='Sedentary Minutes vs Steps', subtitle = "Sample of visualization showing relationship between Sedentary minutes and Steps.", x="Total Steps", y="Sedentary Minutes")
```

### Observation
The graph shows an inverse relationship between the number of steps taken and 
the sedentary minutes. This actively explains that participants that are sedentary
are considered inactive.


```{r}
ggplot(data = dailyActivity)+
  geom_point(mapping = aes(x=TotalSteps, y=VeryActiveMinutes, color="orange"))+
   geom_smooth(mapping = aes(x=TotalSteps, y=VeryActiveMinutes))+
  labs(title = "Steps vs Very Active minutes", subtitle = "This visualization captures the relationship between steps taken and Very active minutes.", x="Total Steps", y="Very Active Minutes")
```


#### Obesrvation
The visualization above shows a direct relationship between movement and activity.
The visualization suggests that a moving person is an active person.
The next visualization will help explain what this visualization indicates better.

```{r warning=FALSE}
ggplot(data = dailyActivity)+
  geom_point(mapping = aes(x=TotalSteps, y=Calories, color="Orange"))+
  geom_smooth(mapping = aes(x=TotalSteps, y=Calories))+
  labs(title = "Total Steps vs Calories", subtitle = "This graph captures the relationship between steps and calories.", x="Total Steps", Y="Calories")
```

#### Observation
The relationship between steps and calories burned is also positive.This explains 
that participants that who move around more frequently have an higher chance
of burning calories which in turn keeps them healthy.

```{r warning=FALSE}
ggplot(data = dailySleep)+
  geom_point(mapping = aes(x=TotalMinutesAsleep, y=TotalTimeInBed, color = "Orange"))+
  geom_smooth(mapping = aes(x=TotalMinutesAsleep, y=TotalTimeInBed,))+
  labs(title = "Total minutes Sleeping vs Total Time in Bed", subtitle = "The relationship between total minutes spent sleeping and the total time in bed.", x="Total Minutes Asleep", y="Total Time in Bed")
```


#### Observation
This graph shows a positive relationship between total time in bed and 
total minutes asleep. This is an indication that participants bed times are 
usually spent sleeping mostly.
```{r}
 intensities<- hourlyIntensities_merged
 
 intensities$ActivityHour=as.POSIXct(intensities$ActivityHour, format="%m/%d/%Y %I:%M:%S %p", tz=Sys.timezone())
 intensities$time <- format(intensities$ActivityHour, format = "%H:%M:%S")
 intensities$date <- format(intensities$ActivityHour, format = "%m/%d/%y")
 
 intensity <- hourlyIntensities_merged %>%
   group_by(time(ActivityHour)) %>%
   drop_na() %>%
   summarise(mean_int = mean(TotalIntensity))
  ggplot(data = intensities, aes(x=hour(ActivityHour), y=TotalIntensity/1000))+
   geom_bar(stat = "identity", fill="light blue")+
    labs(title="Average Total Intensity vs. Time", subtitle = "This graph captures the intentisty of activity by the hour of the day.", x="Hour of the day", y="Total Intensity")
```



#### Observation
This graph above shows that intensity of activity of participants peaks around 
5:00PM and slowly declines till final rest. This might also indicate that
participants are white collar employees going by sedentary activity and the 
information in the graph above.




## Summary and recommendations for Bellabeat membership:


![Membership](C:\Users\user\Downloads\membership pic.jpg)


What is Bellabeat membership?

Bellabeat also offers a subscription-based membership program for users.
Membership gives users 24/7 access to fully personalized guidance on nutrition,
activity, sleep, health and beauty, and mindfulness based on their
lifestyle and goals.

### summary

The trend from this data evaluation shows that these participants are interested 
in their health markers. This is the reason for  their purchase of a smart device.
From the data available, most of the participants (24 of 33) put on their smart 
device even in bed.


**Who are our target audience?**

White Collar office workers who are too busy to visit their doctors regularly.

### Recommendations
* Our customers for Bellabeat membership should have their health markers 
monitored 24/7 and should be marketed as such.
* Our customers should be able to know that its not enough to monitor your body,
but also to have a professional help you interpret your health data.
* Our marketing team should emphasize the personalized health tips that would be
made available to our customers.
* Our marketing team should also explain that we are able to cater for users 
with other underlying health issues at no extra cost.
* Our marketing team should also focus on helping our customers know that we
can recommend other exciting ways to keep fit.



Thank You.
My first R project actually.
Footer
© 2022 GitHub, Inc.
Footer navigation
Terms
Privacy
Security
Status
Docs
Contact GitHub
Pricing
API
Training
Blog
About
My-first-R-project-/Project.Rmd at main · DHighchief/My-first-R-project-
