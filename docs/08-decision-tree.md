# Prediction with Decision Trees




\begin{center}\includegraphics[width=0.6\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/tree} \end{center}

Model building is the main task of any data science project after understood data, processed some attributes, and analysed the attributes' correlations and the individual's prediction power. As described in the previous chapters. There are many ways to build a prediction model. In this chapter, we will demonstrate to build a prediction model with the most simple algorithm - **Decision tree**.  

A decision tree\index{decision tree} is a commonly used classification model, which is a flowchart-like tree structure. In a decision tree, each internal node (non-leaf node) denotes a test on an attribute, each branch represents an outcome of the test, and each leaf node (or terminal node) holds a class label. The topmost node in a tree is the root node. A typical decision tree is shown in Figure \@ref(fig:decisiontree). 

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/decision tree} 

}

\caption{An example of decision tree}(\#fig:decisiontree)
\end{figure}

It represents the concept buys_computer, that is, it predicts whether a customer is likely to buy a computer or not. '`yes`' is likely to buy, and '`no`' is unlikely to buy. Internal nodes are denoted by *rectangles*, they are test conditions, and leaf nodes are denoted by *ovals*, which are the final predictions. Some decision trees produce binary trees where each internal node branches to exactly two other nodes. Others can produce non-binary trees, like `age?` in the above tree has three branches. 

A decision tree is built by a process called tree *induction*, which is the learning or construction of decision trees from a class-labelled training dataset. Once a decision tree has been constructed, it can be used to classify a test dataset, which is also called *deduction*. 

The deduction process is Starting from the root node of a decision tree, we apply the test condition to a record or data sample and follow the appropriate branch based on the outcome of the test. This will lead us either to another internal node, for which a new test condition is applied or to a leaf node. The class label associated with the leaf node is then assigned to the record or the data sample. For example, to predict a new data input with `'age=senior'` and `'credit_rating=excellent'`, traverse starting from the root goes to the most right side along the decision tree and reaches a leaf `yes`, which is indicated by the dotted line in the figure \@ref(fig:decisiontree). 

Build a decision tree classifier needs to make two decisions:

1) which attributes to use for test conditions?
2) and in what order? 

Answering these two questions differently forms different decision tree algorithms. Different decision trees can have different prediction accuracy on the test dataset. Some decision trees are more accurate and cheaper to run than others. Finding the optimal tree is computationally expensive and sometimes is impossible because of the exponential size of the search space. In real practice, it is often to seek efficient algorithms, that are reasonably accurate and only compute in a reasonable amount of time. Hunt’s\index{Hunt’s Algorithm}, ID3\index{ID3}, C4.5\index{C4.5} and CART algorithms\index{CART algorithms} are all of this kind of algorithms for classification. The common feature of these algorithms is that they all employ a greedy strategy as demonstrated in the **Hunt's algorithm**.

## Decision Tree in Hunt's Algorithm 

Hunt's algorithm builds a decision tree in a recursive fashion by partitioning the training dataset into successively *purer* subsets. Hunt's algorithm takes three input values:

1.  A	training dataset, $D$ with a number of attributes, 
2.  A subset of attributes $Att_{list}$ and its testing criterion together to form a `'test condition'`, such as `'age>=25'` is a test condition, where, `'age'` is the attribute and `'>=25'` is the test criterion.
3.	A `Attribute_selection_method`, it refers a procedure to determine the best splitting.

The general recursive procedure is defined as below [@Tan2005]:

1. Create a node $N$, suppose the training dataset when reach to note $N$ is $D_{N}$. Initially, $D_{N}$ is the entire training set $D$.  Do the following: 
2. If $D_{t}$ contains records that belong the same class $y_{t}$, then $t$ is a leaf node labelled as $y_{t}$;
3. If $D_{t}$ is not an empty set but $Att_{list}$ is empty, (there is no more test attributes left untested), then $t$ is a leaf node labelled by the label of the majority records in the dataset;
4. If $D_{t}$ contains records that belong to more than one class and $Att_{list}$ is not empty, use `Attribute_selection_method` to choose next **best attribute** from the $Att_{list}$ and remove that list from $Att_{list}$. use the attribute and its condition as next test condition. 
5. Repeat steps 2,3 and 4 until all the records in the subset belong to the same class.

There are two fundamental problems that need to be sorted before Hunt's algorithm can work:

1. How to form a 'test condition'? particularly when non-binary attributes exist?
2. How to define the 'best test conditions', so very loop the best test condition can be used in a decision tree? 

### How to Form a Test Condition? {- #test_condition}

Decision tree algorithms must provide a method for expressing a test condition and its corresponding outcomes for different attribute types.

1. Binary Attributes. The test condition for a binary attribute is simple because it only generates two potential outcomes, as shown in figure \@ref(fig:sex).

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/sex} 

}

\caption{Test condition for binary attributes.}(\#fig:sex)
\end{figure}
2. No-binary attributes. No-binary attributes depend on the types of nominal or ordinal, it can have different ways of the split. A nominal attribute can have many values, its test condition can be expressed in two ways, as shown in Figure \@ref(fig:age2). For a multiway split, see Figure \@ref(fig:age2)(a), the number of outcomes depends on the number of distinct values for the corresponding attribute. For example, if an attribute such as age has three distinct values: youth, m_aged, or senior. Its test condition will produce a three-way split or a binary split. Figure \@ref(fig:age2)(b) illustrates three different ways of grouping the attribute values for age into two subsets.


\begin{center}\includegraphics[width=0.5\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/age1} \end{center}
\begin{figure}

{\centering \includegraphics[width=1\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/age2} 

}

\caption{Test condition for no-binary attributes.}(\#fig:age2)
\end{figure}

3. Continuous Attributes. For continuous attributes, the test condition can be constructed as a comparison test $(A<v)$ or $(A≥v)$ with binary outcomes, or a range query with outcomes of the form $v_i≤A<v_{i+1}$, For $i=1,…,k$. The difference between these approaches is shown in Figure \@ref(fig:fare). For the binary case, the decision tree algorithm must consider all possible split positions v, and it selects the one that produces the best partition. For the multiway split, the algorithm must consider all possible ranges of continuous values. 

\begin{figure}

{\centering \includegraphics[width=1\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/Fare} 

}

\caption{Test condition for continuous attributes.}(\#fig:fare)
\end{figure}
### How to Determine the Best Split Condition? {- #best_split}

The method used to define the best split makes different decision tree algorithms. There are many measures that can be used to determine the best way to split the records. These measures are defined in terms of the class distribution of the records before and after splitting. The best splitting is the one that has more *purity* after the splitting. If we were to split $D$ into smaller partitions according to the outcomes of the splitting criterion, ideally each partition after splitting would be pure (i.e., all the records that fall into a given partition would belong to the same class). Instead of defining a split’s purity, the impurity of its child node is used. There are a number of commonly used impurity measurements: *Entropy*, *Gini Index* and *Classification Error*.  

 **Entropy:** measures the degree of uncertainty, impurity, or disorder. The formula for calculate entropy is as shown below:

\begin{equation} 
E(x)= ∑_{i=1}^{n}p_ilog_2(p_i),
  (\#eq:entropy)
\end{equation} 

Where $p$ represents the probability and $E(x)$ represents the entropy.

**Gini Index:** also called Gini impurity, measures the degree of probability of a particular variable being incorrectly classified when it is chosen randomly. The degree of the Gini index varies between zero and one, where zero denotes that all elements belong to a certain class or only one class exists, and one denotes that the elements are randomly distributed across various classes. A Gini index of 0.5 denotes equally distributed elements into some classes.

The formula used to calculate *Gini* index is shown below:

\begin{equation} 
GINI(x) = 1- ∑_{i=1}^{n}p_i^2,
  (\#eq:Gini)
\end{equation} 

Where $p_i$ is the probability of an object being classified to a particular class.

**Classification Error** measures the misclassified class labels. It is calculated with the formula shows below:
\begin{equation} 
Classification error(x)= 1 - max_{i}p_i.
  (\#eq:clerror)
\end{equation}

Among these three impurity measurements, *Gini* is Used by the CART (classification and regression tree) algorithm for classification trees, and *Entropy* is Used by the ID3, C4.5, and C5.0 tree-generation algorithms. 

With the above explanation, we can now say that the aims of a decision tree algorithm are to reduce the Entropy level from the root to the leaves and the best tree is the one that takes order from the most to the least in reducing the Entropy level. 

The good news is that we do not need to calculate the impurity of each test condition to build a decision tree manually. Most tools have the tree construction built-in already. We will use the R package called *rpart* to build decision trees for our Titanic prediction problem. 

## The Simplest Decision Tree for Titanic 

In the Titanic problem, Let’s quickly review the possible attributes. Previously, we have understood that there are a few attributes that have a little prediction power or we say they have a little association with the dependent variable *Survivded*. These attributes include *PassengerID*, *Name*, and *Ticket*. That is why we re-engineered some of them like the passenger's name has been re-engineered into the *Title*, etc. Other attributes can all be used to predict a passenger's death or survival since they all have some power of prediction. So, which one to use and in what order? We will use the Titanic problem to demonstrate how to build decision trees for prediction. 

Let us consider a simple decision tree firstly. 

The simplest decision tree perhaps is the one that only has one test condition and two possible outcomes. In terms of a tree, we called it one internal node and two branches. There is only one attribute that meets the requirements. That is *Sex*, so our decision tree will be built only base on the passenger's gender. 

We need a number of libraries to make our code works.


```r
#load rpart the library which support decision tree 
library(rpart)
# Build our first model only use Sex attribute, check help on rpart, 
# This model only takes Sex as predictor and Survived as the consequencer.
# load our re-engineered data set and separate train and test datasets
RE_data <- read.csv("./data/RE_data.csv", header = TRUE)
train <- RE_data[1:891, ]
test <- RE_data[892:1309, ]
#build a decision tree model use rpart. 
#set seed to make random reproducible
set.seed(1234) #decision tree model has many random selections
model1 <- rpart(Survived ~ Sex, data = train, method="class")
```

Simple! and quick. There are only three lines of code. you can see we have the first two lines to build two variables 'train' and 'test' to hold our training dataset and testing dataset. The model is simply a function invocation, the function is called 'rpart'.

R function did the job for us so we do not need to go through the model construction phase manually to build our classifier. The decision tree has been already built. Now we can use our model to make predictions on the test dataset. 

For the Kaggle competition, participants can produce predictions on the test dataset provided and make submissions to the Kaggle competition website. Kaggle will award a score based on the prediction's accuracy on the test dataset. 

In practice, people would not build a prediction model and use it to produce a prediction on the test dataset blandly to finish the job. We want to know how our model is performing before using it to do predictions on the test dataset. One way to get to know about the model's performance is to make a prediction on the training dataset or part of it. So we can compare the model's prediction with the original value.       


```r
#library caret is a comprehensive library support all sorts of model analysis
library(caret)
options(digits=4)
# assess the model's accuracy with train dataset by make a prediction on the train data. 
Predict_model1_train <- predict(model1, train, type = "class")
#build a confusion matrix to make comparison
conMat <- confusionMatrix(as.factor(Predict_model1_train), as.factor(train$Survived))
#show confusion matrix 
conMat$table
```

```
##           Reference
## Prediction   0   1
##          0 468 109
##          1  81 233
```

```r
#show percentage of same values - accuracy
predict_train_accuracy <- conMat$overall["Accuracy"]
predict_train_accuracy
```

```
## Accuracy 
##   0.7868
```
A brief assessment shows our model1's accuracy is 78.68%. It is not bad! Let us use this model to make a prediction on test dataset. 


```r
# The firs prediction produced by the first decision tree which only used one predictor Sex
Prediction1 <- predict(model1, test, type = "class")
```

Our prediction is produced. Let us submit to Kaggle for an evaluation. We need to convert our prediction into Kaggle's required format and save it into a file and name it as "Tree_Model1.CSV". Here, the importance is knowing the procedure.


```r
# produce a submit with Kaggle required format that is only two attributes: PassengerId and Survived
submit1 <- data.frame(PassengerId = test$PassengerId, Survived = Prediction1)
# Write it into a file "Tree_Model1.CSV"
write.csv(submit1, file = "./data/Tree_Model1.CSV", row.names = FALSE)
```
Once we submit this result to Kaggle. Kaggle will evaluate our results and provide a feedback score. That is a good way to know how good the model performed for unknown data. The Kaggle feedback tells us that we have scored **0.76555**. It means our prediction's accuracy is **76.555%**. This accuracy is lower than the accuracy we have assessed with the training dataset, which was **78.68%**.

Let us look a bit more into our prediction model's performance. We check our prediction's death and survive ratio on the test dataset and compare with the same ratio on the train dataset.  


```r
# Inspect prediction
summary(submit1$Survived)
```

```
##   0   1 
## 266 152
```

```r
prop.table(table(submit1$Survived, dnn="Test survive percentage"))
```

```
## Test survive percentage
##      0      1 
## 0.6364 0.3636
```

```r
#train survive ratio
prop.table(table(as.factor(train$Survived), dnn="Train survive percentage"))
```

```
## Train survive percentage
##      0      1 
## 0.6162 0.3838
```
The result shows that among a total of 418 passengers in the test dataset, 266 passengers predicted perished (with survived value 0), which counts as 64%, and 152 passengers predicted to be survived (with survived value 1) and which count as 36%. This is not too far from the radio on the training dataset, which was 62% survived and 38% perished see \@ref(survive).   

We know that our model only had one test condition which is *Sex*. From the training dataset, we knew that the gender ratio was very similar to this number. 


```r
#Install expss
library(expss)
# add Sex back to the submit and form a new data frame called compare
compare <- data.frame(submit1[1], Sex = test$Sex, submit1[2])
# Check train sex and Survived ratios
prop.table(table(train$Sex, train$Survived, dnn = c("", "Gender and Survive Ratio in Train")),  margin = 1)
```

```
##         Gender and Survive Ratio in Train
##               0      1
##   female 0.2580 0.7420
##   male   0.8111 0.1889
```

```r
# Check predicted sex radio
prop.table(table(compare$Sex, dnn="Gender ratio in Test"))
```

```
## Gender ratio in Test
## female   male 
## 0.3636 0.6364
```

```r
#check predicted Survive and Sex radio
prop.table(table(compare$Sex, compare$Survived, dnn=c("","Gender and Survived Ratio in Prediction")), margin = 1)
```

```
##         Gender and Survived Ratio in Prediction
##          0 1
##   female 0 1
##   male   1 0
```
It is clear that our model is too simple: it predicts any male will perish and every female will be survived! This is approved by the gender (male and female) ratio in the test dataset is identical to the death ratio in our prediction result. 

Further, our predicted results' survival ratio on sex is 0% male and 100% female. It makes sense, doesn't it? Since our model was trained using the training dataset. The survive ratio based on gender were as: only 19% male survived and 81% of male perished. Similarly, the Female survival rate was 74% survived and only 26% perished. 

Any prediction model will have to go for the majority. But, we cannot be satisfied with this simple model that only looking into the sex of a given dataset and predict a passenger's fate with sex! 

This is only the starting, we can improve on it later. But before we do, let us have a close look into our model (the tree structure) and have a sense of the results it produced. R has a lot of functions to help. Plot is a visual tool we can use to visualize our model.

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{08-decision-tree_files/figure-latex/tree1-1} \includegraphics[width=0.5\linewidth]{08-decision-tree_files/figure-latex/tree1-2} 

}

\caption{The decision tree only has *Sex* test condition.}(\#fig:tree1)
\end{figure}

This graph is pretty and informative. The first box top number is the voting (either 0 - dead or 1-survived). The two percentages show the value of the splitting (also called *voting* or *confidence*). The final number on each node shows the percent of the population which resides in this node. Also, the colour of nodes signifies the two classes here. For example, the root node, "0" (death) shows the way the root node is voting; ".62" and ".38" represent the proportion of those who die and those who survive; 100% implies that the entire population resides in the root node.

## The Decision Tree with Core Predictors 

We have identified the correlation between the dependent variable *Survived* and other attributes in the previous Chapter. Let us try to improve the basic *Sex* model by introducing more predictors. From the previous chapter, we know that the five most predictive attributes are: *Sex*, *Pclass*, *HasCabinNum*, *Deck*, and *Fare_PP*. Let us see if they can produce a good model. 


```r
# A tree model with the top five attributes 
set.seed(1234)
model2 <- rpart(Survived ~ Sex + Pclass + HasCabinNum + Deck + Fare_pp, data = train, method="class")
```

```r
# Assess model's accuracy with train data
Predict_model2_train <- predict(model2, train, type = "class")
conMat <- confusionMatrix(as.factor(Predict_model2_train), as.factor(train$Survived))
conMat$table
```

```
##           Reference
## Prediction   0   1
##          0 524 140
##          1  25 202
```

```r
#conMat$overall
predict2_train_accuracy <- conMat$overall["Accuracy"]
predict2_train_accuracy
```

```
## Accuracy 
##   0.8148
```
Our assessment of model2's accuracy is to make a prediction on train data and compare the predicted value with the original value. It shows our model2's accuracy is 81%. This is great! Let us use this model to make a prediction on the test dataset. 


```r
Prediction2 <- predict(model2, test, type = "class")
submit2 <- data.frame(PassengerId = test$PassengerId, Survived = Prediction2)
write.csv(submit2, file = "./data/Tree_model2.CSV", row.names = FALSE)
```

We have produced our second prediction. We can also submit our results to the Kaggle website for the second evaluation. 

This time, we can see the score is still **0.76555**. The accuracy of the test dataset has not been improved. 

Let us examine our classifier again by plot it in a graph.


```r
# plot our full house classifier 
prp(model2, type = 0, extra = 1, under = TRUE)
# plot our full house classifier 
fancyRpartPlot(model2, caption = "")
```

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{08-decision-tree_files/figure-latex/tree2-1} \includegraphics[width=0.5\linewidth]{08-decision-tree_files/figure-latex/tree2-2} 

}

\caption{Decision trees with core predictors.}(\#fig:tree2)
\end{figure}

The above decision tree appeared much complicated than the first one and it goes a lot deeper than what we saw with the decision tree the only test on sex. Note that both trees are binary trees (have two branches). For test conditions that more than two possible answers have been changed to a binary by auto add a split with them. For example, predictor `Fare_pp` takes real numbers and has many possibilities, our model simply split it by test conditions `"Fare_pp >= 8"` and "Fare_pp < 7.2". Conditions have been automatically set for other attributes as well such as `"Pclass >= 2.5"`. These auto-generated test conditions are not ideal, they can be changed if you know how to optimize the decision tree. For the moment, we will take the default settings. 

We can also look into the difference between our two prediction models,


```r
# build a comparison data frame to record each prediction results
Tree_compare <- data.frame(test$PassengerId, predict1=Prediction1, predict2=Prediction2)
# Find differences
dif <- Tree_compare[Tree_compare[2]!=Tree_compare[3], ]
#show dif
print.data.frame(dif, row.names = FALSE)
```

```
##  test.PassengerId predict1 predict2
##               893        1        0
##               896        1        0
##               911        1        0
##               924        1        0
##               925        1        0
##               928        1        0
##               929        1        0
##               941        1        0
##               979        1        0
##               982        1        0
##               996        1        0
##              1009        1        0
##              1017        1        0
##              1024        1        0
##              1030        1        0
##              1032        1        0
##              1045        1        0
##              1051        1        0
##              1061        1        0
##              1080        1        0
##              1091        1        0
##              1117        1        0
##              1141        1        0
##              1155        1        0
##              1160        1        0
##              1172        1        0
##              1175        1        0
##              1176        1        0
##              1183        1        0
##              1201        1        0
##              1225        1        0
##              1246        1        0
##              1257        1        0
##              1259        1        0
##              1268        1        0
##              1275        1        0
##              1301        1        0
```
We can see the second classifier has produced 37 different predictions in comparison with the first classifier. Interesting is that the differences are all that predicted to be survived by the model1 is now predicted to be dead by the model2.

## The Decision Tree with More Predictors

We have seen that our 5 key predictor decision tree model has improved on the sex-only prediction model. However, we know that our re-engineered data has more dimensions that contain useful information. Let us see if we can improve the decision tree model with more predictors in addition to the correlation analysis and PCA analyses results. This time We add travel in groups, Age_group, embarked port and the title attributes *Sex*, *Pclass*, *HasCabinNum*, *Deck*, and *Fare_pp*.


```r
# tree model3 construction using more predictors
model3 <- rpart(Survived ~ Sex + Fare_pp + Pclass + Title + Age_group + Group_size + Ticket_class  + Embarked,
              data=train,
              method="class")
# This model will be used in later chapters so save it in to a file for later to be loaded into memory
save(model3, file = "./data/model3.rda")
#Assess prediction accuracy on train data
Predict_model3_train <- predict(model3, train, type = "class")
conMat <- confusionMatrix(as.factor(Predict_model3_train), as.factor(train$Survived))
conMat$table
```

```
##           Reference
## Prediction   0   1
##          0 517 100
##          1  32 242
```

```r
#conMat$overall
predict3_train_accuracy <- conMat$overall["Accuracy"]
predict3_train_accuracy
```

```
## Accuracy 
##   0.8519
```

Our assessment about the model3's accuracy on the train data shows the accuracy has increased to 85%. It is a big increase from 82% of model2. Let us use this model to make another prediction on the test dataset and see if the accuracy on the test dataset is also increased. 


```r
Prediction3 <- predict(model3, test, type = "class")
submit3<- data.frame(PassengerId = test$PassengerId, Survived = Prediction3)
write.csv(submit3, file = "./data/Tree_model3.CSV", row.names = FALSE)
```

After submitting it to Kaggle the feedback was **0.77033**. This is a big improvement on the test dataset. Let us look into the difference between the last two predictions, 


```r
# plot our full house classifier 
prp(model3, type = 0, extra = 1, under = TRUE)
# plot our full house classifier 
fancyRpartPlot(model3)
```

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{08-decision-tree_files/figure-latex/multi-trees-1} \includegraphics[width=0.5\linewidth]{08-decision-tree_files/figure-latex/multi-trees-2} 

}

\caption{Decision trees with more predictors.}(\#fig:multi-trees)
\end{figure}

Again, let us look into the difference between predicted values on the test dataset.


```r
# build a comparison data frame to record each prediction results
compare <- data.frame(test$PassengerId, predict2 = Prediction2 , predict3 = Prediction3)
# Find differences
dif <- compare[compare[2] != compare[3], ]
#show dif
print.data.frame(dif, row.names = FALSE)
```

```
##  test.PassengerId predict2 predict3
##               896        0        1
##               913        0        1
##               925        0        1
##               956        0        1
##               972        0        1
##               981        0        1
##               982        0        1
##               996        0        1
##              1009        0        1
##              1051        0        1
##              1053        0        1
##              1084        0        1
##              1086        0        1
##              1088        0        1
##              1093        0        1
##              1098        1        0
##              1106        1        0
##              1117        0        1
##              1136        0        1
##              1141        0        1
##              1155        0        1
##              1173        0        1
##              1175        0        1
##              1176        0        1
##              1183        0        1
##              1199        0        1
##              1205        1        0
##              1225        0        1
##              1231        0        1
##              1236        0        1
##              1239        1        0
##              1246        0        1
##              1284        0        1
##              1301        0        1
##              1309        0        1
```
There are 35 differences. 

## The Decision Tree with Full Predictors

Let us use all the attributes: 


```r
# The full-house classifier apart from name and ticket
model4 <- rpart(Survived ~ Sex + Pclass + Age + SibSp + Parch + Embarked + HasCabinNum + Friend_size + Fare_pp + Title + Deck + Ticket_class + Family_size + Group_size + Age_group,
#model4 <- rpart(Survived ~ .,
              data=train,
              method="class")
#assess prediction accuracy on train data
Predict_model4_train <- predict(model4, train, type = "class")
conMat <- confusionMatrix(as.factor(Predict_model4_train), as.factor(train$Survived))
conMat$table
```

```
##           Reference
## Prediction   0   1
##          0 513  94
##          1  36 248
```

```r
#conMat$overall
predict4_train_accuracy <- conMat$overall["Accuracy"]
predict4_train_accuracy
```

```
## Accuracy 
##   0.8541
```
Our assessment on model4's accuracy on the train data shows model4's accuracy is 85%. Let us use this model to make a prediction on the test dataset. 


```r
# make prediction on test dataset
Prediction4 <- predict(model4, test, type = "class")
submit4 <- data.frame(PassengerId = test$PassengerId, Survived = Prediction4)
write.csv(submit4, file = "./data/Tree_model4.CSV", row.names = FALSE)
```
We have produced a new prediction with new model. You can submit to Kaggle for an evaluation. You may find it has a pretty bed score (**0.75119**). Let us examine our classifier again by plot it in a graph.


```r
# plot our full house classifier 
prp(model4, type = 0, extra = 1, under = TRUE)
fancyRpartPlot(model4)
```

\begin{figure}

{\centering \includegraphics[width=0.5\linewidth]{08-decision-tree_files/figure-latex/tree3-1} \includegraphics[width=0.5\linewidth]{08-decision-tree_files/figure-latex/tree3-2} 

}

\caption{Decision trees.}(\#fig:tree3)
\end{figure}

We can see from the tree, the first test condition is `"title = Mr"`. We know that there is a large number of passengers are male adults and most of them perish. However, we can see from our decision tree (left branch) that there are two further test conditions are `"Ticket_class"` and `"Deck"` numbers. Our tree end with 2 survived leaves and one dead leaf. The purity on the leaves is not very high, the highest is 39:366 (90%:10%) and the lowest is 7:14 (33%:67%). 

On the right-hand side of the tree, although some leaves have higher purity the overall percentage is very low. The most powerful predictor `Sex` and `Pclass` has been used towards the leaf of the tree. That could be an explanation for the poor performance. We will demonstrate the detailed interpretation of the model in the later chapter of cross validation and report.  

Let us look into the difference between the last two predictions,


```r
# build a comparison data frame to record each prediction results
compare <- data.frame(test$PassengerId, predict3 = Prediction3 , predict4 = Prediction4)
# Find differences
dif2 <- compare[compare[2] != compare[3], ]
#show dif
print.data.frame(dif2, row.names = FALSE)
```

```
##  test.PassengerId predict3 predict4
##               898        1        0
##               916        1        0
##               933        0        1
##               945        1        0
##               956        1        0
##               961        1        0
##               964        1        0
##               965        0        1
##              1038        0        1
##              1050        0        1
##              1073        0        1
##              1128        0        1
##              1134        0        1
##              1137        0        1
##              1183        1        0
##              1247        0        1
##              1251        1        0
##              1259        0        1
##              1296        0        1
##              1304        1        0
```

There are 20 different predictions in comparison with the third prediction model3 that is the best model we have with the decision tree.

## Summary {-}

So far, we have produced four prediction models with different predictors. We also used these four models to generate four submissions to the Kaggle competition for evaluation since we don't have the survival values for the test dataset. 

The results obtained from the Kaggle in terms of prediction's accuracy were 76.56%, 76.56%, 77.03%, and 75.12% respectively. 

We have noticed the accuracy differences between the model's assessment and model's test accuracy are:

+ model1's assessed accuracy was 78.68% but the accuracy on the test was 76.555%; 

+ Model2's accessed accuracy was 81.48% and the actual accuracy on the test was still 76.555%;

+ Model3's accessed accuracy was 85.19%. and the actual accuracy on the test was 77.03%; 

+ Model4's accessed accuracy was 85.41% and the actual accuracy on the test was 75.12%. 

let us plot these model's accuracy, so we can have a comparison.


```r
library(tidyr)
# Tree models comparison
Model <- c("Model1","Model2","Model3","Model4")
Pre <- c("Sex", "Sex, Pclass, HasCabinNum, Deck, Fare_pp", "Sex, Fare_pp, Pclass, Title, Age_group, Group_size, Ticket_class, Embarked", "All")
Train <- c(78.68, 81.48, 85.19, 85.41)
Test <- c(76.56, 76.56, 77.03, 75.12)
df1 <- data.frame(Model, Pre, Train, Test)
df2 <- data.frame(Model, Train, Test)
knitr::kable(df1, longtable = TRUE, booktabs = TRUE, digits = 2, col.names =c("Models", "Predictors", "Accuracy on Train", "Accuracy on Test"), 
  caption = 'The Comparision among 4 decision tree models'
)
```


\begin{longtable}[t]{llrr}
\caption{(\#tab:unnamed-chunk-2)The Comparision among 4 decision tree models}\\
\toprule
Models & Predictors & Accuracy on Train & Accuracy on Test\\
\midrule
Model1 & Sex & 78.68 & 76.56\\
Model2 & Sex, Pclass, HasCabinNum, Deck, Fare\_pp & 81.48 & 76.56\\
Model3 & Sex, Fare\_pp, Pclass, Title, Age\_group, Group\_size, Ticket\_class, Embarked & 85.19 & 77.03\\
Model4 & All & 85.41 & 75.12\\
\bottomrule
\end{longtable}

Let us plot a bar graph,


```r
df.long <- gather(df2, Dataset, Accuracy, -Model, factor_key =TRUE)
ggplot(data = df.long, aes(x = Model, y = Accuracy, fill = Dataset)) +
  geom_col(position = position_dodge()) 
```

![(\#fig:modelcompare)Decision Tree models' accuracy on Train dataset and Test dataset.](08-decision-tree_files/figure-latex/modelcompare-1.pdf) 
From the plot we can see that:

1. All four models perform better when predicting the survival on the training dataset than they do on the test data. In other words, all models drop prediction accuracy when facing unseen new data.

2. The accuracy of the training and testing datasets are both affected by the number of predictors used in the model. 

3. It is not true that the more predictors a model has the better accuracy. Model4 has the worst accuracy on the test dataset.

4. The biggest drop of prediction accuracy is model4, the difference is almost 11% from 85% on train data to 74% on the test dataset. 

The issue demonstrated by model4 is called overfitting. That is a phenomenon that a model has a higher prediction accuracy on the training dataset and subsequently drops prediction accuracy on the unseen data. Overfitting can be a consequence of many factors. One of the factors has been illustrated with the model4. **That is the more predictors a model used the more chance of the model's overfitting**. this is because the model will be well fitted with the training dataset. This can be seen from the prediction accuracy on the training dataset. The overfit will not suit the unseen data so the model will perform badly on the test dataset. 

We will investigate overfitting by a method called "Cross Validation" later in Chapters 10. 

## Exercises {-}

1. Prove there is overfitting with model3 and model4. The idea is to take certain samples from the training dataset and form a validate dataset. Remove the value of attribute *survived* from the validate dataset. Using models to a prediction on validate dataset. Compare their results. If model3 performs better than model2 on the validate dataset but not on the test dataset (which is given by Kaggle), it shows the model has an overfitting problem. Prove this exists on both model3 and model4.

2. Build a decision tree model using different predictors.

3. Investigate the predictor's number and the same number but different predictors to find the highest prediction accuracy on validation dataset.  


