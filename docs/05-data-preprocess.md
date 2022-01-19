# Data Preparasion
\begin{figure}

{\centering \includegraphics[width=0.6\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/preparation} 

}

\end{figure}

>     “Before anything else, preparation is the key to success” 
>    
>                                        --- Alexander Graham Bell

In the previous chapter, we have done **Data understanding** by examining the quantity and quality of the given data by accessing individual attributes and record levels to do different assessments. After that, we had a pretty good understanding of the raw data in terms of its suitability for analyses. The results of the data assessment set up a number of objectives for the data preprocess or to accomplish, which are what we need to do in the chapter.

First of all, let us briefly review the typical tasks that need to be performed in a typical **Data preprocess**. 

## General Data Prepartion Tasks

[Section 1.3](process.html) has listed a number of tasks that need to be performed to make data suitable for analysis. Depends on the understanding of the problem, the tasks can be different. In our previous analyses at both records and attributes levels, we have found some problems. These problems need to be solved first of all.

1. There are inappropriate data types that need conversion. For example, a lot of features need to be converted into numeric ones so that the machine learning algorithms can process them. 
2. There are errors or missing values.
3. There are attributes' values that need normalization. There are some features have widely different value's range, so the value needs to be converted into roughly the same scale. 
4. There are also attribute values that need to be grouped or transformed into more manageable meaningful groups. 

In this chapter, we will carry on using the Titanic problem to demonstrate the tasks to be performed and the methods that can be used to accomplish these tasks. Remember the ultimate goal of the data preprocessing is to make the dataset suitable for analysis. 

The analytical methods used in this chapter are again a mixture of **Descriptive data analysis** and **Exploratory analysis**. 

## Dealt with Miss Values

We had a pretty good understanding of the Titanic datasets. We knew that there are missing values and some errors. They need to be resolved first of all. The systematic way to find missing value is to write a function checking the missing values, like this one,

Before that, let us quickly recap the datasets we have,

```r
# assume we had imported both train and test dataset and we have combined them into on data

# If we save the file from the previous code we can load it directly
data <- read.csv("./data/data.csv", header = TRUE)

# Check our combined dataset details
glimpse(data) # compare with str(data)
```

```
## Rows: 1,309
## Columns: 12
## $ PassengerId <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, ...
## $ Survived    <int> 0, 1, 1, 1, 0, 0, 0, 0, 1, 1, 1, 1, 0, 0, 0, 1, 0, 1, 0...
## $ Pclass      <int> 3, 1, 3, 1, 3, 3, 1, 3, 3, 2, 3, 1, 3, 3, 3, 2, 3, 2, 3...
## $ Name        <fct> "Braund, Mr. Owen Harris", "Cumings, Mrs. John Bradley ...
## $ Sex         <fct> male, female, female, female, male, male, male, male, f...
## $ Age         <dbl> 22, 38, 26, 35, 35, NA, 54, 2, 27, 14, 4, 58, 20, 39, 1...
## $ SibSp       <int> 1, 1, 0, 1, 0, 0, 0, 3, 0, 1, 1, 0, 0, 1, 0, 0, 4, 0, 1...
## $ Parch       <int> 0, 0, 0, 0, 0, 0, 0, 1, 2, 0, 1, 0, 0, 5, 0, 0, 1, 0, 0...
## $ Ticket      <fct> A/5 21171, PC 17599, STON/O2. 3101282, 113803, 373450, ...
## $ Fare        <dbl> 7.2500, 71.2833, 7.9250, 53.1000, 8.0500, 8.4583, 51.86...
## $ Cabin       <fct> , C85, , C123, , , E46, , , , G6, C103, , , , , , , , ,...
## $ Embarked    <fct> S, C, S, S, S, Q, S, S, S, C, S, S, S, S, S, S, Q, S, S...
```
We can observe that there are 1309 data records with 12 attributes. We can also see the types and values of each attribute. 
We understood the goal of the Titanic problem is to predict given passengers' survival. So, except for the attributes *PassengerID* and the targeted variable *Survived*,  there are 10 attributes present in the combined data that are potentially useful. Among them, two variables *Name* and *Ticket* are less useful intuitively and also confirmed from the previous chapter.

Let us focus on solving the data missing problem. 

We can define a function missing_vars, which can get a proportion of values that are missing in each attribute.  


```r
# Define a function to check missing values
missing_vars <- function(x) {
  var <- 0
  missing <- 0
  missing_prop <- 0
  for (i in 1:length(names(x))) {
    var[i] <- names(x)[i]
    missing[i] <- sum(is.na(x[, i])|x[, i] =="" )
    missing_prop[i] <- missing[i] / nrow(x)
  }
# order   
missing_data <- data.frame(var = var, missing = missing, missing_prop = missing_prop) %>% 
arrange(desc(missing_prop))
# print out
missing_data
}
```

Apply our function to the combined dataset `data`. 


```r
missing_vars(data)
```

```
##            var missing missing_prop
## 1        Cabin    1014 0.7746371276
## 2     Survived     418 0.3193277311
## 3          Age     263 0.2009167303
## 4     Embarked       2 0.0015278839
## 5         Fare       1 0.0007639419
## 6  PassengerId       0 0.0000000000
## 7       Pclass       0 0.0000000000
## 8         Name       0 0.0000000000
## 9          Sex       0 0.0000000000
## 10       SibSp       0 0.0000000000
## 11       Parch       0 0.0000000000
## 12      Ticket       0 0.0000000000
```
*Survived* has 418 missing values that is the `test` dataset number. Our entire `test` dataset needs to be filled with that value. It is not an issue. 

*Cabin* and *Age* have some significant proportion of missing values, whereas Embarked & Fare only has 2 and 1 missing values. 

We will use Cabin and Age as examples to demonstrate the general methods used to deal with missing values.

### Cabin Attribute {-}

*Cabin* has a large number of missing values. A total of 1014 missing values and 687 missing values in the `train` dataset counts as 71 percent of the total value. Its prediction power is in serious doubt since it only has a very small number for each cabin. Facing an attribute that has a large percentage of missing values, in most analyses, it will be simply dropped. However, if you think carefully, the missing value may have some reasons, and that reason could be a factor that affects passengers' lives or perished. Therefore, the first thought, which is normally applied to a large number of missing values, is to replace the attribute with another attribute rather than to fill the missing value themselves. In this case, we can create a new attribute called "*HasCabinNum*" which only records if *Cabin* values are "" (empty or missing value). It has two values "`yes`" and "`no`". This method is very general. It can be used in any attribute that has a large number of missing values. 

Ideally, we should replace the attribute *cabin* with the newly created attribute *HasCabinNum*. However, we find out that the data samples which have the cabin number, cabin number may have some useful information. So, we will keep it in the moment and for later use. 

As mentioned in section 4.5, cabin has rich useful information that can be abstracted and used for analysis (or prediction models). 

\BeginKnitrBlock{rmdaction}
Try your self:
Find details of passenger who share cabins and figure out some deatures like social status or relations. Create new featurs linked them with survive values.  
\EndKnitrBlock{rmdaction}


```r
### Dealing with missing values in Cabin
# add newly created attribute and assign it with new values
data$HasCabinNum <- ifelse((data$Cabin != ""), "Yes", "No")
```
We can examine the relation between our newly created cabin replacement's *HasCabinNum* with the attribute *Survival*.


```r
# Make sure survived is in factor type 
p1 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(HasCabinNum), fill = factor(Survived))) +
   geom_bar(width = 0.5) +
   xlab("HasCabinNum") +
   ylab("Total Count") +
   labs(fill = "Survived")+
   ggtitle("Newly created HasCabinNum attribute on Survived")
# show survive percentage on HasCabinNum 
p2 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(HasCabinNum), fill = factor(Survived))) + 
  geom_bar(position = "fill", width = 0.5) + 
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "HasCabinNum", y = "Percentage of Survived") + 
  ggtitle("Newly created HasCabinNum attribute (Proportion Survived)")

grid.arrange(p1, p2, ncol = 2)
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/cabinPro-1} 

}

\caption{Distribution and survival percentage of the newly created HasCabinNum attribute}(\#fig:cabinPro)
\end{figure}

### Age Attribute {-}

Now we can tackle the issue of missing values with the age attribute. *Age* is a typical numerical value. There several options for filling the missing values:

1. Take the mean value to replace the missing value
2. Take a random list of ages that maintains the original statistical summary values.
3. Use a model to predict values based on the existing values.

Let us look into them one by one, be aware of this if you have multiple options to deal with one attribute, you cannot simply manipulate the original attribute. If you do, the value of the attribute will be altered, so the second option will be never executed since the missing value has been already eliminated.  

1. Take the mean value to replace the missing value. It is the simplest way to impurate the missing value.


```r
# replace missing value in Age with its average
ageEverage <- summarise(data, Average = mean(Age, na.rm = TRUE))
# create a new attribute Age_RE1 and assign it with new values
data$Age_RE1 <- ifelse(is.na(data$Age), as.numeric(ageEverage), as.numeric(data$Age))
# plot newly altered age attribute 
# Make sure survived is in factor type 
p1 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(Age_RE1), fill = factor(Survived))) +
   geom_bar(width = 0.5) +
   xlab("Age_RE1") +
   ylab("Total Count") +
   labs(fill = "Survived")+
   ggtitle("Survived value on Age_RE1")
# show survive percentage on HasCabinNum 
p2 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(Age_RE1), fill = factor(Survived))) + 
  geom_bar(position = "fill", width = 0.5) + 
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Age_RE1", y = "Percentage of Survived") + 
  ggtitle("Survived percentage on Age_RE1")

grid.arrange(p1, p2, ncol = 2)
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/agepro-1} 

}

\caption{Distribution and survival percentage on the Age with missing value filled}(\#fig:agepro)
\end{figure}

2. Take a random number range between `min` and `max` age, and keep the mean and standard deviation unchanged.


```r
# calculate the non-NA mean and std
mean <- mean(data[["Age"]], na.rm = TRUE) # take train mean
std <- sd(data[["Age"]], na.rm = TRUE) # take test std
# replace NA with a list that maintian the mean and std
temp_rnum <- rnorm(sum(is.na(data$Age)), mean=mean, sd=std)
# add new attribute Age_RE2
data$Age_RE2 <- ifelse(is.na(data$Age), as.numeric(temp_rnum), as.numeric(data$Age))
summary(data$Age_RE2)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##  -5.219  21.000  28.000  29.483  38.000  80.000
```

```r
# There are possible negative values too, replace them with positive values
data$Age_RE2[(data$Age_RE2)<=0] <- sample(data$Age[data$Age>0], length(data$Age_RE2[(data$Age_RE2)<=0]), replace=F)
# check
summary(data$Age_RE2)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    0.17   21.00   28.00   29.59   38.00   80.00
```

```r
# plot newly altered age attribute 
# Make sure survived is in factor type 
p1 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(Age_RE2), fill = factor(Survived))) +
   geom_bar(width = 0.5) +
   xlab("Age_RE2") +
   ylab("Total Count") +
   labs(fill = "Survived")+
   ggtitle("Survived value on Age_RE2 attribute")

# show survive percentage on HasCabinNum 
p2 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(Age_RE2), fill = factor(Survived))) + 
  geom_bar(position = "fill", width = 0.5) + 
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Age_RE2", y = "Percentage of Survived") + 
  ggtitle("Survived percentage on Age_RE2 attribute")

grid.arrange(p1, p2, ncol = 2)
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/AgePro2-1} 

}

\caption{Distribution and survival percentage on the Age with missing value filled with distribution shape maintained}(\#fig:AgePro2)
\end{figure}

3. Using machine generate model to produce new values based on other exiting values  

Among many prediction models (later chapters), the *decision tree* is the simplest. It can split data samples into subsets based on many test conditions (called branches) until there are not test conditions to test or there is no sample left untested^[detailed decision tree and prediction model will be covered in the next chapter].  
To demonstrate we can use a prediction model to fill the missing values, here we will only use a simple decision tree without any further calibration. Since  *Age* is a continuous variable we want to use the method="anova"^[ANOVA, stands for "Analysis of variance", is a statistical model used to analyse the differences among group means in a sample. Decision trees can take many different ways to partition data samples such as *Entropy*, *Gini Index*, *Classification error*, and *ANOVA*. ] for our decision tree. So let us build a decision tree on the subset of the data with the age values available, and then replace those that are missing,


```r
# confirm Age missing values
data$Age_RE3 <- data$Age
summary(data$Age_RE3)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##    0.17   21.00   28.00   29.88   39.00   80.00     263
```

```r
# Construct a decision tree with selected attributes and ANOVA method
Agefit <- rpart(Age_RE3 ~ Survived + Pclass + Sex + SibSp + Parch + Fare + Embarked,
                  data=data[!is.na(data$Age_RE3),], 
                  method="anova")
#Fill AGE missing values with prediction made by decision tree prediction
data$Age_RE3[is.na(data$Age_RE3)] <- predict(Agefit, data[is.na(data$Age_RE3),])
#confirm the missing values have been filled
summary(data$Age_RE3)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
##    0.17   22.00   27.43   29.63   37.00   80.00
```

```r
p1 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(Age_RE3), fill = factor(Survived))) +
   geom_bar(width = 0.5) +
   xlab("Age_RE3") +
   ylab("Total Count") +
   labs(fill = "Survived")+
   ggtitle("Survived value on Age_RE3 attribute")

# show survive percentage on HasCabinNum 
p2 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(Age_RE3), fill = factor(Survived))) + 
  geom_bar(position = "fill", width = 0.5) + 
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Age_RE3", y = "Percentage of Survived") + 
  ggtitle("Survived percentage on Age_RE3 attribute")

grid.arrange(p1, p2, ncol = 2)
```

![](05-data-preprocess_files/figure-latex/unnamed-chunk-3-1.pdf)<!-- --> 
The above three methods can all fill the missing values. Each filled with different values. Depends on the applications you can choose to use any of them. For our prediction problem, I would use the machine predicted since we are doing predicting anyway. So I will tied data with replacing the original *Age* with *Age_RE3* and removal of the other two extra age attributes.


```r
data$Age <- data$Age_RE3
data <- subset(data, select = -c(Age_RE1, Age_RE2, Age_RE3))
```

### Fare Attribute {-}

Since there was one missing value in *Fare*, we can also see that this person travelled alone, so I can’t impurate it, The best solution is replacing it with the mean or median value, or even other values like median in the same class or median from the same embarked port, or age group, etc. 


```r
# Replacing na with the median value
data[is.na(data$Fare), ]
```

```
##      PassengerId Survived Pclass               Name  Sex  Age SibSp Parch
## 1044        1044       NA      3 Storey, Mr. Thomas male 60.5     0     0
##      Ticket Fare Cabin Embarked HasCabinNum
## 1044   3701   NA              S          No
```

```r
data$Fare[is.na(data$Fare)] <- median(data$Fare, na.rm = T)
```

### Embarked Attribute {- #Embarked}

*Embark* has two missing values. There are two methods to make up these two values: take the mode value, which is the most value at present; or the most likely value. The mode value is `S` (Southampton), the fact that 70% of passengers embarked from ‘S’. 

The most likelihood value needs some analysis. Generally, the embarked port reflects a passenger's journey. It is associated with the fare of the ticket. So we could compare the fare of the ticket to see it most likely fit which part of the journey. However, we have noticed that the fare is the original data may provide faulty information since it can be a shared ticket. The fare is also shared with someone. If that is the case we should consider the partner's Embarked port as its most appropriate value.

So we take two steps: 
1. find out the passenger has a shared ticket or not. If the ticket is shared then find the travel companion's embarked port and take that as the passenger's embarked port;
2. If the ticket is not shared or shared partner's embarked port is also missing, find out the ticket price per person and compare with other ticket's price per person to allocate the embarked port.


```r
# list the missing records to figure out the fare and the ticket?
data[(data$Embarked==""), c("Embarked", "PassengerId",  "Fare", "Ticket")]
```

```
##     Embarked PassengerId Fare Ticket
## 62                    62   80 113572
## 830                  830   80 113572
```

```r
# we want find out if the fare is a single ticket or a group ticket.
```
We can see the two miss records share the same ticket number and the fare. The situation because extremely simple. We don't need to consider other possibilities. The two passengers must travel together. There is no possibility of any other reference can be used to figure out the missing port. 

For safety, let us check if there are other passengers share the same ticket number?


```r
# we need to find out is there other passenger share the ticket?
data[(data$Ticket=="113572"), c("Ticket", "PassengerId", "Embarked", "Fare")]
```

```
##     Ticket PassengerId Embarked Fare
## 62  113572          62            80
## 830 113572         830            80
```

The answer is "No". It tells us only the two missing records share the ticket number. So we only need to find out the price (per person) to compare with other prices (per person) to allocate the missing embarked port. The logic is the same journey should bear the same ticket price. To calculate the ticket price (per person), we create an attribute *Fare_pp*. It is the ticket price divided by the number of the passenger who shares the same ticket. That is the concept of the group ticket. It can also be useful to single out the "group travel" vs "travel alone". 

As matter of fact, the raw data sample already has this concept such as *Sibsp* and *Parch*. We don't know if the *Sibsp* and *Parch* are sharing the same ticket number since the attributes are only numbers. We can imagine the people who travel in the group may not be relatives and they could be simple friends or colleagues. Anyway, in the case of group travel, it is useful to know the group size. So we created another new attribute *Friend_size* to record the number of passengers who share the same ticket number ie. "travel in-group".   


```r
# calculate fare_PP per person
fare_pp <- data %>%
  group_by(Ticket, Fare) %>%
  dplyr::summarize(Friend_size = n()) %>%
  mutate(Fare_pp = Fare / Friend_size)
```

```
## `summarise()` regrouping output by 'Ticket' (override with `.groups` argument)
```

```r
data <- left_join(data, fare_pp, by = c("Ticket", "Fare"))
data %>%
  filter((Embarked != "")) %>%
ggplot(aes(x = Embarked, y = Fare_pp)) + 
  geom_boxplot() + 
  geom_hline(yintercept = 40, col = "deepskyblue4")
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/embarkfarepp-1} 

}

\caption{Possible embarked port by value of Fare per person}(\#fig:embarkfarepp)
\end{figure}
From the above plot, we can see that price 40 (per person) is an outlier in embarked group `S` and `Q`. However, if they embarked from `C` the price only just falls into the upper quartile. So, we can reasonably the pare are embarked from `C`, so we want to assign `C` to the embarked missing value. 


```r
data$Embarked[(data$Embarked)==""] <- "C"
```

Now we have dealt with all the missing values. We could simply run the same code again to confirm the missing values have been fulfilled. 


```r
missing_vars(data)
```

```
##            var missing missing_prop
## 1        Cabin    1014    0.7746371
## 2     Survived     418    0.3193277
## 3  PassengerId       0    0.0000000
## 4       Pclass       0    0.0000000
## 5         Name       0    0.0000000
## 6          Sex       0    0.0000000
## 7          Age       0    0.0000000
## 8        SibSp       0    0.0000000
## 9        Parch       0    0.0000000
## 10      Ticket       0    0.0000000
## 11        Fare       0    0.0000000
## 12    Embarked       0    0.0000000
## 13 HasCabinNum       0    0.0000000
## 14 Friend_size       0    0.0000000
## 15     Fare_pp       0    0.0000000
```

In summary, we have dealt with the 4 discovered missing values. Different approaches and methods are adopted. some of them are simple fulfillment like replacement with mean/median/mode values, others have more complicated processes involved deeper drill-down analysis or even predictions. Depends on the applications, appropriate methods may need multiple trials and exploration. 

However, we have discovered one interesting thing that the fare could be shared among multiple passengers (not only the same fare but also the same ticket numbers) see the previous section. It appeared to be the price of a group ticket. It creates confusing information on the fare. So it may be a good idea to re-engineer it into another more useful attribute like *fare_PP* (Fare per person), see next section.

## Attribute Re-engineering 

In the previous chapter when we do data understanding. Apart from the missing values, we also find some attributes do not make sense or have no prediction power when considering the relationship with survival. for example, we have found *name* has little prediction power. It is illogical to say some survived because he or she has a specific name. However there is title information buried inside the name, The title can potentially useful at least it shows the age addition to the gender. 

We have also find other useful information hidden inside some variables. For example, the information about the deck is possibly hidden inside *cabin*. Information about group travel is buried inside of *Ticket* and *Fare* that passengers share same tickets number and fare must travel in a group. It seems that the ticket is a group ticket. Furthermore, we have also found that the group that shares tickets is mostly family members. This is further confirmed by the none `0` values in the *SibSp* and *Parch* attributes. That hidden information can be very important. We can surface them by attributes' re-engineering.   

### Title from Name attribute {-}

The *Name* is initially believed is useless for predict a passenger's fate. But we have found in it there is information about titles even maybe marriage relations. So our first task in attribute re-engineering is to create a new attribute called *Title*. It is abstracted from *Name*. It is the title of the passenger, which can be extracted from the *Name* attribute using a regular expression.


```r
# Abstract Title out
 data$Title <- gsub('(.*, )|(\\..*)', '', data$Name)
 data %>%
   group_by(Title) %>%
   dplyr::count() %>%
 arrange(desc(n))
```

```
## # A tibble: 18 x 2
## # Groups:   Title [18]
##    Title            n
##    <chr>        <int>
##  1 Mr             757
##  2 Miss           260
##  3 Mrs            197
##  4 Master          61
##  5 Dr               8
##  6 Rev              8
##  7 Col              4
##  8 Major            2
##  9 Mlle             2
## 10 Ms               2
## 11 Capt             1
## 12 Don              1
## 13 Dona             1
## 14 Jonkheer         1
## 15 Lady             1
## 16 Mme              1
## 17 Sir              1
## 18 the Countess     1
```
We can see that there is a total of 18 different titles. Some of them are commonly used titles and others are less common. Those less commonly used titles have a very small number, mostly, just 1.

We will group the less commonly used titles into `other` so to balance distribution. 


```r
# Group those less common title’s into an ‘Other’ category.
data$Title <- ifelse(data$Title %in% c("Mr", "Miss", "Mrs", "Master"), data$Title, "Other")

L<- table(data$Title, data$Sex)
knitr::kable(L, digits = 2, booktabs = TRUE, caption = "Title and sex confirmation")
```

\begin{table}

\caption{(\#tab:unnamed-chunk-6)Title and sex confirmation}
\centering
\begin{tabular}[t]{lrr}
\toprule
  & female & male\\
\midrule
Master & 0 & 61\\
Miss & 260 & 0\\
Mr & 0 & 757\\
Mrs & 197 & 0\\
Other & 9 & 25\\
\bottomrule
\end{tabular}
\end{table}
Checking the table of *Title* vs *Sex* shows nothing anomalous.

A stacked bar graph of the newly created attribute suggests it could be quite useful that the difference in survival between 'Master' and 'Mr' will be something that hasn't been captured by the `Sex` attribute.


```r
data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(Title), fill = factor(Survived))) + 
  geom_bar(position = "fill") + 
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Title", y = "Survival Percentage") + 
  ggtitle("Title attribute (Proportion Survived)")
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/titlePro-1} 

}

\caption{Survivial percentage onver Title}(\#fig:titlePro)
\end{figure}

### Deck from Cabin attribute {-}

From our previous analysis, we have found out that the cabin numbers are all start with a letter. It could be a deck number of some sort. If we group cabin numbers with their initial letter, we can then treat the ordinal missing cabin's value records as a separate group. 

So, we group all cabin numbers into groups according to their first letter. Create a new attribute with the name *Deck*. and assign records with no cabin number as *U* (no cabin number) for its *Deck* value.


```r
data$Cabin <- as.character(data$Cabin)
data$Deck <- ifelse((data$Cabin == ""), "U", substr(data$Cabin, 1, 1))
# plot our newly created attribute relation with Survive
p1 <- ggplot(data[1:891,], aes(x = Deck, fill = factor(Survived))) +
  geom_bar(width = 0.5) +
  labs(x = "Deck number", y = "Total account") + 
  labs(fill = "Survived")

# plot percentage of survive
p2 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(Deck), fill = factor(Survived))) + 
  geom_bar(position = "fill") + 
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Deck number", y = "Percentage") + 
  ggtitle("Newly created Deck number (Proportion Survived)")

grid.arrange(p1, p2, ncol = 2)
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/deckpro-1} 

}

\caption{Survivla vlaue and percentage over newly created Deck attribute}(\#fig:deckpro)
\end{figure}

### Extract ticket class from ticket number {-}

We knew that the values of *Ticket* appear has two major kinds 'Letters Numbers' or just 'Numbers'. This could be worth extracting. However, just two class is too rough. As suggested during understanding data, we can group tickets by their first letter or number. let us create a *Ticket_class* to replace *Ticket*.


```r
data$Ticket <- as.character(data$Ticket)
data$Ticket_class <- ifelse((data$Ticket != " "), substr(data$Ticket, 1, 1), "")
data$Ticket_class <- as.factor(data$Ticket_class)

# plot our newly created attribute relation with Survive
p1 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = Ticket_class, fill = factor(Survived))) +
  geom_bar(width = 0.5) +
  labs(x = "Ticket_class", y = "Total account") + 
  labs(fill = "Survived value over Ticket class")

# plot percentage of survive
p2 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(Ticket_class), fill = factor(Survived))) + 
  geom_bar(position = "fill") + 
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Ticket_class", y = "Percentage") + 
  ggtitle("Survived percentage over Newly created Ticket_class")

grid.arrange(p1, p2, ncol = 2)
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/ticketclass-1} 

}

\caption{Survival value and percentage over newly created Ticket class}(\#fig:ticketclass)
\end{figure}

Although the plot appeared to have a skewed bi-model shape, its prediction is clearly improved by ticket number.

### Travel in Groups {-}

We have seen that passengers shared ticket numbers and fares. It is a clear indication of the passenger traveling in groups. Travel in groups can be an important factor for survival in disasters. The Titanic movie impressed millions because of the love story about a couple, they want to stay together to live and to death. Generally, that is the spirit of grouping - stay together for worse or for better. Apart from two friends travel together, we have also seen the family travel together that as indicated by *SibSp* and *Parch* attributes. 

To make it simple we can create a *Group_size*, which takes a minimum value of 1 to represent the passenger travel alone. otherwise in groups. The group size is defined as:

\begin{equation} 
Group\_size = Max(Friend\_size, Family\_size).
(\#eq:group)
\end{equation} 

where,
\begin{equation} 
Friend\_size = Sum(PassengerID),
(\#eq:friend)
\end{equation} 
that share the some ticket number and fare, which we have already created in the section \@ref(fare_pp) when we create new data frame `Fare_pp`.
\begin{equation} 
Family\_size = SibSp + Parch + 1
(\#eq:family)
\end{equation} 

So we do,


```r
data$Family_size <- data$SibSp + data$Parch + 1
data$Group_size <- pmax(data$Family_size, data$Friend_size)
```
Now let us see our newly created attribute's prediction power,

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/unnamed-chunk-8-1} 

}

\caption{Survival value and percentage over newly created Group Size}(\#fig:unnamed-chunk-8)
\end{figure}
The plot shows that most people traveled alone,  small and large groups have the least chance of survival while Medium-sized groups (3 and 4) seemed to have the best chance of living.

### Age in Groups {-}

We have seen the age has a strong correlation with survival. However, it is too fine granted, it is better to create demographical groups called *Age_group*. 


```r
Age_labels <- c('0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79')

data$Age_group <- cut(data$Age, c(0, 10, 20, 30, 40, 50, 60, 70, 80), include.highest=TRUE, labels= Age_labels)

p1 <- data %>%
  filter(!is.na(Survived)) %>%
    ggplot(aes(x = Age_group, y = ..count.., fill = factor(Survived))) +
  geom_bar() +
  ggtitle("Survived value ove newly created Age_group")

# plot percentage of survive
p2 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = Age_group, fill = factor(Survived))) + 
  geom_bar(position = "fill") + 
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Age group", y = "Percentage") + 
  ggtitle("Survived percentage ove newly created Age_group")

grid.arrange(p1, p2, ncol = 2)
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/agegroup-1} 

}

\caption{Survival value and percentage over newly created Age Group}(\#fig:agegroup)
\end{figure}
We can see here only age group "0-9" has a better chance of survive. 

### Fare per passenger {- #farepp}

We have used this concept when we fill the missing value of *Embarked* in Section 5.2. We were comparing the records' fare with other passengers' fare because we believe the fare should reflect the journey that should indicate the embarked port. It is there we find out the passenger could share the fare and the ticket number. So it is faulty information if you only considering *Fare* values between two passengers. After we introduce a new attribute *Fare_pp* that stands for fare per person, its value is the true value a passenger paid for the travel.  

So we have,
\begin{equation} 
Fare\_PP = Fare / Friend\_size.
(\#eq:farepp)
\end{equation} 

We do this,


```r
data$Fare_pp <- data$Fare/data$Friend_size
```

Let us examine our newly created attribute *Fare_PP*'s prediction power,


```r
# plot Fare_PP against Survived
p1<- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = Fare_pp, fill = factor(Survived))) + 
 geom_histogram(binwidth = 2) +
  scale_y_continuous(breaks = seq(0, 500, 50)) + 
  scale_fill_discrete(name = "Survived") + 
  labs(x = "Fare (per person)", y = "Count") + 
  ggtitle("Survived value over Fare_pp")
p1
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/fareperperson-1} 

}

\caption{Survival value and percentage over newly created Fare per person}(\#fig:fareperperson-1)
\end{figure}

```r
# plot percentage of survive
p2 <- data %>%
  filter(!is.na(Survived)) %>%
ggplot(aes(x = factor(Fare_pp), fill = factor(Survived))) + 
  geom_bar(position = "fill") + 
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Fare per person", y = "Percentage") + 
  ggtitle("Survived rate over newly created Fare_PP")
p2
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/fareperperson-2} 

}

\caption{Survival value and percentage over newly created Fare per person}(\#fig:fareperperson-2)
\end{figure}

```r
# plot in box plot
data %>%
  filter(!is.na(Survived)) %>%
  filter(Fare > 0) %>%
ggplot(aes(factor(Survived), Fare_pp)) +
  geom_boxplot(alpha = 0.2) +
  scale_y_continuous(trans = "log2") +
  geom_point(show.legend = FALSE) + 
  geom_jitter()
```

\begin{figure}

{\centering \includegraphics[width=0.95\linewidth]{05-data-preprocess_files/figure-latex/fareperperson2-1} 

}

\caption{Survival value over newly created Fare per person by boxplot}(\#fig:fareperperson2)
\end{figure}

```r
# grid.arrange(p1, p2, ncol = 2)
```
The graph confirms the fare_PP associated with the passenger's survival. We can see that the perished passenger tend to pay less (around 8 pounds) and the average survived passenger appeared paid something around 14 pounds. 

## Build Re-engineered Dataset

We have done many things:

- unified the `test` dataset with `train` dataset
- transformed some data types 
- make up and filled the missing values for some attributes
- re-engineered some attributes, and
- created some new attributes

Let us look at our dataset attributes,


```r
glimpse(data)
```

```
## Rows: 1,309
## Columns: 21
## $ PassengerId  <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16,...
## $ Survived     <int> 0, 1, 1, 1, 0, 0, 0, 0, 1, 1, 1, 1, 0, 0, 0, 1, 0, 1, ...
## $ Pclass       <int> 3, 1, 3, 1, 3, 3, 1, 3, 3, 2, 3, 1, 3, 3, 3, 2, 3, 2, ...
## $ Name         <fct> "Braund, Mr. Owen Harris", "Cumings, Mrs. John Bradley...
## $ Sex          <fct> male, female, female, female, male, male, male, male, ...
## $ Age          <dbl> 22.00000, 38.00000, 26.00000, 35.00000, 35.00000, 27.4...
## $ SibSp        <int> 1, 1, 0, 1, 0, 0, 0, 3, 0, 1, 1, 0, 0, 1, 0, 0, 4, 0, ...
## $ Parch        <int> 0, 0, 0, 0, 0, 0, 0, 1, 2, 0, 1, 0, 0, 5, 0, 0, 1, 0, ...
## $ Ticket       <chr> "A/5 21171", "PC 17599", "STON/O2. 3101282", "113803",...
## $ Fare         <dbl> 7.2500, 71.2833, 7.9250, 53.1000, 8.0500, 8.4583, 51.8...
## $ Cabin        <chr> "", "C85", "", "C123", "", "", "E46", "", "", "", "G6"...
## $ Embarked     <fct> S, C, S, S, S, Q, S, S, S, C, S, S, S, S, S, S, Q, S, ...
## $ HasCabinNum  <chr> "No", "Yes", "No", "Yes", "No", "No", "Yes", "No", "No...
## $ Friend_size  <int> 1, 2, 1, 2, 1, 1, 2, 5, 3, 2, 3, 1, 1, 7, 1, 1, 6, 1, ...
## $ Fare_pp      <dbl> 7.250000, 35.641650, 7.925000, 26.550000, 8.050000, 8....
## $ Title        <chr> "Mr", "Mrs", "Miss", "Mrs", "Mr", "Mr", "Mr", "Master"...
## $ Deck         <chr> "U", "C", "U", "C", "U", "U", "E", "U", "U", "U", "G",...
## $ Ticket_class <fct> A, P, S, 1, 3, 3, 1, 3, 3, 2, P, 1, A, 3, 3, 2, 3, 2, ...
## $ Family_size  <dbl> 2, 2, 1, 2, 1, 1, 1, 5, 3, 2, 3, 1, 1, 7, 1, 1, 6, 1, ...
## $ Group_size   <dbl> 2, 2, 1, 2, 1, 1, 2, 5, 3, 2, 3, 1, 1, 7, 1, 1, 6, 1, ...
## $ Age_group    <fct> 20-29, 30-39, 20-29, 30-39, 30-39, 20-29, 50-59, 0-9, ...
```
We can see there are 21 attributes in total. Compare with the 12 attributes in the original raw dataset, there are 9 newly added contributes. They have enriched the original attributes but some re-engineered attributes are leftover such as *Name* and *Cabin* (too many missing values). *Name* has been transformed into *Title* and *Cabin* has been transformed into *HasCabinNum* and *Deck*. 

Clearly, we need to clean up or remove redundant attributes. For some re-engineered attributes like *Deck* effectively is derived from *Cabin*. With the *Deck* in place, *Cabin* has no need to exist. Effectively, lose *Cabin* will not lose any information. *Fare* provides misleading information because it only keeps the amount of money paid for a ticket but does not specify the amount is for group fare or single fare. So *Fare_PP* is the accurate replacement of the *Fare*. *Family_size* is derived from *Sibsp* and *Parch*, they are containment relations, if you want fine grant analysis, you can keep all of them. *Friend_size* was introduced when we calculate the ticket price. That is a person who paid for the ticket. *Friend_size* is different from the *Family_size* because the Friend_size is simply the passenger who shares the same ticket number. There is no way to know if they are a family member. At the same time, "Family_size" does not ensure the sharing of the ticket. *Ticket_class* is derived from the *Ticket* number. It is a kind of grouping of the ticket. Finally, the *Age_group* is a similar concept that groups the *Age* attribute. 

Therefore, we could keep our re-engineered dataset as follows:


```r
RE_data <- subset(data, select = -c(Name, Cabin, Fare))
```

Our dataset now have the following attributes:

```r
glimpse(RE_data)
```

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
## $ Ticket       <chr> "A/5 21171", "PC 17599", "STON/O2. 3101282", "113803",...
## $ Embarked     <fct> S, C, S, S, S, Q, S, S, S, C, S, S, S, S, S, S, Q, S, ...
## $ HasCabinNum  <chr> "No", "Yes", "No", "Yes", "No", "No", "Yes", "No", "No...
## $ Friend_size  <int> 1, 2, 1, 2, 1, 1, 2, 5, 3, 2, 3, 1, 1, 7, 1, 1, 6, 1, ...
## $ Fare_pp      <dbl> 7.250000, 35.641650, 7.925000, 26.550000, 8.050000, 8....
## $ Title        <chr> "Mr", "Mrs", "Miss", "Mrs", "Mr", "Mr", "Mr", "Master"...
## $ Deck         <chr> "U", "C", "U", "C", "U", "U", "E", "U", "U", "U", "G",...
## $ Ticket_class <fct> A, P, S, 1, 3, 3, 1, 3, 3, 2, P, 1, A, 3, 3, 2, 3, 2, ...
## $ Family_size  <dbl> 2, 2, 1, 2, 1, 1, 1, 5, 3, 2, 3, 1, 1, 7, 1, 1, 6, 1, ...
## $ Group_size   <dbl> 2, 2, 1, 2, 1, 1, 2, 5, 3, 2, 3, 1, 1, 7, 1, 1, 6, 1, ...
## $ Age_group    <fct> 20-29, 30-39, 20-29, 30-39, 30-39, 20-29, 50-59, 0-9, ...
```

In order to preserve our re-engineered dataset, it is a good idea to save it back to hard drive. So it can be used later in the data analysis.


```r
write.csv(RE_data, file = "./data/RE_Data.CSV", row.names = FALSE)
```

## Summary {-}

In this chapter, based on the previous chapter on **Data Understanding**, we have demonstrated some basic tasks needed to perform in the step of the data preprocess or data preparation. Those tasks are either resulted from the initial data quality assessment like discover the missing values or demanded by the next step of data analyses like correlation analyses to order the potential predictors based on the prediction power. Attributes re-engineering is the task to make the maximum use of the information contained in the given dataset or transform give attributes in the most appropriate form or types. The ultimate goal is to make datasets ready for analysis.  

## Exercises {-}

1. Discuss the advantage and disadvantage of fill *Age* missing value with a sample that has the same `mean` and `std`. 
2. Re-engineer *Cabin* to capture social status and relations by creating new features to reflect more accurate relations with *survive*. Discuss their generalisation and specification. 
3. When we make up missing values of the *Embarked* attribute we want to compare the price of the ticket the passenger paid with other tickets' price to allocate the possible embarked port. It all works well, however, one of the factors we did not consider is the variation of the price on *Pclass*. We have knowledge that the higher class the more expensive the price will be. Can you analyze the price per ticket with the *Pclass* to see if it can produce conflict results against the allocation of the embarked port by price comparison? 




