---
title: "Classification of Qualitative Analysis of Human Activity Recognition of Exercise Routines"
author: "John Lovrinic"
date: "Thursday, October 15, 2015"
output: html_document
---

## Summary
This paper presents an analysis of data from the Human Activity Recognition Project WLE dataset:
  
  
Velloso, E.; Bulling, A.; Gellersen, H.; Ugulino, W.; Fuks, H. [Qualitative Activity Recognition of Weight Lifting Exercises](http://groupware.les.inf.puc-rio.br/work.jsf?p1=11201). Proceedings of 4th International Conference in Cooperation with SIGCHI (Augmented Human '13) . Stuttgart, Germany: ACM SIGCHI, 2013. 

Read more about this project at [Groupware->Projects->HAR](http://groupware.les.inf.puc-rio.br/har#sbia_paper_section).

Our objective is to attempt to classify the the manner of the exercises performed (correctly or which manner of incorrectness), based on the qualitative information provided by the data set. 
The test data consists of the 6 participants performing Unilateral Dumbbell Biceps Curl in five different fashions:

- Class A - exactly according to the specification (correct)
- Class B - throwing the elbows to the front (incorrect)
- Class C - lifting the dumbbell only halfway (incorrect)
- Class D - lowering the dumbbell only halfway (incorrect)
- Class E - throwing the hips to the front (incorrect)



## Analysis
### Preprocessing
We began by paring down the dataset to useful information.  This entailed removing every column related to stddev, avg, var, max, min, amplitude, kurtosis, skewness, timestamp, row identifier (X) and subject name.  Most were null, or NA, and names, which identify the subject, while a factor, would affect the general applicability of our solution.  A further reducion of features was contemplated, but doing all 2x combinations to look for highly correlated pairs was impractical given our limit of 5 plots for this paper.  This left us with 54 features:

'r names(training)`

### Model Fitting
Given this was a classification problem with more than 2 resultant classes, it was decided to explore an rpart and a random forest solution. We started with a 60/40 training/test split and employing the rpart method.  While quick, accuracy was less than 50% and so not acceptable.j  Switching to the random forest method, accuracy jumped to 95%, but execution time also climbed to over an hour.  At this time a 70/30 training/test split was attempted and this gave satisfactory results with accuracy of over 99% and execution time climbing no higher.  A breakdown of the findings follows.

Confusion Matrix
`r conf`

Final Model
`r modfit$finalModel`

## Conclusion


Code Section
```r
library(AppliedPredictiveModeling)
library(caret)
library(ElemStatLearn)
library(pgmm)
install.packages("randomForest")
setwd("F:/Coursera/Practical Machine Learning/CourseProject")
dat<-read.csv("pml-training.csv")
test<-read.csv("pml-testing.csv")
l1<-grepl("stddev|avg|var|max|min|amplitude|kurtosis|skewness|name|timestamp",names(dat),perl=TRUE,ignore.case=TRUE)
l1[1]<-TRUE
d1<-dat[,!l1]
set.seed(12432)
intrain<-createDataPartition(y=d1$classe,p=.7,list=FALSE)
training<-d1[intrain,]
testing<-d1[-intrain,]
modfit<-train(classe~.,method="rf",data=training)
pred<-predict(modfit,newdata=testing)
conf<-confusionMatrix(pred,reference=testing$classe)
ans<-predict(modfit,newdata=test)
```

