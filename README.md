# Yu-Tung-Cheng
HW5
---
title: "Homework 5"
author: "Yu-Tung, Cheng"
date: "2021/02/23"
output: pdf_document
---

```{r , include=FALSE, warning==FALSE,message=FALSE}
knitr::opts_chunk$set(echo = TRUE)
library(tidyverse)
```


# Problem 1

Create an account on GitHub (https://github.com) and create a repository for your personal dataset project. Submit the corresponding URL as for this problem. 

my github repo [URL](gh repo clone Jennnnnnn8133/Yu-Tung-Cheng)

# Problem 2

In your own words, write brief definitions of:

### (a) Mean: Sum up the value of observations then divide the sum by the total number. It measures the central tendency of data.

### (b) Median: The middle value of observations. It also measures the central tendency of data.

### (c) IQR: Quartile, just like quantile, devides observations into four parts. Interquartile range is the difference between the 25th quantile and the 75th quantile. It measurses the variability of data.

### (d) Variance: The average squared distance or deviation from the mean. It also measures the variability of data.

### (e) Skewness: The measurement of asymmetry of data.

# Problem 3
Load the provided COL.csv dataset into R. 

```{r warning=FALSE,message=FALSE}
df_p03 <- read_csv("COL.csv")
```

### (a) 
Decide which rows are outliers in this data and describe and justify how you determined their outlier status.

```{r warning=FALSE,message=FALSE}
df_p03 %>% ggplot(aes(y = Cappuccino)) + geom_boxplot()
df_p03_agg <- df_p03 %>%
  summarise(min = min(Cappuccino,na.rm = TRUE),
            Q1 = quantile(Cappuccino,probs = .25, na.rm = TRUE),
            Q3 = quantile(Cappuccino,probs = .75, na.rm = TRUE),
            IQR= IQR(Cappuccino,na.rm = TRUE),
            l_outlier_Fence = Q1 - 1.5 * IQR,
            u_outlier_Fence = Q3 + 1.5 * IQR)

df_p03 %>% 
  filter(Cappuccino < df_p03_agg$l_outlier_Fence | Cappuccino > df_p03_agg$u_outlier_Fence)
```
In terms of Cappuccino, Stavanger is the outlier row. I use the Q1 - 1.5 * IQR and Q3 + 1.5 * IQR as the standard since the observations leave too away from other data.

### (b)
For each row you identified, if you were performing EDA on this dataset, would you include its values in your analysis and plots? 

I will include its value when performing EDA.

### (c)
Why or why not?

I can cross-check whether the row is still an outlier by checking other columns of it.

# Problem 4

Load the Height_Weight_Age_Sex.csv data into R. 

```{r warning=FALSE,message=FALSE}
df_p04 <- read_csv("Height_Weight_Age_Sex.csv")
```
 
### (a) 
Create boxplots for the height and weight columns separately. Comment on the symmetry and sknewness, if any, for their
distributions using these plots.

```{r warning=FALSE,message=FALSE}
df_p04 <- read_csv("Height_Weight_Age_Sex.csv")
# height
df_p04 %>%
  ggplot(aes(y = height)) +
  geom_boxplot()
# weight
df_p04 %>%
  ggplot(aes(y = weight)) +
  geom_boxplot()
```

Boxplots of height and column show that distribution of the observations are not symmetric. Since medians are closer to Q3 than to Q1, these two variables exhibit negative skewness.

### (b)
Create  histograms for the height and weight columns separately. Comment on the symmetry and sknewness, if any, for their
distributions using these plots.  Are your conclusions based on the boxplots in (a) consistent with those
based on densities?

```{r warning=FALSE,message=FALSE}
# height
df_p04 %>%
  ggplot(aes(x = height)) +
  geom_histogram()
# weight
df_p04 %>%
  ggplot(aes(x = weight)) +
  geom_histogram()
```

In terms of height, it is quite clear that the observations exhibit negative skewness, which is consistent with the trend in our boxplot. However, speaking to weight, the histogram shows that there are two peaks, which is not easily observable using boxplots. 

### (c)
Create separate boxplots for the weight data separated by the Male variable. What do you observe about the two distributions? 

```{r warning=FALSE,message=FALSE}
# weight
df_p04 %>% mutate(male = as.character(male)) %>%
  ggplot(aes(x = male, y = weight)) +
  geom_boxplot()
```
Both male and female observations show negative skewness. Compared with male, weight of female has a smaller IQR and a smaller median.

### (d)
Add a BMI column and an underweight column to the data frame: 

``` {r newcolumns}
HWAS <- read.csv("./Height_Weight_Age_Sex.csv") %>% as_tibble()

HWAS$BMI <- HWAS$weight/((HWAS$height/100)**2)

HWAS$underweight <- HWAS$BMI <18.5

head(HWAS)

```

### (e)
Create separate histograms for the BMI column separated by the Male variable. What do you observe about the two distributions?

``` {r message=FALSE, warning=FALSE}
HWAS %>%
  ggplot(aes(x = BMI)) +
  geom_histogram() +
  facet_wrap(male~.)
```
Both male and female observations show negative skewness. It seems like that compared with male, weight of female has a bigger IQR since the observations scatter more a.

### (f)

Make a scatterplot of height vs. weight for the full dataset that distinguishes both the Male variable and the under variable. What do you observe?

``` {r message=FALSE, warning=FALSE}
HWAS %>% mutate(male = as.character(male)) %>%
  ggplot(aes(x = height, y = weight, alpha = underweight, color = male)) +
  geom_point()
```
First, most of observations don't have underweight issues. Among observations without underweight issues, there is no clear distinction between male and female. Second, among observations with underweight issues, it is clear that all of them lie above those without issues, which is also understandable. Third, both kinds of data show that male have higher weight than female.

# Problem 5

Read the following examples about Simpson's Paradox: \href{https://people.eecs.berkeley.edu/~daw/teaching/cs70-s08/notes/n21.pdf}{How to lie with statistics?} (you may also find the wikipedia page on the topic or the additional readings uploaded to Blackboard to be helpful resources). Fill in the following table with ratios of hits to attempts so that player A has a higher batting average in both season 1 and season 2 but player B has a higher overall batting average for the two seasons combined.


|hits/attempts | Season 1 |  Season 2|
|--------------|----------|----------|
|Player A      | 25/100   | 10/ 25   |
|Player B      |  5/ 25   | 39/100   |

The more important part affecting outcome for player A, is season 1. On contrary, player B is season2. The key to Simpson's Paradox is that the distribution between season 1 and season 2. For B, the bigger part, which is season2 is slightly smaller than A, and the smaller part is not the main focus since it played a small role. Season 2 of player B is thus affecting the result of combined percentage bigger.

