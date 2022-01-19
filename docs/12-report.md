# Report 



\begin{center}\includegraphics[width=0.8\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/analytical-report} \end{center}

The last two steps in the process of doing a data science project are "**Results Interpretation**" and "**Report and Communication**". This chapter will explain the tasks and how to accomplish the tasks in these two final steps.

**Results Interpretation**\index{Results Interpretation}. There are three different explanations about the *Results interpretation*. 

1. The first is explanation is from business people. They require data scientists to interpret or explain any results and findings from the analyses. These results or findings generally regard any patterns or insights discovered by the data analyses. The purpose is to provide reasons for and build credibility about the results. providing explanations can build a kind of trust in the results. This may require reporting on the process and methods used in the process to produce the results. 

2. The second understanding of the *Result interpretation* is the kind of data scientist's own assessment or evaluation of any findings. This is to find out if there are any defects or places for improvement. It was demonstrated in the process of *Data modelling* in the previous chapters. 

3. The third explanation about the *Results interpretation* particularly in the area of business intelligence (BI)\index{BI}. *Results interpretation* means an assessment of the business impact of the analytical results. This can only be done by the business people who have the domain expertise. 

What we mean by the *results interpretation* here is a summary of all the efforts we have put into the data science project so far. Recognising data analysis may be an iteration process that may need continuous efforts, we should do a periodical summary and layout any further analyses necessary. 

**Report and Communication** means to produce a narrative of the results, and draw a conclusion on how close the results address the original problems, and communicating about the outputs of analyses with interesting parties. When communicating with non-technical people, visual forms are more intuitive and worth a lot of words. So It makes sense to use data visualization tools or dashboards to communicate with people for your analytical results. 

## Content of Report

Perhaps the most asked question is what should be included in a report. Unfortunately, there is no unique answer to the question. The contents of a report depend on a number of factors such as the requirements of a project, the parties involved in the project like business organisations, and financial support bodies. Keeping in mind that the purpose of a report is to explain how close the analytical results address the original problems. It could include answers for the following questions:

+ Does the results answer the original question and how? 

+ Does the result help to defend against any objections and how? 

+ Are there any limitations on the results? or any angles haven’t been considered?

This chapter will provide an example report for the Titanic problem. This example report will include three parts: **Results explanation**, **Model interpretation**, and **Further analysing suggestions**.  

## Result Explainition

At results explanation, we will report on the efforts that we have put into solving the "Titanic prediction problem" through a process of **Data understanding**, **Data preprocess**, **Predictors selection**, **Model construction**, **Model cross validation** and **Model fine tune** . The report will provide a summary of the jobs done on the data analytical process. 

+ At the "*Data understanding*" step, we went through individual attributes of both `train` and `test` datasets and examined their quality and quantity. We discovered the attributes that have the *missing values* and some other problems. The discoveries set up the goals for the *data preprocess* to be accomplished. 

+ We typically examined the values of the response variable *`Survived`* and its distributions. We recognised from the train datasets that about 2/3 of the passengers have perished. We have also examined relations between the individual attributes and the response variable. We've found that some attributes have no direct connections or impact on survival such as *`Name`* and *`Ticket`*. There is no evidence that someone perished because of her name or her ticket number. However, we've found that the `name` and the `Ticket` number contain information that can have an impact on survival such as `Title` and `Deck` (number, which reflects the location on the ship). This information needs to be abstracted by a technique called "**Features Re-engineering**"\index{Features Re-engineering}. 

+ At the "*Data preprocess*", we have filled the missing values using different techniques. For attributes with a small portion of missing values, we filled the missing value with the average value, the random values from artificially generated data samples that have the same statistical characteristics of the attribute, or the values predicted with a machine learning model. For attributes With a large proportion of missing values, we created a new attribute that reflects the present (or absent) of the values. We re-engineered (created) many new attributes, so they can reflect the relations between the attributes and the response variable in a more meaningful and more accurate way. 

+ At the "*Predictors selection*", we have carried out *correlation analyses* between individual attributes and the response variable and among the attributes themselves. `PCA` was used to figure out the most influential attributes despite that the method is mostly used for *dimension reduction*\index{dimension reduction}. 

+ "*Model building*" is a key task in any data science project. *Titanic prediction* is a binary classification problem. It can be addressed with many models including **Regression models** which are not ideal for a binary classification problem. We have tried the two most commonly used models "**Decision tree**" and "**Random Forest**". We can see that each model has a different prediction performance. During the model construction, the goal was to pursue a higher estimated prediction accuracy since we don't have access to the model's prediction accuracy at the production stage. This is problematic because a model can have a higher estimated accuracy during the model construction but has a much lower prediction accuracy while in real use or in production. It is difficult to know whether that is overfitting or underfitting in the model construction stage. 

+ "*Cross Validation*" (CV) is a commonly used technique to find and eliminate a model's overfitting problem. CV uses only a portion of training data in the model construction and uses the rest portion to test the constructed model since the leftover training data has values of the response variable^[It is called label in machine learning]. So a comparison can be made and the prediction accuracy can be calculated. At the CV stage, We not only validated the models we have constructed but also several different types of models to compare their performance.  

+ Most of the models are not in their best state when first build. A model's performance can be improved with *"Fine Tune"* of the model's parameters and the use of the training dataset. We've fine-tuned a random forest model. With different trails on the `train` dataset provided, we have found the proper number of predictors and the actual combination of the predictors to use. The proper proportion of the training data was investigated and discovered. 

Going through the entire data analyses process, we have produced our prediction model that is the `Random forest`. The best one is **`RF_model2`**. 

## Model Interpretation

There are a number of ways to interpret a model. The commonly used methods are explaining **model's performance** and its **predictors' importance**. 

### Model's Performance Measure {-}

There are many different metrics that can be used to evaluate a prediction model. Depends on the type of the model, the different metrics are used to measure its performance. Random forest models built with the `Caret` package from R, the default metrics are **`Accuracy`** and **`Kappa`**. We have seen them in Chapters 9, 10, and Chapter 11. 

**Accuracy** is the percentage of correctly classified instances out of all instances. It is useful on binary classification problems because it can be clear on exactly how the accuracy breaks down across the classes through a *confusion matrix*\index{confusion matrix}. 

**Kappa** is also called Cohen’s Kappa\index{Cohen’s Kappa}. It is a normalized accuracy measurement. The normalization is at the baseline of random chance on the dataset. It is a more useful measure to use on problems that have an imbalance in the classes (e.g. 70-30 split for classes 0 and 1 and you can achieve 70% accuracy by predicting all instances are for class 0). 

For example, our best random forest model was `RF_model2`. We can use these metrics to briefly explain our model's performance. Let us load model RF_model2 and print out its model details:

\begin{figure}

{\centering \includegraphics[width=0.8\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/Capture/RF_model2} 

}

\caption{The detials of RF_model2}(\#fig:rfmodel2results)
\end{figure}
We can see that the model's estimated accuracy (by the model construction) is **83.16%**. That is `1-OOB`. Notice that the default parameters of the model are: `mtry = 2` and `ntree = 500`. 

Looking into the model's confusion matrix, the RF_model2's overall prediction errors can be interpreted based on the two classes "`survived`"  or "`perished`": error on the predicted perished is 9.28% and error on the predicted survive is 28.07%. It is important to know the differences between them, as in many applications, they reflect the "*positive error*\index{positive error}" and the "*negative error*"\index{negative error}. Depending on the interest, one class is regarded as the positive, the other will be the negative class. However, our model's performance is better on `perished` than on `survived`. 

We can drill down a bit more, for example, we can check the top 10 decision trees' accuracy (error rate) among the 500 randomly generated trees by the `RF model`, and we can also check the average error rate among the 500 trees.


```
##             OOB          0         1
##  [1,] 0.2192192 0.16176471 0.3100775
##  [2,] 0.1963964 0.13157895 0.3004695
##  [3,] 0.1953010 0.12351544 0.3115385
##  [4,] 0.2110092 0.13404255 0.3344710
##  [5,] 0.2009804 0.11729622 0.3354633
##  [6,] 0.2028640 0.13203883 0.3157895
##  [7,] 0.1953216 0.12571429 0.3060606
##  [8,] 0.1900922 0.11588785 0.3093093
##  [9,] 0.1881414 0.11090573 0.3125000
## [10,] 0.1755379 0.09392265 0.3058824
```

```
##       OOB               0                 1         
##  Min.   :0.1582   Min.   :0.08379   Min.   :0.2544  
##  1st Qu.:0.1650   1st Qu.:0.08925   1st Qu.:0.2807  
##  Median :0.1661   Median :0.09107   Median :0.2865  
##  Mean   :0.1670   Mean   :0.09298   Mean   :0.2858  
##  3rd Qu.:0.1684   3rd Qu.:0.09654   3rd Qu.:0.2895  
##  Max.   :0.2192   Max.   :0.16176   Max.   :0.3355
```
To verify the constructed model's prediction accuracy, We did cross validations on the model `RF_model2` and produced a CV model `RF_model2_cv`, We can show the results too. 


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
The CV model confirmed the RF model's best accuracy is 84.04% and the kappa is 65.18%. But it revealed that the model has an overfitting problem. We, therefore, fine-tuned the RF model (with the different predictors that were identified by the fine tune itself). The fine-tuned model's accuracy is also around 84%. 

### Visualise Model's Prediction {-}

We have said that a graph is worth hundreds of words. Visualising analytical results is important and useful. But directly visualise a model's prediction is difficult since the RF model has over 3 dimensions. Humans are not good at understanding any visual objects that have more than 3 dimensions even there are ways of visualization. So we need to find a way of reducing a model's dimension. **`Rtsne`** package from R is exactly designed for this purpose. You can check `Rtsne` document to find more details. Basically, Function `Rtsne` converts a multiple dimension dataframe's values into a 2D-coordinates. We can then use these 2d-coordinates to plot our response variable's value on it. 

\begin{figure}

{\centering \includegraphics{12-report_files/figure-latex/visualmodelPrediction-1} 

}

\caption{2D Visualization of Model RE_model2's Prediciton}(\#fig:visualmodelPrediction)
\end{figure}
The above code filtered the `train` dataset with only predictors used in `RF_model2` through the `features` variable. It calls function `Rtsne` to generate two-dimension coordinates for each data sample in `train` based on the values of the attributes specified by `features`. The coordinates are stored in the sub-array `Y`. So we can dot plot the value of `survived` on the coordinates. Figure \@ref(fig:visualmodelPrediction) shows the two-class distributions based on the features' values, which were used as predictors in the `RF_model2`.  

### Importance of the Model's Predictors {-}

Another element of a model, which is worthwhile to report, is the predictors' importance. In our best random forest model RF_model2, The predictors' importance is shown in Figure \@ref(fig:RFmodel2impPlot). 

\begin{figure}

{\centering \includegraphics{12-report_files/figure-latex/RFmodel2impPlot-1} 

}

\caption{The ordered importance of the predictors}(\#fig:RFmodel2impPlot)
\end{figure}

```
##          Sex        Title      Fare_pp       Pclass Ticket_class   Group_size 
##  0.101687822  0.087587574  0.040794545  0.038645459  0.028333273  0.027816253 
##    Age_group     Embarked 
##  0.016016542  0.006786278
```

From Figure \@ref(fig:RFmodel2impPlot), we can see that the `MeanDecreaseAccu` and the `MeanDecreaseGini` measure provide a different order. The difference reflects the different evaluation metrics. Briefly,

- **Mean Decrease in Impurity (MDI)**, the metric is the `GINI` index, it can be biased towards categorical features which contain many categories.

- **Mean Decrease in Accuracy (MDA)**, the metrics is accuracy, it can provide low importance to other correlated features if one of them is given high importance.

If a model's performance report sets up the tasks for further analysis, then the predictors' importance report can set up the attributes to which features re-engineering should start. 

## Further Analysis

The previous section reports the constructed model (e.g. `RF_model2`) in terms of how it comes about and what was its limitations:

1. The model `RF_model2` is not the best one and it is seriously overfitting. Its performance on the test dataset should be improved. 

2. If any further work is planned, then it should start by considering re-engineer *`Title`* and *`Sex`* since they are the most important predictors in model `RF_model2`.

This section will demonstrate how to improve a constructed model's performance. We still use `RF_model2` as an example. A good place to start is where it gets things wrong! To spot where things went wrong is difficult from numbers. A good technique is using graphs. However model `RF_model2` has 500 decision trees. It is difficult to visualize 500 trees. 

Recall that we have a decision tree model `model3`. It has the same predictors as the `RF_model2`. We can use this decision tree (see Figure \@ref(fig:simplottreemodel3) to find the place where things may go wrong. 

\begin{figure}

{\centering \includegraphics{12-report_files/figure-latex/simplottreemodel3-1} 

}

\caption{The simple decision tree of RF_model2}(\#fig:simplottreemodel3)
\end{figure}

From Figure \@ref(fig:simplottreemodel3), we can see that the single place that we got things wrong is the left branch of the first test condition, where the adult male passengers (as "`Title = MR`") has 81 passengers being wrongly predicted as survived. This is also confirmed by our model that the error rate of predicting passengers' survival is higher than the error rate of predicting passengers' perished. So re-engineer the attribute "`Title`" is a good place to start. This also coincides with the suggestion from the previous section where the importance order of the predictors used in the `RF_model2`. 

Now we will just demonstrate how to further re-engineering the `Title` attribute. The values of `Title` in the `train` dataset are as follows:


```
## 
## Master   Miss     Mr    Mrs  Other 
##     61    260    757    197     34
```

We can see that there are 34 records in the training dataset which has the value of `Other` in the `Title` attribute. It is a good place where further purification can be done.

Let us go back to the raw dataset and abstract title for the name attribute. 


```
## 
##         Capt          Col          Don         Dona           Dr     Jonkheer 
##            1            4            1            1            8            1 
##         Lady        Major       Master         Miss         Mlle          Mme 
##            1            2           61          260            2            1 
##           Mr          Mrs           Ms          Rev          Sir the Countess 
##          757          197            2            8            1            1
```
It becomes obvious that the value of `Title` which has been categorized as `other` is too simplified. We can abstract more information such as gender and age from them. That information is useful for the prediction. It is also inappropriate to keep them as separate categories since some of them have a small number of instances, use them could lead to overfitting of the model. 

Further, bin or bucket them into a more appropriate category is required. We can do so with the knowledge of nobility, locality (country of origin), and other knowledge such as time (at the beginning of the 20 century). For example, "`Dona`" and "`the Countess`" are female nobility equivalent to "`Lady`", and  "`Ms`" and "`Mlle`" are essentially the same with "`Miss`"; "`Mme`" is a military title equivalent to "`Madame`", so it can be categorized as "`Mrs`"; "`Jonkheer`" is an honorific nobility in the Netherlands; and "`Don`" is the title of a university lecturer, they can be categorized as "`Sir`"; "`Col`", "`Capt`", and "`Major`" are military ranks and can be replaced with a more general title "`Officer`". With all of these, we can reduce the number of title's category.


```
## 
##      Dr    Lady  Master    Miss      Mr     Mrs Officer     Rev     Sir 
##       8       3      61     264     757     198       7       8       3
```
We can convert `Title` into a factor to plot their relations with the value of `Survived`.  

\begin{figure}

{\centering \includegraphics{12-report_files/figure-latex/newtitlewithsuv-1} 

}

\caption{Surival Rates for new.Title}(\#fig:newtitlewithsuv)
\end{figure}
We could stop here since we have purified the Title's value *other* with a more precise category in terms of semantic meaning. However, we notice that some values still have very small numbers. We should re-categorize those with small numbers categories like "`Lady`" and "`Sir`" into categories with larger numbers and keep the survival ratio as close as possible. We can categorize "`Lady`" into "`Mrs`", "`Sir`" and "`Rev`" into "`Mr`", For neutral titles like "`Dr`" and "`Officer`", we can categorize them into the title "`Mr`" and "`Mrs`" according to sex.   


```
## 
##      Dr    Lady  Master    Miss      Mr     Mrs Officer     Rev     Sir 
##       0       0      61     264     782     202       0       0       0
```
We can check the title against gender to see if any mistakes made.



\begin{figure}

{\centering \includegraphics{12-report_files/figure-latex/newcattitlewithsuv-1} 

}

\caption{Surival Rates for re-categorised new.Title}(\#fig:newcattitlewithsuv)
\end{figure}
After re-categorized the small number of titles, we only have 4 categories of titles. From the plot, we can see their survival radio is matched with the Survive radio of the attribute `Sex`.

We could use this re-engineered title attributes "**`New_Title`**" to re-build RF models. The overall accuracy of the new models should be increased. The following code is an example of showing that. The new model has indeed increased the overall model's prediction accuracy by 0.45%. It is not a lot but it approves the point that features re-engineer is a place to do a model's performance improvement. 


```
## 
## Call:
##  randomForest(formula = as.factor(Survived) ~ Sex + Fare_pp +      Pclass + New_Title + Age_group + Group_size + Ticket_class +      Embarked, data = RE_data[1:891, ], importance = TRUE) 
##                Type of random forest: classification
##                      Number of trees: 500
## No. of variables tried at each split: 2
## 
##         OOB estimate of  error rate: 16.05%
## Confusion matrix:
##     0   1 class.error
## 0 504  45  0.08196721
## 1  98 244  0.28654971
```

We can further do the same with many other attributes or a combination of multiple attributes. 

## Summary {-}

This chapter demonstrated the final two steps in doing a data science project: "**Results Interpretation**" and "**Report and Communication**". They are ignored by most data scientists if the analytical work is not required by the funding body or mandated project initiator. I would suggest that try to write some kind of report or analytical results' interpretation even for a data experimental project. The report can be brief or completed. However, it is important to go through a series of thoughts about the results obtained in response to the initial data analytical problem. Asking whether or not that the results answer the original question? Are there any limitations on the results? or any angles haven’t been considered? Most people agree that the analytical results of a data science project is not an engineering solution of a problem. It may need multiple rounds of recursive actions. Sometimes, the analytical results is a starting point of another circle or project. With this understanding, a periodical report is even more important.

A report can contain different contents from process summary to particular model explanation and the interpretation of the results. Numbers with some contextual explanation are useful but graphs can speak more than numbers and text. Therefore a lot of reports using graphical dashboards and data visualization tools.  

In the end, it is the sense and opinion which data analytical results supported counts. Furthermore, how these senses and opinions are understood and accepted by other people rather than the data scientists are the goal of a data science project.    

## Exercises {-}
1. Visualise prediction results is a useful way to find the problem. using '**Rtsne**' package from R to visualize decision tree model2 both the left branch and the right branch's prediction, compare them.

```
features <- c("Sex", "Fare_pp", "Pclass", "Title", "Age_group", "Group_size", "Ticket_class", "Embarked")

Tree.left <- train[train$Title == "Mr",]

set.seed(984357)

tsne.left <- Rtsne(Tree.left[, features], check_duplicates = FALSE)

ggplot(NULL, aes(x = tsne.left$Y[, 1], y = tsne.left$Y[, 2],
                 color = Tree.left$Survived)) +
  geom_point() +
  labs(color = "Survived") +
  ggtitle("Visualization of left branch of tree where title is 'Mr'")

#
Tree.right <- train[train$Title != "Mr",]

set.seed(984357)
tsne.right <- Rtsne(Tree.right[, features], check_duplicates = FALSE)
ggplot(NULL, aes(x = tsne.right$Y[, 1], y = tsne.right$Y[, 2],
                 color = Tree.right$Survived)) +
  geom_point() +
  labs(color = "Survived") +
  ggtitle("Visualization of right branch of the tree")
```

2. Considering re-engineer passengers with the same tickets.
