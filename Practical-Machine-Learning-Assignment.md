# Practical Machine Learning Assignment
B. Vink  
26 april 2016  

## 1. Introduction
Using devices such as *Jawbone Up*, *Nike FuelBand*, and *Fitbit* it is now 
possible to collect a large amount of data about personal activity relatively 
inexpensively. These type of devices are part of the quantified self movement 
– a group of enthusiasts who take measurements about themselves regularly to 
improve their health, to find patterns in their behavior, or because they are 
tech geeks. One thing that people regularly do is quantify **how much** of a 
particular activity they do, but they rarely quantify **how well** they do it. 

This project, will use data from accelerometers on the belt, forearm, arm, and 
dumbell of 6 participants. They were asked to perform barbell lifts correctly 
and incorrectly in 5 different ways.  The five ways are:

- **Class A**:   Exactly according to the specification 
- **Class B**:   Throwing the elbows to the front 
- **Class C**:   Lifting the dumbbell only halfway 
- **Class D**:   Lowering the dumbbell only halfway 
- **Class E**:   Throwing the hips to the front 

The goal of this project is to predict the manner in which the participants did 
the exercise, i.e., Class A to E.

More information is available here: [http://groupware.les.inf.puc-rio.br/har](http://groupware.les.inf.puc-rio.br/har) 
(Weight Lifting Exercise Dataset section).

## 2. Executive summary

## 3. Global setup

```r
#Loading the required packages
library(knitr); library(caret);         library(rattle);
```

```
## Loading required package: lattice
```

```
## Loading required package: ggplot2
```

```
## Rattle: A free graphical interface for data mining with R.
## Version 4.1.0 Copyright (c) 2006-2015 Togaware Pty Ltd.
## Type 'rattle()' to shake, rattle, and roll your data.
```

```r
library(rpart); library(rpart.plot);    library(randomForest);
```

```
## randomForest 4.6-12
```

```
## Type rfNews() to see new features/changes/bug fixes.
```

```
## 
## Attaching package: 'randomForest'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     margin
```

```r
library(repmis)
```

```r
#knitr setup
opts_chunk$set(echo = TRUE) 
```

## 4. Exploratory data analyses

## 5. Prediction Algorithms

### 5.1 Classification trees

### 5.2 Random forests

## 6. Prediction on Testing Set
