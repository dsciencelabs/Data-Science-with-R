# Model Cross Validation




\begin{center}\includegraphics[width=0.8\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/knobs} \end{center}


>
>
>
> "We cannot solve our problems with the same thinking we used 
> when we created them."
>
>
>                                        -- Albert Einstein
>

In the previous two chapters, we have demonstrated how to build prediction models using both *Decision Tree* and *Random Forest*, the two popular prediction models. The models we built have different prediction accuracy. The big problem with the models is their reduced prediction accuracy with the test dataset. An even bigger problem is that the reduction of the prediction accuracy with each model is different and unpredictable. Together they created great difficulty to choose which model should be used for the real applications. 

We are lucky because we have a Kaggle competition that provides us with a test dataset and feedback on our model's performance. In real applications, as the titanic competition simulated, the test dataset has no response variable's (survival status) value. We will have no means to compare to evaluate the model's accuracy. 

Although we may use the methods as we have used in Chapter 8, where we used our model to predict on the training dataset and made a comparison with the original value to estimate the model's prediction accuracy. A similar method (`OOB`) is also used in the random forest models (in Chapter 9) to estimate the model's accuracy. We know that our estimated accuracy is not reliable. 

There is a systematic method in data science to evaluate a prediction model's performance. It is called "Cross-Validation (CV)"\index{Cross Validation (CV)}. In this chapter, we will demonstrate how to use a CV to evaluate a model's performance.
  
## Model's Underfitting and Overfitting

We have experienced problems with both of our decision tree and random forest models. The models have higher estimated accuracy (from the model construction) and much lower accuracy on the test dataset. This Would only mean two things either the prediction model is **overfitting**\index{overfitting} or is **underfitting**\index{underfitting}.

let us quickly look at a very graphic example of underfitting and overfitting. 

\begin{figure}

{\centering \includegraphics[width=1\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/underfit} 

}

\caption{Model's Fitting, Overfitting and Underfitting}(\#fig:modelfit)
\end{figure}

We can see in Figure \@ref(fig:modelfit) where the first model is a straight line (a low variance model: $y$ = $m$ * $x$ + $c$) fails to capture the underlying parabolic curve in the dataset, this is underfitting. At the other extreme, the high degree polynomial (a low bias model) captures too much of the noise at the same time as the underlying parabola and is overfitting. Although it is following the data points provided (i.e. the training dataset), this curve is not transferable to new data (i.e. the test dataset).

Among the models we have produced, the decision tree `model1` with only attribute *Sex* as its predictor is an example of the underfitting model. It has 78.68% estimated accuracy on the training dataset but only has 76.56% accuracy on the test dataset. On the contrary, all our random forest Models have an issue of overfitting.

## General Cross Validation Methods

There are two general CV methods that can be used to validate a prediction model:

1. Single model CV.
2. Multiple models comparison.

### Single Model Cross Validation {-}

The goal of a single model CV is to test the model's ability to predict new data that was not seen and not used in model construction. So, the problem can be spotted like overfitting or selection bias, in addition, it can also give an insight on how the model will generalize to an independent dataset or an unknown dataset.

One round of CV involves partitioning a sample of data into complementary subsets, performing the analysis on one subset (called the *training set*\index{training set}), and validating the analysis on the other subset (called the *validation set*\index{validation set}). To reduce variability, in most methods multiple rounds of CV are performed using different partitions, and the validation results are combined (e.g. averaged) over the rounds to give an estimate of the model's predictive performance.

There are two major cross-validation methods: exhaustive CV and non-exhaustive CV. 

+ **Exhaustive CV** learn and test on all possible ways to divide the original sample into a training and a validation set. **Leave-p-out CV (LpO CV)** is an exhaustive cross-validation method. It involves using $p$ data samples as the validation dataset and the remaining data samples as the training dataset. This is repeated over and over until all possible ways to divide the original data sample into a training and a validation dataset $p$. 

+ **Non-exhaustive cross-validation**, in the contrary, does not compute all the possible ways of splitting the original data sample but still has a certain coverage. **$k$-fold CV** is typical non-exhaustive cross-validation. 

\begin{figure}

{\centering \includegraphics[width=1\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/k-fold} 

}

\caption{K-Folds Cross Validation}(\#fig:k-fold)
\end{figure}
In $k$-fold CV, the original data sample is randomly partitioned into $k$ equal-sized sub-samples. Of the k sub-samples, a single subsample is retained as the validation dataset for testing the model, and the remaining $k$ − 1 sub-samples are used as training data. The CV process is then repeated $k$ times, with each of the $k$ sub-samples used exactly once as the validation data. The $k$ results can then be averaged to produce a single estimation. The advantage of this method over repeated random sub-sampling is that all observations are used for both training and validation, and each observation is used for validation exactly once. `10-fold` CV is commonly used in practice. 

### General Procedure of CV {-}

The general process of Cross-Validation is as follows:

1. Split the entire data randomly into $K$ folds (value of $K$ shouldn’t be too small or too high, ideally we choose 5 to 10 depending on the data size). The higher value of $K$ leads to a less biased model (but large variance might lead to overfitting), whereas the lower value of $K$ is similar to the train-test split approach we saw before.

2. Then fit the model using the $K - 1$ folds and validate the model using the remaining $K$th fold. Note down the scores/errors.

3. Repeat this process until every $K$ fold serves as the test set. Then take the average of your recorded scores. That will be the performance metric for the model.

We will use examples to demonstrate this procedure. 

### Cross Validation on Decision Tree Models {-}

We have produced four decision tree models in Chapter 8. Let us do cross-validation on `model2` and `model3` since they have identical predictors with the random forest `RF_model1` and `RF_model2` which we will do cross-validation later.


```r
library(caret)
library(rpart)
library(rpart.plot)

#read Re-engineered dataset
RE_data <- read.csv("./data/RE_data.csv", header = TRUE)

#Factorize response variable
RE_data$Survived <- factor(RE_data$Survived)

#Separate Train and test data.
train <- RE_data[1:891, ]
test <- RE_data[892:1309, ]

#setup model's train and valid dataset
set.seed(1000)
samp <- sample(nrow(train), 0.8 * nrow(train))
trainData <- train[samp, ]
validData <- train[-samp, ]
```

```r
# set random for reproduction
set.seed(3214)
# specify parameters for cross validation
control <- trainControl(method = "repeatedcv", 
                        number = 10, # number of folds
                        repeats = 5, # repeat times
                        search = "grid")
```
Our cross validation settings are: `10 folds`, and `repeat 5` times, with `Grid` search the optimal parameter. The detailed meaning of each settings refers to http://topepo.github.io/caret/data-splitting.html.

Let us do cross validation (CV) for Tree *model2*,


```r
set.seed(1010)
# Create model from cross validation train data
#  Tree_model2_cv <- train(Survived ~ Sex + Pclass + HasCabinNum + Deck + Fare_pp,
#                       data = trainData,
#                       method = "rpart",
#                       trControl = control)
# # Due to the computation cost once a model is trained. 
# # it is better to save it and load later rather than compute a gain
# save(Tree_model2_cv, file = "./data/Tree_model2_cv.rda")
load("./data/Tree_model2_cv.rda")

print.train(Tree_model2_cv)
```

```
## CART 
## 
## 712 samples
##   5 predictor
##   2 classes: '0', '1' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold, repeated 5 times) 
## Summary of sample sizes: 641, 640, 640, 641, 641, 642, ... 
## Resampling results across tuning parameters:
## 
##   cp          Accuracy   Kappa    
##   0.00887199  0.8253404  0.6003541
##   0.03041825  0.7955233  0.5418574
##   0.43726236  0.6908918  0.2281378
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was cp = 0.00887199.
```

```r
model_accuracy <- format(Tree_model2_cv$results$Accuracy[1], digits = 4)
paste("Estimated accuracy:", model_accuracy)
```

```
## [1] "Estimated accuracy: 0.8253"
```

```r
# Model estimated accuracy is 82.53.
```

Display details of the cross validation model, 


```r
#Visualize cross validation tree
rpart.plot(Tree_model2_cv$finalModel, extra=4)
plot.train(Tree_model2_cv)
```

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{10-model-cross-validation_files/figure-latex/model2CV-1} \includegraphics[width=0.5\linewidth]{10-model-cross-validation_files/figure-latex/model2CV-2} 

}

\caption{Decision Tree CV model2.}(\#fig:model2CV)
\end{figure}
Let us record the model's accuracy on *trainData*, *validData*, and *test* dataset. Remember *trainData* and *validData* are randomly partitioned from the *train* dataset.                                                   

```r
### Access accuracy on different datasets
# prediction's Confusion Matrix on the trainData 
predict_train <-predict(Tree_model2_cv, trainData)
conMat <- confusionMatrix(predict_train, trainData$Survived)
conMat$table
```

```
##           Reference
## Prediction   0   1
##          0 431 104
##          1  18 159
```

```r
# prediction's Accuracy on the trainData 
predict_train_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("trainData Accuracy:", predict_train_accuracy)
```

```
## [1] "trainData Accuracy: 0.8287"
```

```r
# prediction's Confusion Matrix on the validData
predict_valid <-predict(Tree_model2_cv, validData)
conMat <- confusionMatrix(predict_valid, validData$Survived)
conMat$table
```

```
##           Reference
## Prediction  0  1
##          0 93 36
##          1  7 43
```

```r
# prediction's Accuracy on the validData
predict_valid_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("viladData Accuracy:", predict_valid_accuracy)
```

```
## [1] "viladData Accuracy: 0.7598"
```

```r
# predict on test
predict_test <-predict(Tree_model2_cv, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = as.factor(predict_test))
write.csv(submit, file = "./data/Tree_model2_CV.CSV", row.names = FALSE)
# test accuracy 0.75837
paste("Test Accuracy:", 0.7584)
```

```
## [1] "Test Accuracy: 0.7584"
```

```r
# accumulate model's accuracy
name <- c("Esti Accu", "Train Accu", "Valid Accu", "Test Accu")
Tree_model2_CV_accuracy <- c(model_accuracy, predict_train_accuracy, predict_valid_accuracy, 0.7584)
names(Tree_model2_CV_accuracy) <- name
Tree_model2_CV_accuracy
```

```
##  Esti Accu Train Accu Valid Accu  Test Accu 
##   "0.8253"   "0.8287"   "0.7598"   "0.7584"
```
We can see the tree differences from Figure \@ref(fig:tree_model2_CV) and Figure \@ref(fig:tree2). We can also see that despite the model tried the best parameters, the prediction accuracy on the test dataset is dropped from 0.76555 (default decision tree) to 0.75837. It shows that the model construction has reached the best since the change of the tree structure does not increase the accuracy. 

The drop of the accuracy may caused by the reduction of the size of the training dataset. It reflects the second possible cause of the overfitting, that is the size of the training sample. Recall that decision tree `model2` was trained on the `train` dataset and now it is trained on the `trainData`. the later is a random subset of the `train` dataset and only has 80 percent of the data samples. That is to say, the smaller of the training dataset the more chance of the inaccurate prediction accuracy on the test dataset (overfitting or underfitting).

Let us do cross validation on tree *model3*, 


```r
# CV on model3
set.seed(1234)
# Tree_model3_cv <- train(Survived ~ Sex + Fare_pp + Pclass + Title + Age_group + Group_size + Ticket_class  + Embarked,
# 
#                        data = trainData,
#                        method = "rpart",
#                        trControl = control)
# 
# save(Tree_model3_cv, file = "./data/Tree_model3_cv.rda")
load("./data/Tree_model3_cv.rda")
# show model details
print.train(Tree_model3_cv)
```

```
## CART 
## 
## 712 samples
##   8 predictor
##   2 classes: '0', '1' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold, repeated 5 times) 
## Summary of sample sizes: 641, 641, 641, 641, 641, 641, ... 
## Resampling results across tuning parameters:
## 
##   cp          Accuracy   Kappa    
##   0.03802281  0.8209755  0.6124019
##   0.05323194  0.7934831  0.5627884
##   0.42585551  0.6979987  0.2637528
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was cp = 0.03802281.
```

```r
# record accuracy
model_accuracy <- format(Tree_model3_cv$results$Accuracy[1], digits = 4)
paste("Estimated accuracy:", model_accuracy)
```

```
## [1] "Estimated accuracy: 0.821"
```

```r
# accuracy is 0.82.
```
Visualize model,


```r
#Visualize cross validation tree
rpart.plot(Tree_model3_cv$finalModel, extra=4)
plot.train(Tree_model3_cv)
```

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{10-model-cross-validation_files/figure-latex/treemodel3CV-1} \includegraphics[width=0.5\linewidth]{10-model-cross-validation_files/figure-latex/treemodel3CV-2} 

}

\caption{Decision Tree CV model3.}(\#fig:treemodel3CV)
\end{figure}
Record model's accuracy,


```r
### Access accuracy on different datasets
# prediction's Confusion Matrix on the trainData 
predict_train <-predict(Tree_model3_cv, trainData)
conMat <- confusionMatrix(predict_train, trainData$Survived)
conMat$table
```

```
##           Reference
## Prediction   0   1
##          0 387  61
##          1  62 202
```

```r
# prediction's Accuracy on the trainData 
predict_train_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("trainData Accuracy:", predict_train_accuracy)
```

```
## [1] "trainData Accuracy: 0.8272"
```

```r
# prediction's Confusion Matrix on the validData
predict_valid <-predict(Tree_model3_cv, validData)
conMat <- confusionMatrix(predict_valid, validData$Survived)
conMat$table
```

```
##           Reference
## Prediction  0  1
##          0 90 24
##          1 10 55
```

```r
# prediction's Accuracy on the validData 
predict_valid_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("validData Accuracy:", predict_valid_accuracy)
```

```
## [1] "validData Accuracy: 0.8101"
```

```r
#predict on test
predict_test <-predict(Tree_model3_cv, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = as.factor(predict_test))
write.csv(submit, file = "./data/Tree_model3_CV.CSV", row.names = FALSE)

## test accuracy is 0.77751
paste("Test Accuracy:", 0.7775)
```

```
## [1] "Test Accuracy: 0.7775"
```

```r
# accumulate model's accuracy
Tree_model3_CV_accuracy <- c(model_accuracy, predict_train_accuracy, predict_valid_accuracy, 0.7775)
names(Tree_model3_CV_accuracy) <- name
Tree_model3_CV_accuracy
```

```
##  Esti Accu Train Accu Valid Accu  Test Accu 
##    "0.821"   "0.8272"   "0.8101"   "0.7775"
```
The results show a consistent prediction accuracy. The accuracy of the test dataset has been increased from 0.77033 (Tree model3) to 0.7775. The point perhaps is that the increase of predictors does improve the accuracy (so far). 

Based on the two cross-validations we have done to the two decision tree models: `model2` and `model3`, we can conclude that the decision tree model's default settings are nearly their best. This is because after the cross-validations with `10 folds` and `repeat 5 times` and `Grid` search have been carried, we did not manage to improve the models' accuracy.   

### Cross Validation on Random Forest Models {-}

Now, Let us try the same cross validation on the two random forest models constructed in Chapter 9.


```r
# set seed for reproduction
set.seed(2307)
RF_model1_cv <- train(Survived ~ Sex + Pclass + HasCabinNum +      Deck + Fare_pp,
                       data = trainData,
                       method = "rf",
                       trControl = control)
save(RF_model1_cv, file = "./data/RF_model1_cv.rda")
load("./data/RF_model1_cv.rda")

# Show CV mdoel's details
print(RF_model1_cv)
```

```
## Random Forest 
## 
## 712 samples
##   5 predictor
##   2 classes: '0', '1' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold, repeated 5 times) 
## Summary of sample sizes: 641, 642, 640, 641, 641, 641, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##    2    0.7615079  0.4382518
##    7    0.8401691  0.6412818
##   12    0.8284042  0.6263444
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 7.
```

```r
print(RF_model1_cv$results)
```

```
##   mtry  Accuracy     Kappa AccuracySD    KappaSD
## 1    2 0.7615079 0.4382518 0.05265318 0.13042865
## 2    7 0.8401691 0.6412818 0.04046572 0.09332728
## 3   12 0.8284042 0.6263444 0.04617175 0.10032573
```

```r
# Record model's accuracy
model_accuracy <- format(RF_model1_cv$results$Accuracy[2], digits = 4)
paste("Estimated accuracy:", model_accuracy)
```

```
## [1] "Estimated accuracy: 0.8402"
```
We can see that the best model parameters are `mtry = 7` and `ntree = 500`, The trained model's best accuracy is 83.88%.

Let us verify on validate dataset and make prediction on the test dataset. 

```r
### Access accuracy on different datasets
# prediction's Confusion Matrix on the trainData 
predict_train <-predict(RF_model1_cv, trainData)
conMat <- confusionMatrix(predict_train, trainData$Survived)
conMat$table
```

```
##           Reference
## Prediction   0   1
##          0 432  69
##          1  17 194
```

```r
# prediction's Accuracy on the trainData 
predict_train_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("trainData Accuracy:", predict_train_accuracy)
```

```
## [1] "trainData Accuracy: 0.8792"
```

```r
# prediction's Confusion Matrix on the validData
predict_valid <-predict(RF_model1_cv, validData)
conMat <- confusionMatrix(predict_valid, validData$Survived)
conMat$table
```

```
##           Reference
## Prediction  0  1
##          0 88 34
##          1 12 45
```

```r
# prediction's Accuracy on the validData 
predict_valid_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("validData Accuracy:", predict_valid_accuracy)
```

```
## [1] "validData Accuracy: 0.743"
```

```r
# predict on test
predict_test <-predict(RF_model1_cv, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = as.factor(predict_test))
write.csv(submit, file = "./data/RF_model1_CV.CSV", row.names = FALSE)

## test accuracy 0.74641
paste("Test Accuracy:", 0.7464)
```

```
## [1] "Test Accuracy: 0.7464"
```

```r
# accumulate model's accuracy
RF_model1_cv_accuracy <- c(model_accuracy, predict_train_accuracy, predict_valid_accuracy, 0.7464)
names(RF_model1_cv_accuracy) <- name
RF_model1_cv_accuracy
```

```
##  Esti Accu Train Accu Valid Accu  Test Accu 
##   "0.8402"   "0.8792"    "0.743"   "0.7464"
```
The `trainData` set was randomly selected 80 percent of train dataset, the random forest parameter was set to `mtry = 7`, `ntree = 500` and the cross-validation settings were `fold = 10` and `repeats= 5`. They all combined together trained a CV model. The model's prediction accuracy is pretty bad with 74.6% on the test dataset. The random forest model `RF_model1` using the same predictors and the default random forest settings (`mtry = 1`, `ntree = 500`), trained on no split train dataset has a prediction accuracy of 0.76555.

Let us try on random forest `model2`,

```r
# set seed for reproduction
set.seed(2300)

# RF_model2_cv <- train(Survived ~ Sex + Fare_pp + Pclass + Title + Age_group + Group_size + Ticket_class + Embarked,
#                        data = trainData, 
#                        method = "rf", 
#                        trControl = control)
# # This model will be used in chapter 12. so it is saved into a file for late to be loaded
# save(RF_model2_cv, file = "./data/RF_model2_cv.rda")

load("./data/RF_model2_cv.rda")

# Show CV mdoel's details
print(RF_model2_cv)
```

```
## Random Forest 
## 
## 712 samples
##   8 predictor
##   2 classes: '0', '1' 
## 
## No pre-processing
## Resampling: Cross-Validated (10 fold, repeated 5 times) 
## Summary of sample sizes: 641, 640, 642, 641, 640, 641, ... 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##    2    0.8105369  0.5725714
##   17    0.8404408  0.6518406
##   32    0.8312040  0.6320845
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 17.
```

```r
print(RF_model2_cv$results)
```

```
##   mtry  Accuracy     Kappa AccuracySD   KappaSD
## 1    2 0.8105369 0.5725714 0.04319425 0.1018195
## 2   17 0.8404408 0.6518406 0.04065381 0.0900002
## 3   32 0.8312040 0.6320845 0.04514800 0.1001118
```

```r
# Record model's accuracy
mode2_accuracy <- format(RF_model2_cv$results$Accuracy[2], digits = 4)
paste("Estimated accuracy:", mode2_accuracy)
```

```
## [1] "Estimated accuracy: 0.8404"
```
Let us calculate model's accuracy,

```r
### Access accuracy on different datasets
# prediction's Confusion Matrix on the trainData 
predict_train <-predict(RF_model2_cv, trainData)
conMat <- confusionMatrix(predict_train, trainData$Survived)
conMat$table
```

```
##           Reference
## Prediction   0   1
##          0 441  19
##          1   8 244
```

```r
# prediction's Accuracy on the trainData 
predict_train_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("trainData Accuracy:", predict_train_accuracy)
```

```
## [1] "trainData Accuracy: 0.9621"
```

```r
# prediction's Confusion Matrix on the validData
predict_valid <-predict(RF_model2_cv, validData)
conMat <- confusionMatrix(predict_valid, validData$Survived)
conMat$table
```

```
##           Reference
## Prediction  0  1
##          0 86 30
##          1 14 49
```

```r
# prediction's Accuracy on the validData 
predict_valid_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("validData Accuracy:", predict_valid_accuracy)
```

```
## [1] "validData Accuracy: 0.7542"
```

```r
#predict on test
predict_test <-predict(RF_model2_cv, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = as.factor(predict_test))
write.csv(submit, file = "./data/RF_model2_CV.CSV", row.names = FALSE)

## test accuracy 0.75119
paste("Test Accuracy:", 0.7512)
```

```
## [1] "Test Accuracy: 0.7512"
```

```r
# accumulate model's accuracy
RF_model2_cv_accuracy <- c(mode2_accuracy, predict_train_accuracy, predict_valid_accuracy, 0.7512)
names(RF_model2_cv_accuracy) <- name
RF_model2_cv_accuracy
```

```
##  Esti Accu Train Accu Valid Accu  Test Accu 
##   "0.8404"   "0.9621"   "0.7542"   "0.7512"
```

We have used `10 folds` and `repeated 5 times` cross-validation with 80% of the train dataset to build and validate 4 models: two from *decision tree* and two from *random forest*. The different accuracy measurements with different datasets have been collected. Let us put them into one table and plot them in the graph, so we can make a comparison. 


```r
library(tidyr)
Model <- c("Tree_Modlel2","Tree_Model3","RF_model1","RF_model2")

# Show individual models' accuracy
Tree_model2_CV_accuracy
```

```
##  Esti Accu Train Accu Valid Accu  Test Accu 
##   "0.8253"   "0.8287"   "0.7598"   "0.7584"
```

```r
Tree_model3_CV_accuracy
```

```
##  Esti Accu Train Accu Valid Accu  Test Accu 
##    "0.821"   "0.8272"   "0.8101"   "0.7775"
```

```r
RF_model1_cv_accuracy
```

```
##  Esti Accu Train Accu Valid Accu  Test Accu 
##   "0.8402"   "0.8792"    "0.743"   "0.7464"
```

```r
RF_model2_cv_accuracy
```

```
##  Esti Accu Train Accu Valid Accu  Test Accu 
##   "0.8404"   "0.9621"   "0.7542"   "0.7512"
```

```r
#preparee for table construction
Pre <- c("Sex, Pclass, HasCabinNum, Deck, Fare_pp", "Sex, Fare_pp, Pclass, Title, Age_group, Group_size, Ticket_class, Embarked", "Sex, Pclass, HasCabinNum, Deck, Fare_pp", "Sex, Fare_pp, Pclass, Title, Age_group, Group_size, Ticket_class, Embarked")
#
Learn <- c(as.numeric(Tree_model2_CV_accuracy[1])*100, as.numeric(Tree_model3_CV_accuracy[1])*100, as.numeric(RF_model1_cv_accuracy[1])*100, as.numeric(RF_model2_cv_accuracy[1])*100)
#
Train <- c(as.numeric(Tree_model2_CV_accuracy[2])*100, as.numeric(Tree_model3_CV_accuracy[2])*100, as.numeric(RF_model1_cv_accuracy[2])*100, as.numeric(RF_model2_cv_accuracy[2])*100)
#
Valid <- c(as.numeric(Tree_model2_CV_accuracy[3])*100, as.numeric(Tree_model3_CV_accuracy[3])*100, as.numeric(RF_model1_cv_accuracy[3])*100, as.numeric(RF_model2_cv_accuracy[3])*100)
#
Test <- c(as.numeric(Tree_model2_CV_accuracy[4])*100, as.numeric(Tree_model3_CV_accuracy[4])*100, as.numeric(RF_model1_cv_accuracy[4])*100, as.numeric(RF_model2_cv_accuracy[4])*100)

# Construct Dataframe for table and plot
df1 <- data.frame(Model, Pre, Learn, Train, Valid, Test)
df2 <- data.frame(Model, Learn, Train, Valid, Test)
```

```r
# show in table
knitr::kable(df1, longtable = TRUE, booktabs = TRUE, digits = 2, col.names =c("Models", "Predictors", "Accuracy on Learn", "Accuracy on Train", "Accuracy on Valid",  "Accuracy on Test"), 
  caption = 'The Comparision among 4 CV models'
)
```


\begin{longtable}[t]{llrrrr}
\caption{(\#tab:CVmodelcom)The Comparision among 4 CV models}\\
\toprule
Models & Predictors & Accuracy on Learn & Accuracy on Train & Accuracy on Valid & Accuracy on Test\\
\midrule
Tree\_Modlel2 & Sex, Pclass, HasCabinNum, Deck, Fare\_pp & 82.53 & 82.87 & 75.98 & 75.84\\
Tree\_Model3 & Sex, Fare\_pp, Pclass, Title, Age\_group, Group\_size, Ticket\_class, Embarked & 82.10 & 82.72 & 81.01 & 77.75\\
RF\_model1 & Sex, Pclass, HasCabinNum, Deck, Fare\_pp & 84.02 & 87.92 & 74.30 & 74.64\\
RF\_model2 & Sex, Fare\_pp, Pclass, Title, Age\_group, Group\_size, Ticket\_class, Embarked & 84.04 & 96.21 & 75.42 & 75.12\\
\bottomrule
\end{longtable}
Plot results in bar chat.

```r
df.long <- gather(df2, Dataset, Accuracy, -Model, factor_key =TRUE)
ggplot(data = df.long, aes(x = Model, y = Accuracy, fill = Dataset)) +
  geom_col(position = position_dodge()) 
```

![(\#fig:CVmodelcompare)Cross valid models' accuracy on model learning, Traindata dataset. Validdata and Test dataset.](10-model-cross-validation_files/figure-latex/CVmodelcompare-1.pdf) 

From the cross-validation results, we can conclude that:

1. Both decision tree and random forest models default settings are good settings. Despite the dynamic search for the best parameters, the changes in the parameter settings do not affect the prediction accuracy much. So both default settings for the prediction model are acceptable.
2. Change of training dataset for model building from *train dataset* to its subset *trainData*, in `10-fold` and `5-repeat` cross-validation settings, does not change the order of models' performance in terms of decision tree and random forest. It, however, when considering a single model, does suggest that the number of samples used for a model construction has an impact on the model's prediction results. 
3. It is clear that the random forest models have overfitting. 
4. It does not provide a conclusive result that a decision tree is better than a random forest or vice versa. 

A general rule seems to suggest that, the more predictors and the more samples used in a model's construction (training), the more likely the model will suffer from overfitting and vice versa. 

Therefore to choose a model for real prediction (production), we should choose the model that has the smallest accuracy decrease from the model's training to its verification by the cross-validation. 

## Multiple Models Comparison

Multiple model comparison is also called *Cross Model Validation*\index{Cross Model Validation}. Here the model refers to completely different algorithms. The idea is to use multiple models constructed from the same training dataset and validated using the same verification dataset to find out the performance of the different models.

We have already used the technique to compare our decision tree models and random forest models. Cross-model verification has a broader meaning that refers to the comparison between different models produced by the different algorithms or completely different approaches such as decision tree against random forest or decision tree against Support Vector Machine (SVM).

To demonstrate cross model validation, let us produce a few more models with completely different algorithms with the same predictors as much as possible. Let us use *Sex*, *Fare_pp*, *Pclass*, *Title*, *Age_group*, *Group_size*, *Ticket_class*, *Embarked* as predictors.

### Regression Model for Titanic {-}

Logistic regression is a classification, not a regression algorithm. It predicts the probability of occurrence of an event by fitting data to a logit function. Hence, it is also known as logit regression [@analyticsvidhya2015]. Since it predicts the probability, its output values lie between 0 and 1, we can simply separate (or normalise) them by setting a threshold like (> 0.5).


```r
LR_Model <- glm(formula = Survived ~ Pclass + Title + Sex + Age_group + Group_size + Ticket_class  + Fare_pp + Embarked, family = binomial, data = trainData)

#summary(LR_Model_CV)
### Validate on trainData
Valid_trainData <- predict(LR_Model, newdata = trainData, type = "response") #prediction threshold
Valid_trainData <- ifelse(Valid_trainData > 0.5, 1, 0)  # set binary 
#produce confusion matrix
confusion_Mat<- confusionMatrix(as.factor(trainData$Survived),as.factor(Valid_trainData))

# accuracy on traindata
Regression_Acc_Train <- round(confusion_Mat$overall["Accuracy"]*100,2)
paste('Model Train Accuracy =', Regression_Acc_Train)
```

```
## [1] "Model Train Accuracy = 84.97"
```

```r
### Validate on validData
validData_Survived_predicted <- predict(LR_Model, newdata = validData, type = "response")  
validData_Survived_predicted  <- ifelse(validData_Survived_predicted  > 0.5, 1, 0)  # set binary prediction threshold
conMat<- confusionMatrix(as.factor(validData$Survived),as.factor(validData_Survived_predicted))

Regression_Acc_Valid <-round(conMat$overall["Accuracy"]*100,2)
paste('Model Valid Accuracy =', Regression_Acc_Valid) 
```

```
## [1] "Model Valid Accuracy = 79.89"
```

```r
### produce a prediction on test data
library(pROC)
auc(roc(trainData$Survived,Valid_trainData))  # calculate AUROC curve
```

```
## Area under the curve: 0.832
```

```r
#predict on test
test$Survived <- predict(LR_Model, newdata = test, type = "response")  
test$Survived <- ifelse(test$Survived > 0.5, 1, 0)  # set binary prediction threshold
submit <- data.frame(PassengerId = test$PassengerId, Survived = as.factor(test$Survived))

write.csv(submit, file = "./data/LG_model1_CV.CSV", row.names = FALSE)
# Kaggle test accuracy score:0.76555

# record accuracy
Regr_Acc <- c(Regression_Acc_Train, Regression_Acc_Valid, 0.76555)

acc_names <- c("Train Accu", "Valid Accu", "Test Accu")
names(Regr_Acc) <- acc_names
Regr_Acc
```

```
## Train Accu Valid Accu  Test Accu 
##   84.97000   79.89000    0.76555
```

### Support Vector Machine Model for Titanic {-}

Let us also consider a support vector machine (SVM)\index{support vector machine} model [@Cortes1995]. We use the *C-classification* mode. Again, we fit a model with the same set of attributes as in the *logistic regression model*. We use function `svm()` from the `e1071` package (https://cran.r-project.org/web/packages/e1071/e1071.pdf).

We could try to tune the two parameters of the SVM model `gamma` & `cost`, find and select the best parameters (see exercise). 

We then use the best model to make predictions. The results of the model are collected for comparison.


```r
#load library
library(e1071)

# fit the model using default parameters
SVM_model<- svm(Survived ~ Pclass + Title + Sex + Age_group + Group_size + Ticket_class + Fare_pp + Deck + HasCabinNum + Embarked, data=trainData, kernel = 'radial', type="C-classification")

#summary(SVM_model)
### Validate on trainData
Valid_trainData <- predict(SVM_model, trainData) 
#produce confusion matrix
confusion_Mat<- confusionMatrix(as.factor(trainData$Survived), as.factor(Valid_trainData))

# output accuracy
AVM_Acc_Train <- round(confusion_Mat$overall["Accuracy"]*100,4)
paste('Model Train Accuracy =', AVM_Acc_Train)
```

```
## [1] "Model Train Accuracy = 84.4101"
```

```r
### Validate on validData
validData_Survived_predicted <- predict(SVM_model, validData) #produce confusion matrix 
conMat<- confusionMatrix(as.factor(validData$Survived), as.factor(validData_Survived_predicted))
# output accuracy
AVM_Acc_Valid <- round(conMat$overall["Accuracy"]*100,4)
paste('Model Valid Accuracy =', AVM_Acc_Valid) 
```

```
## [1] "Model Valid Accuracy = 78.2123"
```

```r
### make prediction on test
# SVM failed to produce a prediction on test because test has Survived col and it has value NA. A work around is assign it with a num like 1.
test$Survived <-1

# predict results on test
Survived <- predict(SVM_model, test)
solution <- data.frame(PassengerId=test$PassengerId, Survived =Survived)
write.csv(solution, file = './data/svm_predicton.csv', row.names = F)

# prediction accuracy on test 
SVM_Acc <- c(AVM_Acc_Train, AVM_Acc_Valid, 0.78947)
names(SVM_Acc) <- acc_names

# print out
SVM_Acc
```

```
## Train Accu Valid Accu  Test Accu 
##   84.41010   78.21230    0.78947
```

### Neural Network Models {-}
 
Neural networks\index{Neural networks} are a rapidly developing paradigm for information processing based loosely on how neurons in the brain process information. A neural network consists of multiple layers of nodes, where each node performs a unit of computation and passes the result onto the next node. Multiple nodes can pass inputs to a single node and vice versa.

The neural network also contains a set of weights, which can be refined over time as the network learns from sample data. The weights are used to describe and refine the connection strengths between nodes. 

Neural Network with one hidden layer utilizing all features.


```r
# load library
library(nnet)

# train the model
xTrain = train[ , c("Survived", "Pclass","Title", "Sex","Age_group","Group_size", "Ticket_class", "Fare_pp", "Deck", "HasCabinNum", "Embarked")]

NN_model1 <- nnet(Survived ~ ., data = xTrain, size=10, maxit=500, trace=FALSE)

#How do we do on the training data?
nn_pred_train_class = predict(NN_model1, xTrain, type="class" )  # yields "0", "1"
nn_train_pred = as.numeric(nn_pred_train_class ) #transform to 0, 1
confusion_Mat<-confusionMatrix(as.factor(nn_train_pred), train$Survived)
# output accuracy
NN_Acc_Train <- round(confusion_Mat$overall["Accuracy"]*100,4)
paste('Model Train Accuracy =', NN_Acc_Train)
```

```
## [1] "Model Train Accuracy = 89.2256"
```

```r
#How do we do on the valid data?
nn_pred_valid_class = predict(NN_model1, validData, type="class" )  # yields "0", "1"
nn_valid_pred = as.numeric(nn_pred_valid_class ) #transform to 0, 1
confusion_Mat<-confusionMatrix(as.factor(nn_valid_pred), validData$Survived)
# output accuracy
NN_Acc_Valid <- round(confusion_Mat$overall["Accuracy"]*100,4)
paste('Model valid Accuracy =', NN_Acc_Valid)
```

```
## [1] "Model valid Accuracy = 87.7095"
```

```r
#make a prediction on test data
nn_pred_test_class = predict(NN_model1, test, type="class" )  # yields "0", "1"
nn_pred_test = as.numeric(nn_pred_test_class ) #transform to 0, 1
solution <- data.frame(PassengerId=test$PassengerId, Survived = nn_pred_test)
write.csv(solution, file = './data/NN_predicton.csv', row.names = F)

###
# 0.8934,0.8547, 0.71052
NN_Acc <- c(NN_Acc_Train, NN_Acc_Valid, 0.71052)
names(NN_Acc) <- acc_names
NN_Acc
```

```
## Train Accu Valid Accu  Test Accu 
##   89.22560   87.70950    0.71052
```

### Comparision among Different Models {-}

Let us compare the different models we have produced and see which one has a better prediction accuracy on the test dataset. We will use our best prediction accuracy on the test dataset for decision tree and random forest models.


```r
library(tidyr)
Model <- c("Regression","SVM","NN", "Decision tree", "Random Forest")
Train <- c(Regression_Acc_Train, AVM_Acc_Train, NN_Acc_Train, 82.72, 83.16)
Valid <- c(Regression_Acc_Valid, AVM_Acc_Valid, NN_Acc_Valid, 81.01, 92)
Test <- c(76.56, 78.95, 71.05, 77.75, 78.95)
df1 <- data.frame(Model, Train, Valid, Test)

knitr::kable(df1, longtable = TRUE, booktabs = TRUE, digits = 2, col.names =c("Models", "Accuracy on Train", "Accuracy on Valid","Accuracy on Test"), 
  caption = 'The Comparision among 3 Machine Learning Models'
)
```


\begin{longtable}[t]{lrrr}
\caption{(\#tab:Tabmodelcompare)The Comparision among 3 Machine Learning Models}\\
\toprule
Models & Accuracy on Train & Accuracy on Valid & Accuracy on Test\\
\midrule
Regression & 84.97 & 79.89 & 76.56\\
SVM & 84.41 & 78.21 & 78.95\\
NN & 89.23 & 87.71 & 71.05\\
Decision tree & 82.72 & 81.01 & 77.75\\
Random Forest & 83.16 & 92.00 & 78.95\\
\bottomrule
\end{longtable}

```r
df.long <- gather(df1, Dataset, Accuracy, -Model, factor_key =TRUE)
ggplot(data = df.long, aes(x = Model, y = Accuracy, fill = Dataset)) +
  geom_col(position = position_dodge()) 
```

![(\#fig:RFmodelcompare)Accuracy comparision among differnt Machine Leanring models.](10-model-cross-validation_files/figure-latex/RFmodelcompare-1.pdf) 
From the above table and plot, we can see that multiple models cross-validation does not provide a conclusive answer on which model to use for real applications or production. Ideally, we would choose the model that has higher accuracy on *trainData* and *validData*. From the table \@ref(tab:Tabmodelcompare), we should choose model *NN* since it has the highest train accuracy (91.13%) and the second highest validation accuracy (87.71%), however, it has the lowest test accuracy (71.05%). Another possible logic would be to choose the highest validation accuracy and ignore the train accuracy, in this case, we would choose *Random Forest* model since it has the highest validation accuracy (92.00%), and the highest test accuracy among the models (78.95%). However, the *SVM* model also has 78.95% test accuracy but its validation accuracy is the lowest. 

It reveals an unpleasant fact - there is no model which be a certainty that its performance on unseen data can be ensured by the cross validation. The CV can only be used to spot and discover problems but not solutions. 

## Summary {-}

Remember the initial motivation of introducing *Cross Validation* was to identify the overfitting of a prediction model so it should not be used for the real application. To identify overfiting, a single model CV is sufficient. After split the training dataset into `trainData` and `validData`, the model's overfitting problem can be clearly seen with the `validData`. For example, Random forest `model4`, in table \@ref(tab:CVmodelcom), has 83.99% estimated accuracy on the model's construction and 96.63% of accuracy on the train dataset,	but it only has 77.09% accuracy on the validation dataset which the model does not see before. This big drop on the accuracy indicates that the overfitting of the model. It is confirmed by the worsen prediction accuracy on the test dataset, which was 75.12%. 

Cross-validation with multiple models was expected to provide a conclusive judgment of a model whether it is the best or not. It seems that it can provide some evidence but it does not provide a definite conclusion. Perhaps it approved the point that each model has its own conditions and cases of usage. In case of multiple models can all be used for a problem, the individual model's performance can be affected by the data samples and the model's parameters, even the combination of the two together. In data science, there is a way to further dill down on this issue is called **model's fine tune**. That is what we are going to discuss in the next Chapter. 

## Exercises {-}

1. Tune SVM models using `e1071` package. There are two parameters gamma & cost. Using tune.svm() to tune the model and find the best values for gamma & cost. 


2. Try different models with Neural Networks. It should have a better prediction accuracy than the one we have produced.





