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

## 2. Global setup

```r
#Loading the required packages
library(knitr); library(caret); library(rattle);
library(rpart); library(rpart.plot); library(randomForest);
library(repmis)
```

```r
#knitr setup
opts_chunk$set(echo = TRUE, fig.width = 10, fig.height = 6)
```

## 3. Getting and cleaning data

```r
#Downloading the training and test data
trainUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
testUrl  <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"

#Reading the data and replacing #DIC/0! for NA
training <- read.csv(url(trainUrl), na.strings = c("NA", "#DIV/0!", ""))
testing  <- read.csv(url(testUrl),  na.strings = c("NA", "#DIV/0!", ""))

#Show the data set dimensions 
dim(training); dim(testing)
```

```
## [1] 19622   160
```

```
## [1]  20 160
```
The training data has 19622 observations and 160 variables (predictors). The testing data has 20 observations and the same 160 variables. In this analysis predicts the outcome of variable `classe` in the training set.

To reduce the number of features you remove the variables with nearly zero variance, those variables are almost always NA. And removing the variables that intuitive don’t make sense for prediction. 

```r
#Removing NearZeroVariance variables
nzv    <- nearZeroVar(training, saveMetrics = TRUE)
train  <- training[, nzv$nzv == FALSE]

nzv    <- nearZeroVar(testing, saveMetrics = TRUE)
test   <- testing[, nzv$nzv == FALSE]

#Removing all NA's
train  <- train[, colSums(is.na(train)) == 0]
test   <- test [, colSums(is.na(test))  == 0]

#Removing the first six variables that, intuitive, don't make sense for prediction 
train  <- train[, -c(1:6)]
test   <- test[,  -c(1:6)]
```
The cleaned training and testing data stil have the same observations; 19622 for the training dataset and 20 for the testing dataset. The variables has changed to; 160 for the training dataset and 160 for the testing dataset.

In order to get out-of-sample errors, the data split the cleaned training set into a training set (70%) for prediction and a validation set (30%) to compute the out-of-sample errors.

```r
set.seed(23456)
#Splitting data into a training and vailidation set
inTrain    <- createDataPartition(train$classe, p = 0.7, list = FALSE)
train_set  <- train[inTrain, ]
valid_set  <- train[-inTrain, ]
```

## 4. Prediction Algorithms

### 4.1 Classification trees

```r
#Instruct train to use 5-fold cross validation
control <- trainControl(method = "cv", number = 5)

#Fit the classification tree model 
mod_ct  <- train(classe ~ ., method = "rpart", data = train_set, 
                 trControl = control)

#Create a fancyRpartPlot of the classification tree
fancyRpartPlot(mod_ct$finalModel)
```

![](Practical-Machine-Learning-Assignment_files/figure-html/creating a classification tree model-1.png)<!-- -->

```r
#Calculate the accuracy 
pred_ct <- predict(mod_ct, valid_set)
confusionMatrix(pred_ct, valid_set$classe)$overall[1]
```

```
##  Accuracy 
## 0.4837723
```
The confusion matrix shows a accuracy rate of 0.48. Using a classification tree does not produce a very accurate prediction. It might be worthty to look if anothter methode gets a more accurate prediction. 

### 4.2 Random forests

```r
#Fit the random forests model 
mod_rf  <- train(classe ~ ., method = "rf", data = train_set, 
                 trControl = control)

#Calculate the accuracy 
pred_rf <- predict(mod_rf, valid_set)
confusionMatrix(pred_rf, valid_set$classe)$overall[1]
```

```
##  Accuracy 
## 0.9942226
```
The confusion matrix shows a accuracy rate of 0.99 for the random forests model. This is allready quit high. 

### 4.3 Generalized Boosted Regression

```r
#Fit the generalized boosted regression model
mod_gbm  <- train(classe ~ ., method = "gbm", data = train_set, 
                 trControl = control, verbose = FALSE)

#Calculate the accuracy
pred_gbm <- predict(mod_gbm, valid_set)
confusionMatrix(pred_gbm, valid_set$classe)$overall[1]
```

```
##  Accuracy 
## 0.9648258
```
The confusion matrix shows a accuracy rate of 0.96 for the generalized boosted regression model. This means it is the second best preforming model.  

## 5. Prediction on Testing Set
The most accurate preforming model is the Random Forests model with an Accuracy 0.99. The expected out-of-sample error is 100 - 0.9942 = 0.58%.

```r
# prediction on testset
mod_fin <- predict(mod_rf, test)
mod_fin
```

```
##  [1] B A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```

```r
# convert predictions to character vector
mod_fin <- as.character(mod_fin)

# create function to write predictions to files
pred_to_file <- function(x) {
        n <- length(x)
        for(i in 1:n) {
                filename <- paste0("problem_id_", i, ".txt")
                write.table(x[i], file = filename, quote=F, row.names=F, col.names=F)
    }
}

# create prediction files for submition
pred_to_file(mod_fin)
```


