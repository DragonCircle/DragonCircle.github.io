---
layout: post
title:  "JHU Machine Learning Course Project"
date:   2015-11-32 23:09:00s
categories: ML
highlight: true
---
# JHU Machine Learning Course Project

## Background

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit it is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement, One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it. In this project, we use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. And the result can potentially provide useful information for a large variety of applications,such as sports training.

## Dataset Introduction
Six young health participants were asked to perform one set of 10 repetitions of the Unilateral Dumbbell Biceps Curl in five different fashions: exactly according to the specification (Class A), throwing the elbows to the front (Class B), lifting the dumbbell only halfway (Class C), lowering the dumbbell only halfway (Class D) and throwing the hips to the front (Class E).

Class A corresponds to the specified execution of the exercise, while the other 4 classes correspond to common mistakes. Participants were supervised by an experienced weight lifter to make sure the execution complied to the manner they were supposed to simulate. The exercises were performed by six male participants aged between 20-28 years, with little weight lifting experience. We made sure that all participants could easily simulate the mistakes in a safe and controlled manner by using a relatively light dumbbell (1.25kg).

## Data loading and first glance

```r
data <- read.csv("pml-training.csv")
dim(data)
```

```
## [1] 19622   160
```

As we can see from the `dim` function, this weight lifting exercise dataset contains 160 variables and 19622 observations. These variables contains observation index number, collecting timestamp,sample window parameters, participant's name and accelemeters sample data on four differents parts. Namely, these parts are belt, forearm, dumbbell, arm. For each part, accelemeter collects accelerate, magnet, and angle in three directions.

## Data Preprocessing

```r
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```r
data <- data[-c(1:7)]
nzv <- nearZeroVar(data)
data <- data[,-nzv]  # removing near Zero covariates
data <- data[,colSums(is.na(data))==0] # removing columns that include NA values
print(dim(data))
```

```
## [1] 19622    53
```

It's clear that timestamp, row index, participant's name and window parameters do not help us to do classe prediction, so we first remove them. Also, for that columns include NA values, we observe that theses columns are all have 98% percent NA values, so we remove them. Final, we omit there near-zero variables, in case there variables dilute trainning process.After all that preprocess procedures, we can see that there are still 49 variables left, which a little too much for training, expecially for algorithms like RandomForest, which have relatively high computional complexity.


```r
test_final <- read.csv("pml-testing.csv")
test_final <- test_final[,colnames(data)[-53]]
inTrain <- createDataPartition(data$classe,p=0.75,list=FALSE)
training <- data[inTrain,]
testing <- data[-inTrain,]
```

We spilt the whole dataset into training dataset(75%) and testing dataset(25%), this is a common procedure with nothing to explain.


```r
preObj <- preProcess(training[,-53],method="pca",thresh=0.8)
print(preObj) # Shows that We Need 12 compoments to capture 80% of the variance
```

```
## Created from 14718 samples and 52 variables
##
## Pre-processing:
##   - centered (52)
##   - ignored (0)
##   - principal component signal extraction (52)
##   - scaled (52)
##
## PCA needed 13 components to capture 80 percent of the variance
```

```r
trainPC <- predict(preObj,training)
testPC <- predict(preObj,testing)
test_finalPC <- predict(preObj,test_final)
dim(trainPC)
```

```
## [1] 14718    14
```

* In this procedure, we futher reduce the size of the problem by doing PCA.
* We set the threshold of cumulative variance to 80%, which means that we retain 80% of variance.
* By doing PCA, we can see that now we only have 13 varibales, which is good for training.

## Parallel processing


```r
library(doMC)
```

```
## Loading required package: foreach
## Loading required package: iterators
## Loading required package: parallel
```

```r
registerDoMC(cores=8)
```

To make my quad core eight thread processor computer run at fully speed, we use doMC package to do parallel processing, set the cores we need to 8.


```r
modelFit_PCA <- train(classe~.,method="rf",data=trainPC) # Random Forest with PCA
```

```
## Loading required package: randomForest
## randomForest 4.6-12
## Type rfNews() to see new features/changes/bug fixes.
```

```r
modelFit_noPCA <- train(classe~.,method="rf",data=training) # Random Forest without PCA
```

After 30 minutes, here we get the model that do random forest with and without PCA.


```r
pred_PCA <- predict(modelFit_PCA,testPC)
pred_noPCA <- predict(modelFit_noPCA,testing)
```

## Random Forest with PCA


```r
library(caret)
```

```
## Loading required package: lattice
## Loading required package: ggplot2
```

```r
confusionMatrix(pred_PCA,testing$classe)
```

```
## Confusion Matrix and Statistics
##
##           Reference
## Prediction    A    B    C    D    E
##          A 1366   21    6    6    2
##          B   14  907   14    3    4
##          C    2   17  824   26    7
##          D   11    2    9  767   10
##          E    2    2    2    2  878
##
## Overall Statistics
##                                           
##                Accuracy : 0.967           
##                  95% CI : (0.9616, 0.9718)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2e-16         
##                                           
##                   Kappa : 0.9582          
##  Mcnemar's Test P-Value : 0.01321         
##
## Statistics by Class:
##
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9792   0.9557   0.9637   0.9540   0.9745
## Specificity            0.9900   0.9912   0.9872   0.9922   0.9980
## Pos Pred Value         0.9750   0.9628   0.9406   0.9599   0.9910
## Neg Pred Value         0.9917   0.9894   0.9923   0.9910   0.9943
## Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
## Detection Rate         0.2785   0.1850   0.1680   0.1564   0.1790
## Detection Prevalence   0.2857   0.1921   0.1786   0.1629   0.1807
## Balanced Accuracy      0.9846   0.9734   0.9755   0.9731   0.9862
```

As we can see from `confusionMatrix` result, randomforest with PCA preprocess, we get Accuracy of 96.19%.

## Random Forest without PCA


```r
confusionMatrix(pred_noPCA,testing$classe)
```

```
## Confusion Matrix and Statistics
##
##           Reference
## Prediction    A    B    C    D    E
##          A 1390    6    0    0    0
##          B    4  936    5    0    1
##          C    0    7  845    5    0
##          D    0    0    5  798    8
##          E    1    0    0    1  892
##
## Overall Statistics
##                                           
##                Accuracy : 0.9912          
##                  95% CI : (0.9882, 0.9936)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9889          
##  Mcnemar's Test P-Value : NA              
##
## Statistics by Class:
##
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9964   0.9863   0.9883   0.9925   0.9900
## Specificity            0.9983   0.9975   0.9970   0.9968   0.9995
## Pos Pred Value         0.9957   0.9894   0.9860   0.9840   0.9978
## Neg Pred Value         0.9986   0.9967   0.9975   0.9985   0.9978
## Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
## Detection Rate         0.2834   0.1909   0.1723   0.1627   0.1819
## Detection Prevalence   0.2847   0.1929   0.1748   0.1654   0.1823
## Balanced Accuracy      0.9974   0.9919   0.9927   0.9947   0.9948
```

From the result, randomforest without PCA achieve accuracy of 99.16%, 3 percent higher than randomforest with PCA.

## Model Selecting


```r
confu1 <- as.data.frame(confusionMatrix(pred_PCA,testing$classe)$table)
confu2 <- as.data.frame(confusionMatrix(pred_noPCA,testing$classe)$table)
ggplot(confu1,aes(x=Reference, y=Prediction, fill=Freq)) +ggtitle("Random Forest with PCA") + geom_tile()
```

![](Coursera_files/figure-html/unnamed-chunk-10-1.png)

```r
ggplot(confu1,aes(x=Reference, y=Prediction, fill=Freq)) +ggtitle("Random Forest without PCA")+ geom_tile()
```

![](Coursera_files/figure-html/unnamed-chunk-10-2.png)

After compare the accurcy of models, we choose randomforest without PCA as out final model to take the predictions of the validation class.

## Predict results


```r
print(predict(modelFit_noPCA, test_final))
```

```
## Loading required package: randomForest
## randomForest 4.6-12
## Type rfNews() to see new features/changes/bug fixes.
```

```
##  [1] B A B A A E D B A A B C B A E E A B B B
## Levels: A B C D E
```
