# TBANLT-560_Final-Project

---
title: "Project 2"
author: "Devin Pompa"
date: "3/7/2021"
output: word_document
---

#Install Packages
```{r}
#install.packages("caret")
#install.packages("e1071")
#install.packages("klaR")
#install.packages("nnet")
#install.packages("MASS")
#install.packages("rpart")
#install.packages("randomForest")
#install.packages("mlbench")
```


Library Packages
```{r}
library(tidyverse)
library(readxl)
library(ggplot2)
library(gplots)
library(caret)
library(e1071)
library(rpart)
library(randomForest)
library(klaR)
library(nnet)
library(MASS)
library(rpart)
library(mlbench)
```

#Load Data
```{r}
data(BreastCancer)
colnames(BreastCancer)
BreastCancer<-na.omit(BreastCancer)
BreastCancer$Id <- NULL
mydata <- BreastCancer
str(mydata)
```

```{r}
summary(mydata)
```

```{r}
mysvm <- svm(Class ~ ., mydata)
mysvm.pred <- predict(mysvm, mydata)
table(mysvm.pred,mydata$Class)
```


```{r}
mynb <- NaiveBayes(Class ~ ., mydata)
mynb.pred <- predict(mynb,mydata)
table(mynb.pred$class,mydata$Class)
```

```{r}
mynnet <- nnet(Class ~ ., mydata, size=1)
mynnet.pred <- predict(mynnet,mydata,type="class")
table(mynnet.pred,mydata$Class)
```


```{r}
mytree <- rpart(Class ~ ., mydata)
plot(mytree); text(mytree) # in "mydata_tree.ps"
summary(mytree)
mytree.pred <- predict(mytree,mydata,type="class")
table(mytree.pred,mydata$Class)
```


# Leave-1-Out Cross Validation (LOOCV)
```{r}
#ns <- numeric(length(mydata[,1]))
#for (i in 1:length(mydata[,1])) {
#  mytree <- rpart(Class ~ ., mydata[-i,])
#  mytree.pred <- predict(mytree,mydata[i,],type="class")
#  ans[i] <- mytree.pred
#}
#ans <- factor(ans,labels=levels(mydata$Class))
#table(ans,mydata$Class)
```

#Quadratic Discriminant Analysis
```{r}
#myqda <- qda(Class ~ ., mydata)
#myqda.pred <- predict(myqda, mydata)
#table(myqda.pred$class,mydata$Class)
```

#Regularised Discriminant Analysis
```{r}
myrda <- rda(Class ~ ., mydata)
myrda.pred <- predict(myrda, mydata)
table(myrda.pred$class,mydata$Class)
```

#Random Forests
```{r}
myrf <- randomForest(Class ~ .,mydata)
myrf.pred <- predict(myrf, mydata)
table(myrf.pred, mydata$Class)
```

```{r}
classifier1<- c(0,1,0,1, 0)
classifier2<-c(0,0,0,1,0)
classifier3<-c(0,0,0,1,0)
classifier4<-c(0,0,0,1,0)
classifier5<-c(0,0,0,1,0)
combine.df<-cbind(classifier1, classifier2, classifier3, classifier4, classifier5)
```


```{r}
head(myrf.pred)
head(mysvm.pred)
classifier1<-myrf.pred
classifier2<-mysvm.pred
classifier3<-myrda.pred$class
classifier4<-mytree.pred
classifier5<-mynb.pred$class
classifier6<-mynnet.pred
combine.df<-data.frame(classifier1, classifier2, classifier3, classifier4, classifier5, classifier6)
head(combine.df)
```


```{r}
combine.df[,1]<-ifelse(combine.df[,1]=="benign",0,1)
combine.df[,2]<-ifelse(combine.df[,2]=="benign",0,1)
combine.df[,3]<-ifelse(combine.df[,3]=="benign",0,1)
combine.df[,4]<-ifelse(combine.df[,4]=="benign",0,1)
combine.df[,5]<-ifelse(combine.df[,5]=="benign",0,1)
combine.df[,6]<-ifelse(combine.df[,6]=="benign",0,1)

# Checking
head(combine.df)
#combine.df[,7]<-NULL

```



```{r}
#combine.df[,7]<-rowSums(combine.df)

combine.df[,7]<- ifelse(rowSums(combine.df)>=4,"malignant","benign")
head(combine.df)
table(combine.df[,7], BreastCancer$Class)
```

