# Fine Tune Models



\begin{center}\includegraphics[width=0.7\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/fine_tune} \end{center}
In the previous chapters, we have obtained the knowledge that many prediction models need fine-tune, so they could:

1. better fit the training dataset, 
2. produce better performance for the test dataset (less overfit). 

In most data science projects, fine-tune a model is not only necessary but also desirable since it can increase the final outcomes of the projects. In this chapter, we will demonstrate techniques that are used to fine-tune a prediction model.

Fine-tune a model is specific to the model. Different models may have different parameters and different measurements of the performance. In general, A model's overall performance is affected by the three factors:

1. **The predictors.** The numbers of predictors used in a model and the specific predictors used in the model. 
2. **The training sample.** The larger the data sample the better change of model fit. However, there are many methods for dealing with a small data sample. Mostly to enlarge the data sample or make efficient use of available data samples.
3. **The parameter of the model.** The adjustable parameters in a model. Most of the model tuning refers to adjust the parameters of the model. 

We can see that the search space defined by the three dimensions is fairly large. Sometimes it is computationally infeasible to search them in one go. In practice, we generally fix one or two dimensions and search for another dimension. In other words, we can fine-tune them one by one.  

That is what we are going to do.

## Tuning a model's Predictor

From our *Decision tree* and *Random forest* model constructions, we've learned that the predictors can affect a model's performance. The techniques used to select the best predictors is *Correlation analysis* and *Association analysis*, to select predictors that have no correlation among the predictors and strong association with the dependent variable, in other words, the attributes have the prediction power. Some models can provide some measurements of contributions of each predictor to the response variable. Such as in *Random forest* model, the user can specify the `importance` parameter to `true` (See Chapter 9), and let the model record the predictors' `importance` and this importance can be used for post-model-construction analysis. If we have the predictors' importance, the tuning of the number of the predictors will become simple. We can simply take "bottom-up" or "top-down" approaches to tune the number of predictors until the best model accuracy has been achieved. 

Let us use **Random forest** model to demonstrate this process. Recall that we have built three random forest models (in Chapter 9), each has different predictors and model's accuracy (see Table 9.1). Let us ignore the overfitting issue at the moment and focus on the predictor's impact on the model's accuracy. Among the three models, both `model1` and `model3` have lower accuracy than `model2`. `Model2` has more predictors than `model1` and fewer predictors than `model3`. It reveals a principle which we have mentioned earlier, that is more predictors not necessarily mean higher accuracy. 

To fine-tune the predictors, let us use the "top-down" approach. We start from the use of all attributes and gradually reduce the predictors by removing the least important attribute until the last attribute. We can compare the models' accuracy and select the highest model.


```r
# load necessary library
library(randomForest)
library(plyr)
library(caret)

# load our re-engineered data set and separate train and test datasets
RE_data <- read.csv("./data/RE_data.csv", header = TRUE)
train <- RE_data[1:891, ]
test <- RE_data[892:1309, ]

# Train a Random Forest with the default parameters using full attributes
# Survived is our response variable and the rest can be predictors except pasengerID. 
rf.train <- subset(train, select = -c(PassengerId, Survived))
rf.label <- as.factor(train$Survived)

#RandomForest cannot handle factors with over 53 levels
rf.train$Ticket <- as.numeric(train$Ticket)

set.seed(1234) # for reproduction 
# FT_rf.1 <- randomForest(x = rf.train, y = rf.label, importance = TRUE)
# save(FT_rf.1, file = "./data/FT_rf.1.rda")
load("./data/FT_rf.1.rda")
FT_rf.1
```

```
## 
## Call:
##  randomForest(x = rf.train, y = rf.label, importance = TRUE) 
##                Type of random forest: classification
##                      Number of trees: 500
## No. of variables tried at each split: 4
## 
##         OOB estimate of  error rate: 14.93%
## Confusion matrix:
##     0   1 class.error
## 0 491  58   0.1056466
## 1  75 267   0.2192982
```

```r
#rf.1 model with full house predictors has error rate: 15.49% 
# Check the order of the predictors prediction power.
pre.or <- sort(FT_rf.1$importance[,3], decreasing = TRUE)
pre.or
```

```
##        Title          Sex      Fare_pp       Pclass Ticket_class       Ticket 
##  0.084673918  0.083622938  0.034991513  0.031993819  0.031729670  0.026882064 
##          Age  Friend_size         Deck    Age_group   Group_size  Family_size 
##  0.022106781  0.016718555  0.013445769  0.012993600  0.011544400  0.010971526 
##  HasCabinNum     Embarked        SibSp        Parch 
##  0.008285454  0.005295491  0.004567226  0.002776599
```

```r
varImpPlot(FT_rf.1, main="Ordered predictors measurements")
```

\begin{figure}

{\centering \includegraphics{11-fine-tune-models_files/figure-latex/ImportancePlot-1} 

}

\caption{The importance of the predictors}(\#fig:ImportancePlot)
\end{figure}
We have obtained the "full-house" model's accuracy 85.07%, that is `1 - prediction error` (14.93%). 

We have also obtained the order of the predictor's prediction power, which is from the most to the least in the following order: "*Sex*, *Title*, *Fare_pp*, *Ticket_class*, *Pclass*, *Ticket*, *Age*, *Friend_size*, *Deck*, *Age_group*, *Group_size*, *Family_size*, *HasCabinNum*, *SibSp*, *Embarked*, *Parch*".

We now can repeat the process by removing one attribute from the end of the list above and train a new *Random forest model* such as `FT_rf.2`, `FT_rf.1.3`, ... until `FT_rf.1.16`. We can compare the models' `OOB error` or `Accuracy` to find out which model has the highest accuracy. 

We only shows `FT_rf.2` as an example in here, 


```r
# rf.2 as an example
rf.train.2 <- subset(rf.train, select = -c(Parch))
set.seed(1234)
# FT_rf.2 <- randomForest(x = rf.train.2, y = rf.label, importance = TRUE)
# save(FT_rf.2, file = "./data/FT_rf.2.rda")
load("./data/FT_rf.2.rda")
FT_rf.2
```

```
## 
## Call:
##  randomForest(x = rf.train.2, y = rf.label, importance = TRUE) 
##                Type of random forest: classification
##                      Number of trees: 500
## No. of variables tried at each split: 3
## 
##         OOB estimate of  error rate: 15.38%
## Confusion matrix:
##     0   1 class.error
## 0 492  57   0.1038251
## 1  80 262   0.2339181
```
We have obtained the model `FT_rf.2`, in which the attribute *"Parch"* has been removed since it is the last attributes in the attributes' prediction power list, which means it has the least prediction power. 

The *FT_rf.2*  model's accuracy 84.62%, that is `1 - prediction error` (15.38%). 

We can carry on the process until the last attribute *Sex*. We will then have a list of models and each has its estimated accuracy (We will not repeat the process in here and leave it to the exercise).

Once we have completed the process, the results can be listed and compared as in the Table \@ref(tab:Comppredictor).


```r
library(tidyr)

Model <- c("rf.1","rf.2","rf.3","rf.4","rf.5","rf.6","rf.7","rf.8","rf.9","rf.10","rf.11","rf.12","rf.13","rf.14","rf.15","rf.16")
Pre <- c("Sex", "Title", "Fare_pp", "Ticket_class", "Pclass", "Ticket", "Age", "Friend_size", "Deck", "Age_group", "Group_size", "Family_size", "HasCabinNum", "SibSp", "Embarked", "Parch")
#Produce models predictor list
Pred <- rnorm(16)
tem <- NULL
for (i in 1:length(Pre)) {
  tem  <- paste(tem, Pre[i], sep = " ")
#Using environment variable setting    
  ls  <- paste("Pred[",i,"]", sep="")
  eq  <- paste(paste(ls, "tem", sep="<-"), collapse=";")  
  eval(parse(text=eq)) 
  }
Pred <- sort(Pred, decreasing = TRUE)

Error <- c(15.49, 15.15, 14.93, 15.26, 14.7, 14.7, 14.03, 13.58, 14.48, 15.6, 16.27, 16.95, 17.51, 20.31, 20.76, 21.32)
Accuracy <- 100 - Error
df <- data.frame(Model, Pred, Accuracy)

knitr::kable(df, longtable = TRUE, booktabs = TRUE, digits = 2, col.names =c("Models", "Predictors", "Accuracy"), 
  caption = 'Model Predictors Comparision'
)
```


\begin{longtable}[t]{llr}
\caption{(\#tab:Comppredictor)Model Predictors Comparision}\\
\toprule
Models & Predictors & Accuracy\\
\midrule
rf.1 & Sex Title Fare\_pp Ticket\_class Pclass Ticket Age Friend\_size Deck Age\_group Group\_size Family\_size HasCabinNum SibSp Embarked Parch & 84.51\\
rf.2 & Sex Title Fare\_pp Ticket\_class Pclass Ticket Age Friend\_size Deck Age\_group Group\_size Family\_size HasCabinNum SibSp Embarked & 84.85\\
rf.3 & Sex Title Fare\_pp Ticket\_class Pclass Ticket Age Friend\_size Deck Age\_group Group\_size Family\_size HasCabinNum SibSp & 85.07\\
rf.4 & Sex Title Fare\_pp Ticket\_class Pclass Ticket Age Friend\_size Deck Age\_group Group\_size Family\_size HasCabinNum & 84.74\\
rf.5 & Sex Title Fare\_pp Ticket\_class Pclass Ticket Age Friend\_size Deck Age\_group Group\_size Family\_size & 85.30\\
\addlinespace
rf.6 & Sex Title Fare\_pp Ticket\_class Pclass Ticket Age Friend\_size Deck Age\_group Group\_size & 85.30\\
rf.7 & Sex Title Fare\_pp Ticket\_class Pclass Ticket Age Friend\_size Deck Age\_group & 85.97\\
rf.8 & Sex Title Fare\_pp Ticket\_class Pclass Ticket Age Friend\_size Deck & 86.42\\
rf.9 & Sex Title Fare\_pp Ticket\_class Pclass Ticket Age Friend\_size & 85.52\\
rf.10 & Sex Title Fare\_pp Ticket\_class Pclass Ticket Age & 84.40\\
\addlinespace
rf.11 & Sex Title Fare\_pp Ticket\_class Pclass Ticket & 83.73\\
rf.12 & Sex Title Fare\_pp Ticket\_class Pclass & 83.05\\
rf.13 & Sex Title Fare\_pp Ticket\_class & 82.49\\
rf.14 & Sex Title Fare\_pp & 79.69\\
rf.15 & Sex Title & 79.24\\
\addlinespace
rf.16 & Sex & 78.68\\
\bottomrule
\end{longtable}
From the table we can see that the best model is **FT_rf.8**. Its accuracy reaches 86.42 and the predictors are:


```r
# load the best model and record its predictors
# save(FT_rf.8, file = "./data/FT_rf.8.rda")

load("./data/FT_rf.8.rda")
Predictor <- c("Sex, Title, Fare_pp, Ticket_class, Pclass, Ticket, Age, Friend_size, Deck")
Predictor
```

```
## [1] "Sex, Title, Fare_pp, Ticket_class, Pclass, Ticket, Age, Friend_size, Deck"
```

Of course, you can try different combinations of the predictors. The idea will be the same. 

Some other models support predictor fine-tune. For example, Logistic Regression model `glm` provides *Stepwise* attributes prediction power analysing. One can use *"backward"* Step-wise search to compare models' AIC^[The Akaike information criterion (AIC) is an estimator of out-of-sample prediction error and thereby the relative quality of models for a given dataset. Given a collection of models for the data, AIC estimates the quality of each model, relative to each of the other models. Thus, AIC provides a means for model selection.] to find the best model and its predictors.   

## Tuning Training Data Samples

Once we have selected predictors from available attributes of data samples. The second factor that affects the model's performance is the data samples. We know that we should try to make the most use of data samples for training a model. In practice, a lot of times, we only have a small-sized data sample and we have to use other techniques to enlarge the data sample. However, there is a drawback of over-used the data sample. That is the possibility of the noise and outliers may have been fitted into a model and therefore decreasing the model's prediction accuracy when used in production. In this section, we use CV to demonstrate the process of setting the right portion of the data sample. 

We will use CV to illustrate the technique. So it means to set the proper portion (ratio) between the split of the train dataset and the sample usage in the model's fitting process.

We will continue to use RF model as our example. We know that the RF model has an overfitting issue. One possibility is we have used an improper portion of the training dataset. Following our predictor selection in the above section, we know that the best RF model is `FT_rf.8`. We will use `FT_rf.8` to demonstrate how to find the best CV settings. 

### Set Prediction Accuracy Benchmark {-}

The benchmark is the model's prediction accuracy on the test dataset (unseen data).


```r
# Let's start with a submission of FT_rf.8 to Kaggle 
# to find the difference between model's OOB and the accuracy

# Subset our test records and features
test.submit.df <- test[, c("Sex", "Title", "Fare_pp", "Ticket_class", "Pclass", "Ticket", "Age", "Friend_size", "Deck")]
test.submit.df$Ticket <- as.numeric(test.submit.df$Ticket)

# Make predictions
FT_rf.8.preds <- predict(FT_rf.8, test.submit.df)
table(FT_rf.8.preds)
```

```
## FT_rf.8.preds
##   0   1 
## 260 158
```

```r
# Write out a CSV file for submission to Kaggle
submit.df <- data.frame(PassengerId = test$PassengerId, Survived = FT_rf.8.preds)

write.csv(submit.df, file = "./data/FT_rf.8.csv", row.names = FALSE)
```

After our submission, we have scores of 0.75598 from Kaggle, but the OOB predicts that we should score 0.8642. We can see there is a big gap in between. The idea is to reduce this gap by adjusting the CV sampling controls. 

### 10 Folds CV Repeat 10 Times {-}

Let's look into CV using the `caret` package to see if we can get more accurate estimates by adjusting CV's sampling parameters. Research has shown that `10-fold` CV `repeated 10 times` is the best place to start. One of the important ideas is that to ensure that the ratio of those values of the response variable (*Survived*) in each fold matches the overall training set. This is known as **stratified CV**\index{stratified CV}.

Firstly, We randomly create `10 folds` and `repeat 10` times with our train dataset by a caret function `createMultiFolds`. So it has effectively enlarged our train data sample 100 times. This can be seen from the length of the list '`cv.10.folds`'. We will use these settings to train our RF model `rf.8`. Before we do that, we can also verify the survived ratio in the samples created to see if they are the same or close to the same ratio. 


```r
library(caret)
library(doSNOW)

set.seed(2348)
# rf.label is the Survived in the train dataset.
# ? createMultiFolds to find out more. train (891)
cv.10.folds <- createMultiFolds(rf.label, k = 10, times = 10)

# Check stratification
table(rf.label)
```

```
## rf.label
##   0   1 
## 549 342
```

```r
342 / 549
```

```
## [1] 0.6229508
```

```r
table(rf.label[cv.10.folds[[34]]])
```

```
## 
##   0   1 
## 494 308
```

```r
308 / 494
```

```
## [1] 0.6234818
```
We can see that we have produced 100 sample sets and they are in a size of $length(train)*9/10$ and kept the stratification (both has a similar radio around 62.3%). Let us use "`repeatedcv`" to train our `rf.8` model and see the impact of the sampling on the model's prediction accuracy. 


```r
# Set up caret's trainControl object using 10-folds repeated CV
ctrl.1 <- trainControl(method = "repeatedcv", number = 10, repeats = 10, index = cv.10.folds)
```
Model construction with "`10-folds repeated CV`" is a very expensive computation. Thanks, R has a package called **"doSNOW"**, that facilities the use of a multi-core processor and permits parallel computing in a pseudo cluster mode\index{pseudo cluster mode}.


```r
## Set up doSNOW package for multi-core training. 
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
# # Set seed for reproducibility and train
# set.seed(34324)
# 
# FT_rf.8.cv.1 <- train(x = rf.train.8, y = rf.label, method = "rf", tuneLength = 3, ntree = 500, trControl = ctrl.1)
# 
# #Shutdown cluster
# stopCluster(cl)
# save(FT_rf.8.cv.1, file = "./data/FT_rf.8.cv.1.rda")
# Check out results
load("./data/FT_rf.8.cv.1.rda")
# Check out results
FT_rf.8.cv.1 
```

```
## Random Forest 
## 
## 891 samples
##   9 predictor
##   2 classes: '0', '1' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold, repeated 10 times) 
## Summary of sample sizes: 802, 802, 802, 802, 802, 802, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##   2     0.8495937  0.6773587
##   5     0.8511480  0.6818755
##   9     0.8467582  0.6727850
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 5.
```

The RF model `FT_rf.8.cv.1` trained using new data samples (10 sets with each has 802 data records) above is only slightly more pessimistic than the `rf.8` OOB\index{OOB} prediction since the accuracy reduced from 0.8642 to 0.8511, but not pessimistic enough to the test accuracy, it is 0.75598. However, it clearly demonstrated the impact of the data samples on the model's performance. 

### 5 Folds CV Repeat 10 Times {-}

Let's try new data samples with 5-fold CV repeated 10 times.

```r
set.seed(5983)
# cv.5.folds <- createMultiFolds(rf.label, k = 5, times = 10)
# 
# ctrl.2 <- trainControl(method = "repeatedcv", number = 5, repeats = 10, index = cv.5.folds)
# 
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
# 
# set.seed(89472)
# FT_rf.8.cv.2 <- train(x = rf.train.8, y = rf.label, method = "rf", tuneLength = 3, ntree = 500, trControl = ctrl.2)
# 
# #Shutdown cluster
# stopCluster(cl)
# save(FT_rf.8.cv.2, file = "./data/FT_rf.8.cv.2.rda")
# # Check out results
load("./data/FT_rf.8.cv.2.rda")
# Check out results
FT_rf.8.cv.2 
```

```
## Random Forest 
## 
## 891 samples
##   9 predictor
##   2 classes: '0', '1' 
## 
## No pre-processing
## Resampling: Cross-Validated (5 fold, repeated 10 times) 
## Summary of sample sizes: 714, 713, 713, 712, 712, 712, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##   2     0.8491649  0.6768403
##   5     0.8444515  0.6675058
##   9     0.8415270  0.6618163
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 2.
```

We can see that `5-fold CV` is a little better. The accuracy is now moved under 85% (0.8491649). The model's training data set is moved from 9/10 to 4/5, which is around 713 records per fold now. 

### 3 Folds CV Repeat 10 Times {-}

Let us move further to try `3-fold CV` repeated 10 times. 


```r
set.seed(37596)
# cv.3.folds <- createMultiFolds(rf.label, k = 3, times = 10)
# 
# ctrl.3 <- trainControl(method = "repeatedcv", number = 3, repeats = 10, index = cv.3.folds)
# 
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
# 
# set.seed(94622)
# FT_rf.8.cv.3 <- train(x = rf.train.8, y = rf.label, method = "rf", tuneLength = 3, ntree = 500, trControl = ctrl.3)
# 
# #Shutdown cluster
# stopCluster(cl)
# 
# save(FT_rf.8.cv.3, file = "./data/FT_rf.8.cv.3.rda")
# # # Check out results
load("./data/FT_rf.8.cv.3.rda")
# Check out results
FT_rf.8.cv.3
```

```
## Random Forest 
## 
## 891 samples
##   9 predictor
##   2 classes: '0', '1' 
## 
## No pre-processing
## Resampling: Cross-Validated (3 fold, repeated 3 times) 
## Summary of sample sizes: 594, 594, 594, 594, 594, 594, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##   2     0.8387579  0.6529203
##   5     0.8376356  0.6522826
##   9     0.8357651  0.6503612
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 2.
```

We can see the accuracy has further decreased (0.8387579). Let us also reduced the number of times that the samples are repeated used in the training (repeat times). Let us see if the sample repeat times reduce to 3, if the accuracy can be further reduced.


```r
# set.seed(396)
# cv.3.folds <- createMultiFolds(rf.label, k = 3, times = 3)
# 
# ctrl.4 <- trainControl(method = "repeatedcv", number = 3, repeats = 3, index = cv.3.folds)
# 
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
# 
# set.seed(9622)
# FT_rf.8.cv.4 <- train(x = rf.train.8, y = rf.label, method = "rf", tuneLength = 3, ntree = 50, trControl = ctrl.4)
# 
# #Shutdown cluster
# stopCluster(cl)
#save(FT_rf.8.cv.4, file = "./data/FT_rf.8.cv.4.rda")
# # # Check out results
load("./data/FT_rf.8.cv.4.rda")
# Check out results
FT_rf.8.cv.4
```

```
## Random Forest 
## 
## 891 samples
##   9 predictor
##   2 classes: '0', '1' 
## 
## No pre-processing
## Resampling: Cross-Validated (3 fold, repeated 3 times) 
## Summary of sample sizes: 594, 594, 594, 594, 594, 594, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##   2     0.8443696  0.6649206
##   5     0.8436214  0.6659881
##   9     0.8368874  0.6531629
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 2.
```

We can see the impact of the training samples (`numbers` and `repeated times`) used on the RF model's accuracy. Among of our 4 trials, it appeared that the settings `ctrl.3`, which is `3-folds and repeated 10 times` has the best accuracy. We could continue the trails until we are satisfied. But we will stop here for computation reasons. 

One of the conclusion we may draw form the exercises we did is that the best sampling should imic the proportion of the training dataset with the testing dataset. Our Titanic datasets have a proportion of (891:418), which is roughly 2:1. so our sampling should match with this proportions. The 3-folds CV partition, using 2 folds to train and 1 fold to test, matches this ratio. So it is reasonable to believe that the best data sampling is the `3-folds repeated 10 times` for our Titanic problem. 

Some of you may notice that we have set different `mtree` values. That is one of the parameters *RF model* used for generating the prediction. We are going to discuss about them next. 

## Tuning Model's Parameters  

Tuning model parameters is a parameter optimization problem [@Brownlee2021].  Depending on the models, the adjustable parameters can be different completely. For example, the decision tree has two adjustable parameters: `complexity parameter (CP)` and `tune length (TL)`. `CP` tells the algorithm to stop when the measure (generally is accuracy) does not improve by this factor. `TL` tells how many instances to use for training. SVM models, as another example, also have two adjustable parameters `cost` and `gamma`. The `cost`, is a parameter that controls the trade-off between the classification of training points and a smooth decision boundary. It suggests the model chooses data points as a support vector. If the value of `cost` is large, then the model choose more data points as a support vector and we get a higher variance and lower bias, which may lead to the problem of overfitting; If the value of `cost` is small, then the model will choose fewer data points as a support vector and get a lower variance and high bias. `Gamma` defines how far the influence of a single training example reaches. If the value of Gamma is high, then the decision boundary will depend on the points close to the decision boundary and the nearer points carry more weights than far away points due to which the decision boundary becomes more wiggly. If the value of `Gamma` is low, then the far-away points carry more weights than the nearer points and thus the decision boundary becomes more like a straight line.

We will continue use *RF model* as an example to demonstrate the parameter tuning process. RF has many parameters that can be adjusted but the two main tuning parameters are **`mtry`** and **`ntree`**.

+ `mtry`: Number of variables randomly selected as testing conditions at each split of decision trees. default value is `sqr(col)`. 
Increasing `mtry` generally improves the performance of the model as each node has a higher number of options to be considered. However, this is not necessarily true as this decreases the diversity of individual trees. At the same time, it will decrease the speed. Hence, it needs to strike the right balance.

+ `ntree`: Number of trees to grow. the default value is 500. A higher number of trees give you better performance but makes your code slower. You should choose as high a value as your processor can handle because this makes your predictions stronger and more stable.

In the rest of the section, we demonstrate the process of using CV to fine-tune RF model's parameters `mtry` and `ntree`. In general, different optimization strategies can be used to find a model's optimal parameters. The two most commonly used methods for RF are **Random search** and **Grid search**.

*Random Search*. Define a search space as a bounded domain of parameter values and randomly sample points in that domain.

*Grid Search*. Define a search space as a grid of parameter values and evaluate every position in the grid.

Let us try them one at a time.

### Random Search {-}

Random search provided by the package `caret` with the method "`rf`" (*Random forest*) in function `train` can only tune parameter `mtry`^[Not all machine learning algorithms are available in caret for tuning. The choice of parameters was decided by the developers of the package. Only those parameters that have a large effect are available for tuning in caret. For the `RF` method, only `mtry` parameter is available in caret for tuning. The reason is its effect on the final accuracy and that it must be found empirically for a dataset]. 

Let us continue using what we have found from the previous sections, that are：

1. model `rf.8` with 9 predictors.
2. *CV* with `3-folds` and `repeat 10 times`.

Let us also fix "`ntree = 500`" and "`tuneLength = 15`", and use `random` search to find `mtry`. 


```r
#library(caret)
#library(doSNOW)
# Random Search
set.seed(2222)
# #use teh best sampling results that is K=3 ant T=10
# cv.3.folds <- createMultiFolds(rf.label, k = 3, times = 10)
# 
# # Set up caret's trainControl object.
# ctrl.1 <- trainControl(method = "repeatedcv", number = 3, repeats = 10, index = cv.3.folds, search="random")
# 
# # set up cluster for parallel computing
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
# 
# # Set seed for reproducibility and train
# set.seed(34324)
# 
# #use rf.train.8 with 9 predictors 
# 
# #RF_Random <- train(x = rf.train.8, y = rf.label, method = "rf", tuneLength = 15, ntree = 500, trControl = ctrl.1)
# #save(RF_Random, file = "./data/RF_Random_search.rda")
# 
# #Shutdown cluster
# stopCluster(cl)

# Check out results
load("./data/RF_Random_search.rda")
print(RF_Random)
```

```
## Random Forest 
## 
## 891 samples
##   9 predictor
##   2 classes: '0', '1' 
## 
## No pre-processing
## Resampling: Cross-Validated (3 fold, repeated 10 times) 
## Summary of sample sizes: 594, 594, 594, 594, 594, 594, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##   2     0.8435466  0.6643066
##   3     0.8453423  0.6690529
##   4     0.8437710  0.6665398
##   5     0.8419753  0.6630091
##   6     0.8397306  0.6586318
##   7     0.8383838  0.6556425
##   8     0.8379349  0.6544327
##   9     0.8353535  0.6495571
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 3.
```
\begin{figure}

{\centering \includegraphics[width=1\linewidth]{11-fine-tune-models_files/figure-latex/plotRandommtry-1} 

}

\caption{The best mtry numbers on model's accuracy produced by `Random` search.}(\#fig:plotRandommtry)
\end{figure}
We can see that the random search for `mtry` has found the best value is 3. When the model uses the parameter `mtry = 3` it can have an accuracy of 84.53%. 

### Grid Search {-}

Grid search is generally searching for more than one parameter. Each axis of the grid is a parameter, and points in the grid are specific combinations of parameters. Because caret train can only tune one parameter, the grid search is now a linear search through a vector of candidate values.


```r
# ctrl.2 <- trainControl(method="repeatedcv", number=3, repeats=10, index = cv.3.folds, search="grid")
# 
# set.seed(3333)
# # set Grid search with a vector from 1 to 15.
# 
# tunegrid <- expand.grid(.mtry=c(1:15))
# 
# # set up cluster for parallel computing
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
# 
# 
# #RF_grid_search <- train(y = rf.label, x = rf.train.8,  method="rf", metric="Accuracy", trControl = ctrl.2, tuneGrid = tunegrid, tuneLength = 15, ntree = 500)
# 
# 
# #Shutdown cluster
# stopCluster(cl)
# #save(RF_grid_search, file = "./data/RF_grid_search.rda")

load("./data/RF_grid_search.rda")
print(RF_grid_search)
```

```
## Random Forest 
## 
## 891 samples
##   9 predictor
##   2 classes: '0', '1' 
## 
## No pre-processing
## Resampling: Cross-Validated (3 fold, repeated 10 times) 
## Summary of sample sizes: 594, 594, 594, 594, 594, 594, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##    1    0.8232323  0.6140400
##    2    0.8439955  0.6652153
##    3    0.8452301  0.6691079
##    4    0.8443322  0.6675864
##    5    0.8428732  0.6645467
##    6    0.8398429  0.6584647
##    7    0.8379349  0.6548634
##    8    0.8390572  0.6571467
##    9    0.8370370  0.6529631
##   10    0.8365881  0.6519263
##   11    0.8359147  0.6504591
##   12    0.8370370  0.6525838
##   13    0.8365881  0.6520535
##   14    0.8356902  0.6502470
##   15    0.8354658  0.6494413
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 3.
```
\begin{figure}

{\centering \includegraphics[width=1\linewidth]{11-fine-tune-models_files/figure-latex/plotGridmtry-1} 

}

\caption{The best mtry numbers on model's accuracy produced by `Grid` search.}(\#fig:plotGridmtry)
\end{figure}
The Grid search method identified the best parameter for `mtry` is also 3. When `mtry = 3`,  the model's estimated accuracy reaches 84.52%.

We can see that both search methods have the same `mtry` suggestions. 

### Manual Search {-}

Let us consider another parameter **`ntree`** in the `RF model`. Since our `train` method from `caret` cannot tune `ntree`, we have to write our own function to search the best value of parameter `ntree`.  This method is also called **Manual Search**. The idea is to write a loop repeating the same model's fitting process a certain number of times. Each time Within a loop, a different value of the parameter to be tuned is used,  and the model's results are accumulated, Finally, a manual comparison is made to figure out what is the best value of the tuned parameter.

To tune the RF model's parameter `ntree`, we set `mtry=3` from the above section and use a list of 4 values (100, 500, 1000, 1500)^[These are generally used `ntree` values. For demonstration purposes we only choose these values, you can try more different values.] and find which one produces the best result. 


```r
# Manual Search we use control 1 random search
model_list <- list()

tunegrid <- expand.grid(.mtry = 3)
control <- trainControl(method="repeatedcv", number=3, repeats=10, search="grid")

# # the following code have been commented out just for produce the markdown file. so it will not wait for ran a long time
# # set up cluster for parallel computing
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
# 
# 
# #loop through different settings
# 
# for (n_tree in c(100, 500, 1000, 1500)) {
#   
#   set.seed(3333)
#   fit <- train(y = rf.label, x = rf.train.8,  method="rf", metric="Accuracy",  tuneGrid=tunegrid, trControl= control, ntree=n_tree)
# 
#   key <- toString(n_tree)
#   model_list[[key]] <- fit
# }
# 
# #Shutdown cluster
# stopCluster(cl)
# save(model_list, file = "./data/RF_manual_search.rda")
# # the above code comneted out for output book file

load("./data/RF_manual_search.rda")
# compare results
results <- resamples(model_list)
summary(results)
```

```
## 
## Call:
## summary.resamples(object = results)
## 
## Models: 100, 500, 1000, 1500 
## Number of resamples: 30 
## 
## Accuracy 
##           Min.   1st Qu.    Median      Mean   3rd Qu.      Max. NA's
## 100  0.7979798 0.8249158 0.8367003 0.8383838 0.8535354 0.8855219    0
## 500  0.8013468 0.8324916 0.8451178 0.8418631 0.8518519 0.8821549    0
## 1000 0.8013468 0.8282828 0.8434343 0.8415264 0.8518519 0.8787879    0
## 1500 0.8013468 0.8324916 0.8451178 0.8430976 0.8518519 0.8855219    0
## 
## Kappa 
##           Min.   1st Qu.    Median      Mean   3rd Qu.      Max. NA's
## 100  0.5686275 0.6277878 0.6498418 0.6540654 0.6778695 0.7539114    0
## 500  0.5751073 0.6439474 0.6681725 0.6614719 0.6854823 0.7462468    0
## 1000 0.5751073 0.6327199 0.6676526 0.6608493 0.6823330 0.7394356    0
## 1500 0.5751073 0.6409731 0.6714760 0.6640467 0.6857492 0.7539114    0
```

```r
#plot 
dotplot(results)
```

\begin{figure}

{\centering \includegraphics[width=1\linewidth]{11-fine-tune-models_files/figure-latex/manulsearch-1} 

}

\caption{The impact of the mtry numbers on model's accuracy.}(\#fig:manulsearch)
\end{figure}

We can see with the default *mtry =3* setting, the best *ntree* value is 1500. The model can reach 84.31% accuracy. 

## Summary {-}

In this chapter, we have demonstrated the process of fine-tuning a prediction model's parameters so to achieve the best performance of the model to eliminate the possible model's overfit. We not only performed the fine-tune of a model's parameters but also demonstrated the process of fine-tune the other two factors that may cause the model's overfitting. They are the train data sampling and the predictors' selection.

We use the RF model as an example, starting from the order of all attributes prediction power, we have figured out the best collection of predictors including the number of predictors and the actual predictors. We've concluded the best predictor list is,

```r
Predictor
```

```
## [1] "Sex, Title, Fare_pp, Ticket_class, Pclass, Ticket, Age, Friend_size, Deck"
```
We also demonstrated the process of training dataset sampling. That is a basic technique used in the smallest dataset. Data sampling has a great impact on the model's performance. We have demonstrated the technique using *k-folds CV*. We have concluded that the best training data sample is **`3-folds with repeats 10 times`**. 

And finally, we demonstrated the methods used to fine-tune a model's parameters. With RF, the only two parameters are: `mtry` and `ntree`. We have illustrated "Random search", "Grid search" and "Manual search" methods and find out the best parameters, based on the fixed predictors and the sampling, are  **`mtry = 3`** and **`ntree = 1500`**. 

Let us use these parameters to produce a model on the training dataset and make a prediction on the test dataset. We can then submit the final result to Kaggle for evaluation. 



```r
set.seed(1234)
tunegrid <- expand.grid(.mtry = 3)
control <- trainControl(method="repeatedcv", number=3, repeats=10, search="grid")

# # # the following code have been commented out just for produce the markdown file. so it will not wait for ran a long time
# # # set up cluster for parallel computing
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
#  
# Final_model <- train(y = rf.label, x = rf.train.8,  method="rf", metric="Accuracy",  tuneGrid=tunegrid, trControl= control, ntree=1500)
# 
# #Shutdown cluster
# stopCluster(cl)
# 
# save(Final_model, file = "./data/Final_model.rda")
# # # the above code commented out for output book file

load("./data/Final_model.rda")

# Make predictions
Prediction_Final <- predict(Final_model, test.submit.df)
#table(Prediction_Final)

# Write out a CSV file for submission to Kaggle
submit.df <- data.frame(PassengerId = test$PassengerId, Survived = Prediction_Final)

write.csv(submit.df, file = "./data/Prediction_Final.csv", row.names = FALSE)
```

We have got a score of 0.76076. Recall that our base model without fine-tune the Kaggle scores was 0.75598. It shows that our RF model has been increased by 0.5 percent. It seems not a lot but the technique and the process are far more important the increase of the accuracy. 

## Exercises {-}

1. Write a function to train Random forest models from a single predictor to full-house predictors to compare models' prediction accuracy and find the best number of predictors and the predictors.

2. Train Random forest models using the number of predictors find from exercise one and find out the best combination of the same number of predictors and the prediction accuracy. 


