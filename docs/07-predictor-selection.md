# Predictor Selection  



\begin{center}\includegraphics[width=0.8\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/selection} \end{center}

>
>
>
> "Transformation occurs when there has been a learning lesson and you choose to create a better choice." 
>
>                               -- Andrea Reibmayr 
>

In the previous chapter, we have established the idea that predictive data analysis (PDA) is a kind of model building. In its three-step process, the first step is the predictor selection. This is because most prediction model does not use all the attributes of the data samples. An only a small amount of predictors are used in a model. Therefore before a model can be constructed or trained, it is necessary to select predictors^[if you are familiar with association and PCA analyses, you can jump to chapter 8]. 

## Predictor Selection Pricinples

Selecting predictors needs to answer two questions: how many predictors and which one to be selected. It is a very complicated issue that not only depends on the attributes but also the model to be constructed. The latter can only be clear after the model construction. Let us focus on the first factor that is the attributes themselves. 

Predictor selection when considering the attributes, the principle is:

1. Select as little as possible. Since more predictors may increase the computation cost of a model and may reduce the model's performance by introducing noise and outliers. 
2. Do not select attributes that do not have prediction power. the prediction power refers to the influence or impacts a predictor on the dependent variable.
3. Do not select attributes that do not provide extra information. Some attributes are strongly correlated or have Collinearity^[A phenomenon in which one predictor variable in a multiple regression model can be linearly predicted from the others with a substantial degree of accuracy.]. In this case, only one predictor from them is enough.
4. Always choose predictors to follow the order of the prediction power. That is select the attribute that has the most prediction power and then the second and the third, so on so forth. 

## Attributes Analysis

To obtain the attributes' prediction power and the correlation among them, the basic analytic tasks need to be performed. These analytic tasks include *Correlation Analysis*, *Principal component analysis (PCA)*, and *Possibly factor analysis (FA)*.

- **Correlation Analysis**\index{Correlation Analysis}. Analysis correlation among the attributes, and ordering them based on the correlation of attributes with the dependent attribute. Select an appropriate number of the attributes from the highest value towards the lowest value of correlation. 

- **Principal component analysis (PCA)**\index{Principal component analysis}. PCA is a dimension reduction method by projecting each data point onto only the first few principal components to obtain lower-dimensional data while preserving as much of the data's variation as possible. PCA only works on numerical variables.

- **Possibly factor analysis (FA)**\index{Factor analysis}. Factor analysis is a statistical method used to describe variability among observed, correlated variables in terms of a potentially lower number of unobserved variables called factors. For example, it is possible that variations in six observed variables mainly reflect the variations in two unobserved (underlying) variables.

There are other similar tasks such as *MCA*, *FAMD*, *CA*, and *MFA*. MCA\index{multiple correspondence analysis} stands for multiple correspondence analysis. It can only apply to categorical variables; FAMD\index{factor analysis of mixed data} stands for factor analysis of mixed data. It can apply to both numerical and categorical variables; CA\index{correspondence analysis} is correspondence analysis, it can only work on two variables (contingency table); MFA\index{multiple factor analysis} is multiple factor analysis, it is needed only when you have variables set by the group. These tasks are all species of the PCA. 

In this chapter, we will demonstrate the basic Correlation analysis and principal component analysis to understand the relationship among attributes and between the predictor and the dependent variable. We will continue to use the Titanic example. 

## Attributes Correlation Analysis

We have re-engineered the Titanic dataset. So instead of using the original dataset, let us consider the correlation among attributes of our re-engineered dataset. 

```
## Rows: 1,309
## Columns: 18
## $ PassengerId  <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16,...
## $ Survived     <int> 0, 1, 1, 1, 0, 0, 0, 0, 1, 1, 1, 1, 0, 0, 0, 1, 0, 1, ...
## $ Pclass       <int> 3, 1, 3, 1, 3, 3, 1, 3, 3, 2, 3, 1, 3, 3, 3, 2, 3, 2, ...
## $ Sex          <fct> male, female, female, female, male, male, male, male, ...
## $ Age          <dbl> 22.00000, 38.00000, 26.00000, 35.00000, 35.00000, 27.4...
## $ SibSp        <int> 1, 1, 0, 1, 0, 0, 0, 3, 0, 1, 1, 0, 0, 1, 0, 0, 4, 0, ...
## $ Parch        <int> 0, 0, 0, 0, 0, 0, 0, 1, 2, 0, 1, 0, 0, 5, 0, 0, 1, 0, ...
## $ Ticket       <fct> A/5 21171, PC 17599, STON/O2. 3101282, 113803, 373450,...
## $ Embarked     <fct> S, C, S, S, S, Q, S, S, S, C, S, S, S, S, S, S, Q, S, ...
## $ HasCabinNum  <fct> No, Yes, No, Yes, No, No, Yes, No, No, No, Yes, Yes, N...
## $ Friend_size  <int> 1, 2, 1, 2, 1, 1, 2, 5, 3, 2, 3, 1, 1, 7, 1, 1, 6, 1, ...
## $ Fare_pp      <dbl> 7.250000, 35.641650, 7.925000, 26.550000, 8.050000, 8....
## $ Title        <fct> Mr, Mrs, Miss, Mrs, Mr, Mr, Mr, Master, Mrs, Mrs, Miss...
## $ Deck         <fct> U, C, U, C, U, U, E, U, U, U, G, C, U, U, U, U, U, U, ...
## $ Ticket_class <fct> A, P, S, 1, 3, 3, 1, 3, 3, 2, P, 1, A, 3, 3, 2, 3, 2, ...
## $ Family_size  <int> 2, 2, 1, 2, 1, 1, 1, 5, 3, 2, 3, 1, 1, 7, 1, 1, 6, 1, ...
## $ Group_size   <int> 2, 2, 1, 2, 1, 1, 2, 5, 3, 2, 3, 1, 1, 7, 1, 1, 6, 1, ...
## $ Age_group    <fct> 20-29, 30-39, 20-29, 30-39, 30-39, 20-29, 50-59, 0-9, ...
```

```
##   PassengerId      Survived          Pclass          Sex           Age       
##  Min.   :   1   Min.   :0.0000   Min.   :1.000   female:466   Min.   : 0.17  
##  1st Qu.: 328   1st Qu.:0.0000   1st Qu.:2.000   male  :843   1st Qu.:22.00  
##  Median : 655   Median :0.0000   Median :3.000                Median :27.43  
##  Mean   : 655   Mean   :0.3838   Mean   :2.295                Mean   :29.63  
##  3rd Qu.: 982   3rd Qu.:1.0000   3rd Qu.:3.000                3rd Qu.:37.00  
##  Max.   :1309   Max.   :1.0000   Max.   :3.000                Max.   :80.00  
##                 NA's   :418                                                  
##      SibSp            Parch            Ticket     Embarked HasCabinNum
##  Min.   :0.0000   Min.   :0.000   CA. 2343:  11   C:272    No :1014   
##  1st Qu.:0.0000   1st Qu.:0.000   1601    :   8   Q:123    Yes: 295   
##  Median :0.0000   Median :0.000   CA 2144 :   8   S:914               
##  Mean   :0.4989   Mean   :0.385   3101295 :   7                       
##  3rd Qu.:1.0000   3rd Qu.:0.000   347077  :   7                       
##  Max.   :8.0000   Max.   :9.000   347082  :   7                       
##                                   (Other) :1261                       
##   Friend_size      Fare_pp           Title          Deck       Ticket_class
##  Min.   : 1.0   Min.   :  0.000   Master: 61   U      :1014   3      :429  
##  1st Qu.: 1.0   1st Qu.:  7.579   Miss  :260   C      :  94   2      :278  
##  Median : 1.0   Median :  8.050   Mr    :757   B      :  65   1      :210  
##  Mean   : 2.1   Mean   : 14.765   Mrs   :197   D      :  46   P      : 98  
##  3rd Qu.: 3.0   3rd Qu.: 15.000   Other : 34   E      :  41   S      : 98  
##  Max.   :11.0   Max.   :128.082                A      :  22   C      : 77  
##                                                (Other):  27   (Other):119  
##   Family_size       Group_size       Age_group  
##  Min.   : 1.000   Min.   : 1.000   20-29  :552  
##  1st Qu.: 1.000   1st Qu.: 1.000   30-39  :229  
##  Median : 1.000   Median : 1.000   40-49  :171  
##  Mean   : 1.884   Mean   : 2.194   10-19  :162  
##  3rd Qu.: 2.000   3rd Qu.: 3.000   0-9    :100  
##  Max.   :11.000   Max.   :11.000   50-59  : 62  
##                                    (Other): 33
```
A quick correlation plot of the numeric attributes to get an idea of how they might relate to one another. You can see that we have dropped two `chr` attributes: *Title* and *Deck*. We could include them if we convert the character value into numbers. For example, the *Title* could be converted into 1-6 numbers as 1 represents `Mr`, 2 represents `Mrs`, and so on. 

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{07-predictor-selection_files/figure-latex/unnamed-chunk-1-1} 

}

\caption{Correlation among numerical attributes}(\#fig:unnamed-chunk-1)
\end{figure}

```r
# show correlation in table
library(kableExtra) # markdown tables 
```

```
## Warning: package 'kableExtra' was built under R version 3.6.3
```

```
## 
## Attaching package: 'kableExtra'
```

```
## The following object is masked from 'package:dplyr':
## 
##     group_rows
```

```r
lower <- round(cor,2)
lower[lower.tri(cor, diag=TRUE)]<-""
lower <- as.data.frame(lower)
knitr::kable(lower, booktabs = TRUE,
  caption = 'Coorelations among attributes') %>% 
  kable_styling(bootstrap_options = c("striped", "hover", "condensed", "responsive", font_size = 8))
```

\begin{table}

\caption{(\#tab:unnamed-chunk-2)Coorelations among attributes}
\centering
\begin{tabular}[t]{lllllllllllllllll}
\toprule
  & Survived & Pclass & Sex & Age & SibSp & Parch & Embarked & HasCabinNum & Friend\_size & Fare\_pp & Title & Deck & Ticket\_class & Family\_size & Group\_size & Age\_group\\
\midrule
Survived &  & -0.34 & -0.54 & -0.05 & -0.04 & 0.08 & -0.17 & 0.32 & 0.07 & 0.29 & -0.05 & -0.3 & -0.04 & 0.02 & 0.08 & -0.03\\
Pclass &  &  & 0.12 & -0.43 & 0.06 & 0.02 & 0.19 & -0.71 & -0.08 & -0.77 & -0.22 & 0.73 & -0.02 & 0.05 & -0.07 & -0.45\\
Sex &  &  &  & 0.06 & -0.11 & -0.21 & 0.1 & -0.14 & -0.17 & -0.12 & 0.01 & 0.13 & 0 & -0.19 & -0.2 & 0.07\\
Age &  &  &  &  & -0.27 & -0.15 & -0.07 & 0.3 & -0.2 & 0.38 & 0.49 & -0.32 & 0 & -0.26 & -0.2 & 0.98\\
SibSp &  &  &  &  &  & 0.37 & 0.07 & -0.01 & 0.68 & -0.05 & -0.2 & 0.01 & 0.05 & 0.86 & 0.73 & -0.27\\
\addlinespace
Parch &  &  &  &  &  &  & 0.05 & 0.04 & 0.65 & -0.03 & -0.09 & -0.03 & 0.06 & 0.79 & 0.67 & -0.14\\
Embarked &  &  &  &  &  &  &  & -0.21 & 0.01 & -0.3 & -0.03 & 0.24 & -0.04 & 0.07 & 0.02 & -0.06\\
HasCabinNum &  &  &  &  &  &  &  &  & 0.1 & 0.65 & 0.14 & -0.96 & -0.03 & 0.01 & 0.09 & 0.3\\
Friend\_size &  &  &  &  &  &  &  &  &  & 0.09 & -0.19 & -0.1 & 0.12 & 0.8 & 0.97 & -0.2\\
Fare\_pp &  &  &  &  &  &  &  &  &  &  & 0.18 & -0.7 & 0.1 & -0.05 & 0.09 & 0.38\\
\addlinespace
Title &  &  &  &  &  &  &  &  &  &  &  & -0.15 & 0.01 & -0.18 & -0.18 & 0.48\\
Deck &  &  &  &  &  &  &  &  &  &  &  &  & 0.02 & -0.01 & -0.1 & -0.32\\
Ticket\_class &  &  &  &  &  &  &  &  &  &  &  &  &  & 0.06 & 0.11 & 0.01\\
Family\_size &  &  &  &  &  &  &  &  &  &  &  &  &  &  & 0.85 & -0.26\\
Group\_size &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  & -0.2\\
\addlinespace
Age\_group &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  & \\
\bottomrule
\end{tabular}
\end{table}

The plot shows not only the correlation between other attributes, which can potentially be used as predictors, with the dependent attribute *Survived*, but also the correction among potential predictors. In terms of correlation with *Survived*, *Sex* has the largest value but in negative -0.54, the next is *Pclass* with -0.34. So if we can only have two predictors for survival, the first two we should use are *Sex* and *Pclass*. If I want to choose five predictors for survival, I would choose *Sex*, *Pclass*, *HasCabinNum*, *Deck*, and *Fare_PP*. 

The largest correlation value is between *Age* and *Age_group* with 0.98. It makes sense because *Age_group* is a grouping of *Age*. 

We can also observe that *Pclass* has a high correlation with *HasCabinNum* (71),   *Fare_pp* (-77) and *Deck* (73). It suggests that if we have *Pclass* in our model, we may not need to use *Fare_pp*, *HasCabinNum* or *Deck* since they are effectively telling us the same thing that we have suspected at the beginning that is the "social class" of a passenger. This social class can be interpreted as the richer people, who paid more money on a ticket, has a better cabin. 

A similar concept can be read between attribute *Group_size* and the other three attributes *Friend_Size*, *Family_size*, *SibSp*, and *Parch*. *Family_size* also has a high correlation with both *SibSp* and *Parch*. But *Family_size* has a very low correlation with *Friend_Size*.  

The important point is that the correlation analysis is very useful. It provides the basic reasons for our predictor selection. The idea is that we should choose attributes that have a high correlation with the response variable. For example, if only choose three predictors in a model to predict *Survived*, we should choose the *Sex*, *Pclass*, and *HasCabinNum* because they have the three highest absolute correlation values with *Survived*. If in a model we have chosen *Pclass* we may not need to choose *Fare_pp* and *Deck* because these three have large correction values.   

## PCA Analysis 

PCA and Factor analysis are the most commonly used methods in dimension reduction. In a general data science project, it is possible that a given dataset can have tens or hundreds of features (attributes). For example in the text analysis, if we count words' appearance in a document, we could easily have hundreds even thousands of dimensions. If we want to reduce the dimension into a manageable number, PCA can be very useful. Particularly in visualization, humans are not good with anything over three dimensions. 

PCA uses *Eigenvalues* and *Eigenvectors*^[In linear algebra, an Eigenvector or characteristic vector of a linear transformation is a non-zero vector that changes by a scalar factor when that linear transformation is applied to it.] to reserve the original data information and variation as much as possible. Therefore PCA is simple to calculate the given data's Eigenvectors. The Eigenvectors show the attributes' importance.

PCA normally has the following steps:

1. Calculate the Covariance Matrix^[Covariance matrix is a square matrix giving the covariance between each pair of elements of a given random vector.] of the given dataset.
2. Calculate the Eigenvalues and Eigenvectors of the resulting Covariance Matrix.
3. The resulting Eigenvector that corresponds to the largest Eigenvalue can then be used to reconstruct a large fraction of the variance of the original dataset.

In R, we have a function called `prcomp()`. It takes numerical values. Let us calculate all the 18 attributes' Eigenvalue in our RE_data dataset except *passengerId* and *Survived*. It is obvious that these two attributes are out of consideration. 


```r
# Calculate Eigenvalues of the attributes
data.pca <- prcomp(RE_data[1:891,c(-1, -2)], center = TRUE, scale = TRUE)
summary(data.pca)
```

```
## Importance of components:
##                           PC1    PC2   PC3     PC4     PC5     PC6     PC7
## Standard deviation     2.1734 1.9300 1.362 1.23379 0.99671 0.91959 0.79275
## Proportion of Variance 0.2952 0.2328 0.116 0.09514 0.06209 0.05285 0.03928
## Cumulative Proportion  0.2952 0.5280 0.644 0.73913 0.80122 0.85407 0.89335
##                            PC8     PC9    PC10    PC11    PC12    PC13    PC14
## Standard deviation     0.75131 0.66502 0.55475 0.48300 0.27453 0.19723 0.15091
## Proportion of Variance 0.03528 0.02764 0.01923 0.01458 0.00471 0.00243 0.00142
## Cumulative Proportion  0.92863 0.95627 0.97550 0.99008 0.99479 0.99722 0.99865
##                           PC15      PC16
## Standard deviation     0.14717 1.534e-15
## Proportion of Variance 0.00135 0.000e+00
## Cumulative Proportion  1.00000 1.000e+00
```
We have seen 16 principal components, which named as PC1 to PC16. Each of these explains a percentage of the total variation in the dataset. That is to say, PC1 explains 29% of the total variance, PC2 explains nearly 24% of the variance. Together with over half of the information in the dataset can be encapsulated by just these two principal components. So, by knowing the position of a sample in relation to just PC1 and PC2, you can get a very accurate view of where it stands in relation to other samples, as just PC1 and PC2 can explain 53% of the variance.

Let's call `str()` to have a look at the PCA object.


```r
str(data.pca)
```

```
## List of 5
##  $ sdev    : num [1:16] 2.173 1.93 1.362 1.234 0.997 ...
##  $ rotation: num [1:16, 1:16] -0.1995 0.0756 0.3116 -0.3526 -0.2938 ...
##   ..- attr(*, "dimnames")=List of 2
##   .. ..$ : chr [1:16] "Pclass" "Sex" "Age" "SibSp" ...
##   .. ..$ : chr [1:16] "PC1" "PC2" "PC3" "PC4" ...
##  $ center  : Named num [1:16] 2.309 1.648 29.452 0.523 0.382 ...
##   ..- attr(*, "names")= chr [1:16] "Pclass" "Sex" "Age" "SibSp" ...
##  $ scale   : Named num [1:16] 0.836 0.478 13.432 1.103 0.806 ...
##   ..- attr(*, "names")= chr [1:16] "Pclass" "Sex" "Age" "SibSp" ...
##  $ x       : num [1:891, 1:16] -0.571 1.664 -0.24 1.708 0.818 ...
##   ..- attr(*, "dimnames")=List of 2
##   .. ..$ : chr [1:891] "1" "2" "3" "4" ...
##   .. ..$ : chr [1:16] "PC1" "PC2" "PC3" "PC4" ...
##  - attr(*, "class")= chr "prcomp"
```
The above results contain a lot of details, briefly:

- The center point (`$center`), scaling (`$scale`), standard deviation(`sdev`) of each principal component
- The relationship (correlation or anti-correlation, etc) between the initial variables (on the whole, It can be regarded as the data record) and the principal components (`$rotation`)
- The values of each sample in terms of the principal components (`$x`)

Let us plot PCA to get a visual sense of it. To do so we need to use **biplot**. A biplot is a type of plot that will allow you to visualize how the samples relate to one another in the selected principal components (which samples are similar and which are different) and will simultaneously reveal how each variable contributes to each principal component.

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{07-predictor-selection_files/figure-latex/PCA-1} 

}

\caption{The 1st and the 2nd PCs ploted with ggplot_pca}(\#fig:PCA-1)
\end{figure}
\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{07-predictor-selection_files/figure-latex/PCA-2} 

}

\caption{The 1st and the 2nd PCs ploted with ggplot_pca}(\#fig:PCA-2)
\end{figure}
The axes are seen as arrows originating from the center point. Here, you see that the variables *Fare_pp*, *Age_group*, and *Survived* contribute to PC1, with higher values in those variables moving the records to the right on this plot. This lets you see how the data points relate to the axes. 

We also have other principal components available although they may have fewer weights in comparison with the first two. Each of the other components maps differs from the original variables. We can also plot these other components, for example, PC3 and PC4. If you look into the PC3 and PC4, they are *Sex* and *Age_group*. You may wonder what do they do with our prediction. Well, it can show at least the contribution between them with the dependent variable *Survived*, in addition, it can also show the covariance of both with other variables.  
\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{07-predictor-selection_files/figure-latex/PCA2-1} 

}

\caption{The 3rd and the 4th PC ploted with ggplot_pca}(\#fig:PCA2)
\end{figure}
This Plot shows that original attributes *Ticket_class*, *Sex*, *Fare_PP*, and *Group_size* contribute to PC3, which is *Sex*, in a negative way. It means that with lower values in those variables, the records will move to the left on this plot. Notice the graph shows a close relationship between original attributes with the newly created Principal Components. It indicates the correlation among them.

With these correlation and PCA analyses, We can have a pretty good idea about the attributes. Depending on the models we are constructing, we can be confident to select the number of predictors and specific predictors to ensure our model has a good performance.   
Attribute selection is a parsimonious process that aims to identify a minimal set of predictors for the maximum gain (predictive accuracy). This approach is the opposite of the data pre-process whereas as many meaningful attributes as possible are considered for potential use. 
Later on, when we talk about prediction models, a lot of models have a function to analyse its predictor's importance. It is very similar to the PCA here. 

It is important to recognize that attribute selection could be an iterative process that occurs throughout the model building process. It finishes after no more improvement can be achieved in terms of model accuracy.

## Summary {-}

Predictor selection is a complex issue. It has been studied in many fields like Statistics, Data Analysis, Predictions, and Machine learning. In data science, it is addressed at the individual attribute level and at multiple attributes level. At the individual attribute level, it is called single variant analysis\index{single variant analysis}, it is many studies the relationship between individual attribute and the dependent variable like what we have done in Chapter 4 and 5. Correlation analysis between individual attributes and the dependent variable can provide the prediction power of each individual attribute so the selection can take predictors as wished. The multiple attribute analysis, called multivariant analysis\index{multivariant analysis}, focused on and covariant among the multiple attributes, so the strong correlation or collinearity can be identified, so only representative attribute can be selected as a predictor. Predictor selection is also influenced and affected by the model constructed. this will become clear in later Chapters after the model construction is introduced. 
