# Apendix:  The R code of the entire project {-}


The entire code are stored in 6 files:

1. [TitanicDataAnalysis1_UnderstandData.R](#UnderstandData)
2. [TitanicDataAnalysis2_Data_Preprocess.R](#DataPreprocess)
3. [TitanicDataAnalysis3_Model_Construction.R](#ModelConstruction)
4. [TitanicDataAnalysis4_Model_Cross_Validation.R](#ModelCrossValidation)
5. [TitanicDataAnalysis5_Model_Fine_Tune.R](#ModelFineTune)
6. [TitanicDataAnalysis6_Analyse_Report.R](#AnalyseReport)

They are also available on-line.

## TitanicDataAnalysis1_Understand_Data.R  {- #UnderstandData}

```
############################################################################
# Copyright 2020 Gangmin Li
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#  	http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
# The base of this code was from Dave Langer "Intoduction to Data Science."
# Credit to him. You can find him on Github: https://github.com/EasyD
# I have also integrated other sources like Titanic Forum, Python code
# and some Chinese R code.
#
# Notably,
# https://www.kaggle.com/startupsci/titanic-data-science-solutions
#
# The whole purpose of this is to teach My students on Data Science.
#
# This R source code file corresponds to video 1 of the YouTube series
# "Introduction to Data Science with R" located at the following URL:
#     http://www.youtube.com/watch?v=32o0DnuRjfg
#
# The task is to build a model based on the train data
# then to predict test data who can survive in Kaggle Titanic competition
##########################################################################
# 1. Understand Data
##########################################################################

### Load raw data

train <- read.csv("train.csv", header = TRUE)
test <- read.csv("test.csv", header = TRUE)

# RStudio help function
# what help with any R function just type ?
# for example ?read.csv in Console

# First explore of datasets
# the first R code you can use is str.
# use ?str to find more

help(str)

# use str to explore train and test
str(train)
str(test)

# Add a "Survived" variable to the test set to allow for combining data sets
test <- data.frame(Survived = rep("NA", nrow(test)), test[,])

# Now check test. you can see that it has 12 variable now.
# And Survived is the first variable. because we used test[,]
# if you want add Survived into the second position Do this,
# test <- data.frame(test[1], Survived = rep("NA", nrow(test)), test[,2:ncol(test)])

# Combine datasets together. actually append test to train
data <- rbind(train, test)
# We may need to keep the raw data into a file in case we need it later.
write.csv(data, "data.cvs", row.names = FALSE )

#### We have done combined datasest with only a few line of code
# notice that the type of Survived has been changed to Chr.
# This is because we used "NA" as its value
# A bit about R data types
# ?str structure of dataset
# chr, int
# Factor in R is 'category'. it likes a selection from a list.
# for example, Cabin Factor w/187 levels. It means there are 187 selections.
# Sex Factor w/ 2 "female", "male", 2,1 means, two options 2- female, 1- male.
#
# NA : not available (absent value, missing value)

str(data)

# Exam PassengerID, type INT, we can check total number and the number of unique values.
# If they are equal and both equal to the number of records. it means there are
# unique and has no missing value.
length(data$PassengerId)
length(unique(data$PassengerId))

### Exam Survived
data$Survived <- as.factor(data$Survived)
table(data$Survived)

# Calculate the survive rate in train data is 38% and the death rate is 61.61%
prop.table(table(as.factor(train$Survived)))

### Examine Pclass value,
# Look into Kaggle's explanation about Pclass: it is a proxy for social class i.e. rich or poor
# It should be factor and it does not make sense to stay in int.
data$Pclass <- as.factor(data$Pclass)
test$Pclass <- as.factor(test$Pclass)
train$Pclass <- as.factor(train$Pclass)

# Distribution across classes
table(data$Pclass)

# Distribution across classes with survive
table(data$Pclass, data$Survived)

# Calculate the distribution on Pclass
# Overall passenger distribution on classes.
prop.table(table(data$Pclass))

# Train data passenger distribution on classes.
prop.table(table(train$Pclass))

# Test data passenger distribution on classes.
prop.table(table(test$Pclass))

# Calculate death distribution across classes with Train data
SurviveOverClass <- table(train$Pclass, train$Survived)
# Convert SurviveOverClass into data frame
SoC.data.fram <- data.frame(SurviveOverClass)
# Retrieve death distribution in classes
Death.distribution.on.class <- SoC.data.fram$Freq[SoC.data.fram$Var2==0]
prop.table(Death.distribution.on.class)

# Calculate death rate in train data
# Distribution across classes with survive in Train data
SurviveOverClass <- table(train$Pclass, train$Survived)
# Convert SurviveOverClass into data frame
SoC.data.fram <- data.frame(SurviveOverClass)
# Retrieve death distribution in classes
Death.distribution.on.class <- SoC.data.fram$Freq[SoC.data.fram$Var2==0]
prop.table(Death.distribution.on.class)

# Try summary on data
summary.data.frame(data)

## Exploratory data analysis with graph
# Load up ggplot2 package to use for visualizations
# load it into memory
library(ggplot2)

# High class passenger has more chance of survive than passenger with lower class
# Hypothesis - Rich passengers can but expensive ticket. class=1 is more expensive
# survived at a higher rate
ggplot(train, aes(x = Pclass, fill = factor(Survived))) +
  geom_bar(width = 0.5) +
  xlab("Pclass") +
  ylab("Total Count") +
  labs(fill = "Survived")

# It sort of proved the survive rate with social class
# more people perished in the third class

### Exam Name attribute
# the original name typed as factor, which we really don't want (shows the uniqueness)
# convert Name type
data$Name <- as.character(data$Name)

# Confirm the name has 1037 unique values
length(unique(data$Name))

# Find the two duplicate names
# First used which function to get the duplicate names and store them as a vector dup.names
# check it up ?which.
dup.names <- data[which(duplicated(data$Name)), "Name"]

# Echo out
dup.names

### Exam Sex attribute
# Retrial male and females. then check their numbers.
summary(data$Sex)

#Plot Sex distribution on entire dataset and get general an impression
ggplot(data[1:891,], aes(x = Sex)) +
  geom_bar(fill="steelblue") +
  xlab("Sex") +
  ylab("Total Count")

# plot Survived over Sex on train. use data[1:891,]
ggplot(data[1:891,], aes(x = Sex, fill = Survived)) +
  geom_bar() +
  xlab("Sex") +
  ylab("Total Count") +
  labs(fill = "Survived")

### Examine Age
# Summary over data, train and test.
summary(data$Age)
summary(train$Age)
summary(test$Age)

#It makes sense to change Age type to Factor to see distribution
summary(as.factor(data$Age))

# Plot distribution of age group
ggplot(data, aes(x = Age)) +
  geom_histogram(binwidth = 10, fill="steelblue") +
  xlab("Age") +
  ylab("Total Count")

# Plot Survived on age group on train dataset
ggplot(data[1:891,], aes(x = Age, fill = Survived)) +
  geom_histogram(binwidth = 10) +
  xlab("Age") +
  ylab("Total Count")

### Exam SibSp, Its original type is int
summary((data$SibSp))

# How many possible unique values?
length(unique(data$SibSp))

# Treat it as a factor, so we know the value distribution
data$SibSp <- as.factor(data$SibSp)
summary(data$SibSp)

# Plot entire SibSp distribution among the 7 values
ggplot(data, aes(x = SibSp)) +
  geom_bar() +
  xlab("SibSp") +
  ylab("Total Count")+
  coord_cartesian()

# Plot on the Survived on SibSp
ggplot(data[1:891,], aes(x = SibSp, fill = Survived)) +
  geom_bar() +
  xlab("SibSp") +
  ylab("Total Count")  +
  labs(fill = "Survived")

### Exam Parch
summary(data$Parch)

# How many possible values?
length(unique(data$Parch))

# Treat is as a factor so we know the value distribution
data$Parch <- as.factor(data$Parch)
summary(data$Parch)

# Plot entire Parch distribution among the 8 posibilites
ggplot(data, aes(x = Parch)) +
  geom_bar() +
  xlab("Parch") +
  ylab("Total Count")

# Plot on the Survived on SibSp
ggplot(data[1:891,], aes(x = Parch, fill = Survived)) +
  geom_bar() +
  xlab("Parch") +
  ylab("Total Count")  +
  labs(fill = "Survived")

### Exam  ticket
summary(data$Ticket)
length(unique(data$Ticket))
str(data$Ticket)
which(is.na(data$Ticket))

# Plot it value
ggplot(data[1:891,], aes(x = Ticket)) +
  geom_bar() +
  xlab("Ticket") +
  ylab("Total Count")

# Plot on the survive on Ticket
ggplot(data[1:891,], aes(x = Ticket, fill = Survived)) +
  geom_bar() +
  xlab("Ticket") +
  ylab("Total Count")  +
  labs(fill = "Survived")

### Examine Fare
summary(data$Fare)
length(unique(data$Fare))

# Can't make fare a factor, treat as numeric & visualize with histogram
ggplot(data, aes(x = Fare)) +
  geom_histogram(binwidth = 5) +
  ggtitle("Fare Distribution") +
  xlab("Fare") +
  ylab("Total Count") +
  ylim(0,200)

# Let's check to see if fare has predictive power
ggplot(data[1:891,], aes(x = Fare, fill = Survived)) +
  geom_histogram(binwidth = 5) +
  xlab("fare") +
  ylab("Total Count") +
  ylim(0,50) +
  labs(fill = "Survived")

# Explore Fare distribution between train and test to see if they are overlapped?
ggplot(train, aes(x = Fare)) +
  geom_histogram(binwidth = 5) +
  ggtitle("Fare Distribution") +
  xlab("Fare") +
  ylab("Total Count") +
  ylim(0,200)

ggplot(test, aes(x = Fare)) +
  geom_histogram(binwidth = 5) +
  ggtitle("Fare Distribution") +
  xlab("Fare") +
  ylab("Total Count") +
  ylim(0,200)

### Cabin
# Examine cabin values
str(data$Cabin)
# Cabin really isn't a factor, make a string and the display first 100
data$Cabin <- as.character(data$Cabin)
data$Cabin[1:100]

# Find number of the missing value
table(train[which(train$Cabin ==""), "Cabin"])

# Analysis of the cabin variable
str(data$Cabin)

# Cabin really isn't a factor, make a string and the display first 100
data$Cabin <- as.character(data$Cabin)
data$Cabin[1:100]

# Find out number of the missing value in the train
train$Cabin <- as.character(train$Cabin)
table(train[which(train$Cabin ==""), "Cabin"])

# Replace empty cabins with a "U"
#data[which(data$Cabin == ""), "Cabin"] <- "U"
data$Cabin[1:100]

# Take a look at just the first char as a factor
cabin.first.char <- as.factor(substr(data$Cabin, 1, 1))
str(cabin.first.char)
levels(cabin.first.char)

# Add to combined data set and plot
data$cabin.first.char <- cabin.first.char

# High level plot
ggplot(data[1:891,], aes(x = cabin.first.char, fill = Survived)) +
  geom_bar() +
  ggtitle("Survivability by cabin.first.char") +
  xlab("cabin.first.char") +
  ylab("Total Count") +
  ylim(0,750) +
  labs(fill = "Survived")

### Examine Embark
str(data$Embarked)
summary(data$Embarked)

# Plot Embarked data distribution and the Survived data over it
ggplot(data, aes(x = Embarked)) +
  geom_bar(width=0.5) +
  xlab("Passenger embarked port") +
  ylab("Total Count")

ggplot(data[1:891,], aes(x = Embarked, fill = Survived)) +
  geom_bar(width=0.5) +
   xlab("embarked") +
  ylab("Total Count") +
  labs(fill = "Survived")

##Calculate death RATE distribution over Embarked port with Train data
# We use table in R, you can check with ?table. A good example is
# mytable <- table(A,B) # A will be rows, B will be columns
# mytable # print table

# margin.table(mytable, 1) # A frequencies (summed over B)
# margin.table(mytable, 2) # B frequencies (summed over A)

# prop.table(mytable) # cell percentages
# prop.table(mytable, 1) # row percentages
# prop.table(mytable, 2) # column percentages

# We need prop.table to get column percentage which is the survived

# creat Embarked and Survived contingency table
SurviveOverEmbarkedTable <- table(train$Embarked, train$Survived)
# Death-0/survived-1 value distribution (percentage) based on embarked ports
# prop.table(mytable, 2) give us column (Survived) percentages
Deathandsurvivepercentage <- prop.table(SurviveOverEmbarkedTable, 2)
# Plot
M <- c("c-Cherbourg", "Q-Queenstown", "S-Southampton")
barplot(Deathandsurvivepercentage[2:4,1]*100, xlab =(""), ylim=c(0,100), ylab="Death distribution in percentage %",  names.arg = M, col="steelblue", main="Death distribution", border="black", beside=TRUE)
barplot(Deathandsurvivepercentage[2:4,2]*100, xlab =(""), ylim=c(0,100), ylab="Death distribution in percentage %",  names.arg = M, col="blue", main="Death distribution", border="black", beside=TRUE)

## Calculate survived RATE distribution based on embarked ports
# Death-0/survived-1 value distribution (percentage) based on embarked ports
# prop.table(mytable, 1) give us row (Port) percentages
# col-1 (Survived=0, perished) and col-2 (Survived =1, survived)
DeathandsurviveRateforeachport <- prop.table(SurviveOverEmbarkedTable, 1)
#plot
barplot(Deathandsurvivepercentage[2:4,1]*100, xlab =(""), ylim=c(0,100), ylab="Death rate in percentage %",  names.arg = M, col="red", main="Death rate comparison among mebarked ports", border="black", beside=TRUE)

##END 1 ######################################################
```
## TitanicDataAnalysis2_Data_Preprocess.R  {- #DataPreprocess}

```
##############################################################
# 2. Data preprocess
###############################################################
## Assume we had imported both train and test dataset and we have combined them
# into on data
# train <- read.csv("train.csv", header = TRUE)
# test <- read.csv("test.csv", header = TRUE)
## Integrate into one file for checking save to do the dame for both files.
# data <- bind_rows(train, test) # compare with data <- rbind(train, test)
#
# If we save the file from the previous code we can load it directly
data <- read.csv("data.csv", header = TRUE)

# Check our combined dataset details
glimpse(data) # compare with str(data)

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

  (missing_data <- data.frame(var = var, missing = missing, missing_prop = missing_prop) %>%
      arrange(desc(missing_prop)))
}
#############################################
## Dealing with missing values
#############################################
# Large number of missing values, itself can be meaningful.
# Add newly created attribute and assign it with new values
data$HasCabinNum <- ifelse((data$Cabin != ""), "Has", "HasNo")

# Examine the relations between our newly created cabin replacement's
# 'HasCabinNum' with the attribute Survival using plot

# The forst plot is the numbers
# Make sure survived is in factor type
p1 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(HasCabinNum), fill = factor(Survived))) +
  geom_bar(width = 0.5) +
  xlab("HasCabinNum") +
  ylab("Total Count") +
  labs(fill = "Survived")+
  ggtitle("Newly created HasCabinNum attribute on Survived")

# The 2nd plot shows survive percentage of HasCabinNum
p2 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(HasCabinNum), fill = factor(Survived))) +
  geom_bar(position = "fill", width = 0.5) +
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "HasCabinNum", y = "Percentage of Survived") +
  ggtitle("Newly created HasCabinNum attribute (Proportion Survived)")

##### Dealing with missing values in Cabin #####

### 1. Replace missing value in Age with its average
ageEverage <- summarise(data, Average = mean(Age, na.rm = TRUE))
# create a new attribute Age_RE1 and assign it with new values
data$Age_RE1 <- ifelse(is.na(data$Age), as.numeric(ageEverage), as.numeric(data$Age))

# Plot newly altered age attribute
# Make sure survived is in factor type
p1 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(Age_RE1), fill = factor(Survived))) +
  geom_bar(width = 0.5) +
  xlab("Age_RE1") +
  ylab("Total Count") +
  labs(fill = "Survived")+
  ggtitle("Survived value on Age_RE1")

# Show survive percentage on HasCabinNum
p2 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(Age_RE1), fill = factor(Survived))) +
  geom_bar(position = "fill", width = 0.5) +
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Age_RE1", y = "Percentage of Survived") +
  ggtitle("Survived percentage on Age_RE1")

### 2. Take a random number range between `min` and `max` age,
# and keep the mean and standard deviation unchanged.
# calculate the non-NA mean and std
mean <- mean(data[["Age"]], na.rm = TRUE) # take  mean
std <- sd(data[["Age"]], na.rm = TRUE) # take  std
# Replace NA with a list that maintian the mean and std
temp_rnum <- rnorm(sum(is.na(data$Age)), mean=mean, sd=std)
# Add new attribute Age_RE2
data$Age_RE2 <- ifelse(is.na(data$Age), as.numeric(temp_rnum), as.numeric(data$Age))
summary(data$Age_RE2)
# There are possible negative values too, replace them with positive values
data$Age_RE2[(data$Age_RE2)<=0] <- sample(data$Age[data$Age>0], length(data$Age_RE2[(data$Age_RE2)<=0]), replace=F)

# Check result
summary(data$Age_RE2)

# Plot newly altered age attribute
# Make sure survived is in factor type
p1 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(Age_RE2), fill = factor(Survived))) +
  geom_bar(width = 0.5) +
  xlab("Age_RE2") +
  ylab("Total Count") +
  labs(fill = "Survived")+
  ggtitle("Survived value on Age_RE2 attribute")

# Show survive percentage on HasCabinNum
p2 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(Age_RE2), fill = factor(Survived))) +
  geom_bar(position = "fill", width = 0.5) +
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Age_RE2", y = "Percentage of Survived") +
  ggtitle("Survived percentage on Age_RE2 attribute")

###Using machine generate model
# to produce new values based on other exiting values
# confirm Age missing values
# get the origianl so it can be compared later
data$Age_RE3 <- data$Age
summary(data$Age_RE3)
# Construct a decision tree with selected attributes and ANOVA method
Agefit <- rpart(Age_RE3 ~ Survived + Pclass + Sex + SibSp + Parch + Fare + Embarked,
                data=data[!is.na(data$Age_RE3),],
                method="anova")
#Fill AGE missing values with prediction made by decision tree prediction
data$Age_RE3[is.na(data$Age_RE3)] <- predict(Agefit, data[is.na(data$Age_RE3),])
#Confirm the missing values have been filled
summary(data$Age_RE3)

p1 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(Age_RE3), fill = factor(Survived))) +
  geom_bar(width = 0.5) +
  xlab("Age_RE3") +
  ylab("Total Count") +
  labs(fill = "Survived")+
  ggtitle("Survived value on Age_RE3 attribute")

# Show survive percentage on HasCabinNum
p2 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(Age_RE3), fill = factor(Survived))) +
  geom_bar(position = "fill", width = 0.5) +
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Age_RE3", y = "Percentage of Survived") +
  ggtitle("Survived percentage on Age_RE3 attribute")

# Choose use one results as Age, we use machine generated
data$Age <- data$Age_RE3
data <- subset(data, select = -c(Age_RE1, Age_RE2, Age_RE3))

### Deal with Fare Attribute
#
# just one missing value so replace it with mean or median value
data[is.na(data$Fare), ]
data$Fare[is.na(data$Fare)] <- median(data$Fare, na.rm = T)

### Embarked Attribute
# we take two steps:
# 1. find out the passenger has a shared ticket or not.
# If the ticket is shared than find the travel companion's
# embarked port and take that as the passenger's embarked port;
# 2. If the ticket is not shared or shared partner's
# embarked port is also missing, find out the ticket price
# per person and compare with other ticket's price per
# person to allocate the embarked port.

# List info of the missing records to figure out the fare and the ticket?
data[(data$Embarked==""), c("Embarked", "PassengerId",  "Fare", "Ticket")]
# we want find out if the fare is a single ticket or a group ticket.
# we need to find out is there other passenger share the ticket?
data[(data$Ticket=="113572"), c("Ticket", "PassengerId", "Embarked", "Fare")]

# Calculate fare_PP per person
fare_pp <- data %>%
  group_by(Ticket, Fare) %>%
  dplyr::summarize(Friend_size = n()) %>%
  mutate(Fare_pp = Fare / Friend_size)
data <- left_join(data, fare_pp, by = c("Ticket", "Fare"))

# Plot Fare per person on Embarked port
data %>%
  filter((Embarked != "")) %>%
  ggplot(aes(x = Embarked, y = Fare_pp)) +
  geom_boxplot() +
  geom_hline(yintercept = 40, col = "deepskyblue4")

# Assign `C` to the embarked missing value.
data$Embarked[(data$Embarked)==""] <- "C"

# Confirm the missing values have been fulfilled.
missing_vars(data)

#######################################
##### Attribute Re-engineering #####
#######################################
#
### Title from Name attribute
#
# Abstract Title out
data$Title <- gsub('(.*, )|(\\..*)', '', data$Name)
data %>%
  group_by(Title) %>%
  dplyr::count() %>%
  arrange(desc(n))

# Group those less common title’s into an ‘Other’ category.
data$Title <- ifelse(data$Title %in% c("Mr", "Miss", "Mrs", "Master"), data$Title, "Other")
# Checking the table of *Title* vs *Sex* shows nothing anomalous
L<- table(data$Title, data$Sex)
knitr::kable(L, digits = 2, booktabs = TRUE, caption = "Title and sex confirmation")

# Plot the results
data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(Title), fill = factor(Survived))) +
  geom_bar(position = "fill") +
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Title", y = "Survival Percentage") +
  ggtitle("Title attribute (Proportion Survived)")

### Deck from Cabin attribute
data$Cabin <- as.character(data$Cabin)
data$Deck <- ifelse((data$Cabin == ""), "U", substr(data$Cabin, 1, 1))
# Plot our newly created attribute relation with Survive
p1 <- ggplot(data[1:891,], aes(x = Deck, fill = factor(Survived))) +
  geom_bar(width = 0.5) +
  labs(x = "Deck number", y = "Total account") +
  labs(fill = "Survived")

# Plot percentage of survive
p2 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(Deck), fill = factor(Survived))) +
  geom_bar(position = "fill") +
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Deck number", y = "Percentage") +
  ggtitle("Newly created Deck number (Proportion Survived)")

### Extract ticket class from ticket number

data$Ticket <- as.character(data$Ticket)
data$Ticket_class <- ifelse((data$Ticket != " "), substr(data$Ticket, 1, 1), "")
data$Ticket_class <- as.factor(data$Ticket_class)

# Plot our newly created attribute relation with Survive
p1 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = Ticket_class, fill = factor(Survived))) +
  geom_bar(width = 0.5) +
  labs(x = "Ticket_class", y = "Total account") +
  labs(fill = "Survived value over Ticket class")

# Plot percentage of survive
p2 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(Ticket_class), fill = factor(Survived))) +
  geom_bar(position = "fill") +
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Ticket_class", y = "Percentage") +
  ggtitle("Survived percentage over Newly created Ticket_class")

### Travel in Groups
data$Family_size <- data$SibSp + data$Parch + 1
data$Group_size <- pmax(data$Family_size, data$Friend_size)

# Plot our newly created attribute's prediction power
p1 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = Group_size, fill = factor(Survived))) +
  geom_histogram() +
  scale_y_continuous(breaks = seq(0, 700, 100)) +
  scale_x_continuous(breaks = seq(0, 10)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Group Size: max(Family Size, Group Size)", y = "Count") +
  ggtitle("Survived count over groupsize")

# Plot percentage of survive
p2 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = Group_size, fill = factor(Survived))) +
  geom_bar(position = "fill") +
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Group_size", y = "Percentage") +
  ggtitle("Survived percentage over Newly created Group_size")

### Age in Groups
# Set bins
Age_labels <- c('0-9', '10-19', '20-29', '30-39', '40-49', '50-59', '60-69', '70-79')
# Assign labels
data$Age_group <- cut(data$Age, c(0, 10, 20, 30, 40, 50, 60, 70, 80), include.highest=TRUE, labels= Age_labels)

# Plot
p1 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = Age_group, y = ..count.., fill = factor(Survived))) +
  geom_bar() +
  ggtitle("Survived value ove newly created Age_group")

# Plot percentage of survive
p2 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = Age_group, fill = factor(Survived))) +
  geom_bar(position = "fill") +
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Age group", y = "Percentage") +
  ggtitle("Survived percentage ove newly created Age_group")

### Fare per passenger
data$Fare_pp <- data$Fare/data$Friend_size

# Plot Fare_PP against Survived
p1<- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = Fare_pp, fill = factor(Survived))) +
  geom_histogram(binwidth = 2) +
  scale_y_continuous(breaks = seq(0, 500, 50)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Fare (per person)", y = "Count") +
  ggtitle("Survived value over Fare_pp")
p1
# Plot percentage of survive
p2 <- data %>%
  filter(!is.na(Survived)) %>%
  ggplot(aes(x = factor(Fare_pp), fill = factor(Survived))) +
  geom_bar(position = "fill") +
  scale_y_continuous(labels = scales::percent, breaks = seq(0, 1, 0.1)) +
  scale_fill_discrete(name = "Survived") +
  labs(x = "Fare per person", y = "Percentage") +
  ggtitle("Survived rate over newly created Fare_PP")
p2

# Plot in box plot
data %>%
  filter(!is.na(Survived)) %>%
  filter(Fare > 0) %>%
  ggplot(aes(factor(Survived), Fare_pp)) +
  geom_boxplot(alpha = 0.2) +
  scale_y_continuous(trans = "log2") +
  geom_point(show.legend = FALSE) +
  geom_jitter()

## Build Re-engineered Dataset
glimpse(data)
# Remove abundant attributs
RE_data <- subset(data, select = -c(Name, Cabin, Fare))
# Write in file
write.csv(RE_data, file = "RE_Data.CSV", row.names = FALSE)
### END 2 Data preprocess #################################################
```
## TitanicDataAnalysis3_Model_Construction.R {- #ModelConstruction}

```
####################################################
# Prediction Model Construction
####################################################
# This file contains prediction model construction
#
#  1. Decision trees
#  2. Random forest
#
##### Predictor selection
# load Library
library(dplyr)# data manipulation
library(tidyverse)
library(caret) # tuning & cross-validation
library(gridExtra) # visualizations


#load re-engineered dataset
RE_data <- read.csv("RE_data.csv", header = TRUE)

# check data
glimpse(RE_data)
summary(RE_data)

### A quick correlation analysis
# and plot of the numeric attributes
# to get an idea of how they might relate to one another.

# convert non numeric types to numeric
RE_data <- RE_data %>% mutate_if(is.factor,  as.numeric)

# plot correlation among numeric attributes
cor <- RE_data %>% select(., -c(Ticket, PassengerId)) %>%
  cor(use="pairwise.complete.obs") %>%
  corrplot::corrplot.mixed(upper = "circle", tl.col = "black")

# show results in a table
library(kableExtra) # markdown tables
lower <- round(cor,2)
lower[lower.tri(cor, diag=TRUE)]<-""
lower <- as.data.frame(lower)
knitr::kable(lower, booktabs = TRUE,
             caption = 'Coorelations among attributes') %>%
kable_styling(bootstrap_options = c("striped", "hover", "condensed", "responsive", font_size = 8))

### PCA Analysis
# Calculate Eigenvalues of the attributes
data.pca <- prcomp(RE_data[1:891,c(-1, -2)], center = TRUE, scale = TRUE)
summary(data.pca)

library(AMR)
#AMR::ggplot_pca(data.pca)
ggplot_pca(data.pca) #default shows PC1 and PC2, the two most imprtance attibutes
#biplot(data.pca)

#plot other components for example PC3 and PC4
ggplot_pca(data.pca, ellipse=TRUE, choices=c(3,4))

################################################
### Decision tree models
################################################
#load rpart the library which support decision tree
library(rpart)
# Build our first model with Rpart, only use Sex attribute
# load our re-engineered data set and separate train and test datasets
RE_data <- read.csv("RE_data.csv", header = TRUE)
train <- RE_data[1:891, ]
test <- RE_data[892:1309, ]

#build a decision tree model use rpart.
model1 <- rpart(Survived ~ Sex, data = train, method="class")

### Assess model's performance
#library caret is a comprehensive library support all sorts of model analysis

library(caret)
options(digits=4) # set decimal points of numbers.
# assess the model's accuracy by make a prediction on the train data.
Predict_model1_train <- predict(model1, train, type = "class")
#build a confusion matrix to make comparison
conMat <- confusionMatrix(as.factor(Predict_model1_train), as.factor(train$Survived))
#show confusion matrix
conMat$table
#show percentage of same values - accuracy
predict_train_accuracy <- conMat$overall["Accuracy"]
predict_train_accuracy

# The firs prediction by the first decision tree
Prediction1 <- predict(model1, test, type = "class")

# produce a submit with Kaggle required format that is only two attributes: PassengerId and Survived
submit1 <- data.frame(PassengerId = test$PassengerId, Survived = Prediction1)
# Write it into a file "Tree_Model1.CSV"
write.csv(submit1, file = "Tree_Model1.CSV", row.names = FALSE)

##Submit to kaggle and get a score: 0.76555

# Inspect prediction
summary(submit1$Survived)
prop.table(table(submit1$Survived, dnn="Test survive percentage"))
#train survive ratio
prop.table(table(as.factor(train$Survived), dnn="Train survive percentage"))

##
## The result shows that among total of 418 passenger in the test dataset,
## 266 passenger predicted perished (with survived value 0), which counts
## as 64% and 152 passenger predicted to be survived (with survived value 1)
## and which count as 36%. This is not too far from the radio on the
## training dataset, which was 62% survived and 38% perished.

# add Sex back to the submit and form a new data frame called compare
compare <- data.frame(submit1[1], Sex = test$Sex, submit1[2])
# Check train sex and Survived ratios
prop.table(table(train$Sex, train$Survived), margin = 1)
# Check predicted sex radio
prop.table(table(compare$Sex, dnn="Gender ratio in Test"))
#check predicted Survive and Sex radio
prop.table(table(compare$Sex, compare$Survived), margin = 1)

## It is clear that our model is too simple: it predicts any male
## will be perished and every female will be survived! This is approved
## by the gender (male and female) ratio in the test dataset is identical
## to the death ratio in our prediction result.

#plot our decision tree
# load some useful libraries
library(rattle)
library(rpart.plot)
library(RColorBrewer)
#
prp(model1)
fancyRpartPlot(model1)

### Tree Model2 with 5 Core Predictors
# A tree model with the top five attributes
set.seed(1234)
model2 <- rpart(Survived ~ Sex + Pclass + HasCabinNum + Deck + Fare_pp, data = train, method="class")

# Assess model's accuracy with train data
Predict_model2_train <- predict(model2, train, type = "class")
conMat <- confusionMatrix(as.factor(Predict_model2_train), as.factor(train$Survived))
conMat$table
#conMat$overall
predict2_train_accuracy <- conMat$overall["Accuracy"]
predict2_train_accuracy

# make a prediction and submit to Kaggle
Prediction2 <- predict(model2, test, type = "class")
submit2 <- data.frame(PassengerId = test$PassengerId, Survived = Prediction2)
write.csv(submit2, file = "Tree_model2.CSV", row.names = FALSE)

### kaggle score: **0.76555**
# plot our full house classifier
prp(model2, type = 0, extra = 1, under = TRUE)
# plot our full house classifier
fancyRpartPlot(model2)

# build a comparison data frame to record each prediction results
Tree_compare <- data.frame(test$PassengerId, predict1=Prediction1, predict2=Prediction2)
# Find differences
dif <- Tree_compare[Tree_compare[2]!=Tree_compare[3], ]
#show dif
dif

### Tree model3 construction using more predictors
model3 <- rpart(Survived ~ Sex + Fare_pp + Pclass + Title + Age_group + Group_size + Ticket_class  + Embarked,
                data=train,
                method="class")
# This model will be used in later chapters so save it in to a file for later to be loaded into memory
save(model3, file = "model3.rda")

#Assess prediction accuracy on train data
Predict_model3_train <- predict(model3, train, type = "class")
conMat <- confusionMatrix(as.factor(Predict_model3_train), as.factor(train$Survived))
conMat$table

#conMat$overall
predict3_train_accuracy <- conMat$overall["Accuracy"]
predict3_train_accuracy

# make prediction and submission, Score: 0.77033
Prediction3 <- predict(model3, test, type = "class")
submit3<- data.frame(PassengerId = test$PassengerId, Survived = Prediction3)
write.csv(submit3, file = "tree_model3.CSV", row.names = FALSE)

# plot our full house classifier
prp(model3, type = 0, extra = 1, under = TRUE)
# plot our full house classifier
fancyRpartPlot(model3)

# build a comparison data frame to record each prediction results
compare <- data.frame(test$PassengerId, predict2 = Prediction2 , predict3 = Prediction3)
# Find differences
dif <- compare[compare[2] != compare[3], ]
#show dif
print.data.frame(dif, row.names = FALSE)

### Tree model4, full-house classifier apart from name and ticket
model4 <- rpart(Survived ~ Sex + Pclass + Age + SibSp + Parch + Embarked + HasCabinNum + Friend_size + Fare_pp + Title + Deck + Ticket_class + Family_size + Group_size + Age_group,
                #model4 <- rpart(Survived ~ .,
                data=train,
                method="class")
#assess prediction accuracy on train data
Predict_model4_train <- predict(model4, train, type = "class")
conMat <- confusionMatrix(as.factor(Predict_model4_train), as.factor(train$Survived))
conMat$table
#conMat$overall
predict4_train_accuracy <- conMat$overall["Accuracy"]
predict4_train_accuracy

# make prediction and submission. Kaggle score: 0.75119
Prediction4 <- predict(model4, test, type = "class")
submit4 <- data.frame(PassengerId = test$PassengerId, Survived = Prediction4)
write.csv(submit4, file = "Tree_model4.CSV", row.names = FALSE)

# plot our full house classifier
prp(model4, type = 0, extra = 1, under = TRUE)
fancyRpartPlot(model4)

# build a comparison data frame to record each prediction results
compare <- data.frame(test$PassengerId, predict3 = Prediction3 , predict4 = Prediction4)
# Find differences
dif2 <- compare[compare[2] != compare[3], ]
#show dif
dif2

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
# bar plot of comparison
df.long <- gather(df2, Dataset, Accuracy, -Model, factor_key =TRUE)
ggplot(data = df.long, aes(x = Model, y = Accuracy, fill = Dataset)) +
  geom_col(position = position_dodge())

##############################################
#  Random Forest models
##############################################
# Install the random forest library, if you have not
# install.packages('randomForest')
# load library
library(randomForest)
library(plyr)
# library(caret)

# load data if you have not
RE_data <- read.csv("RE_data.csv", header = TRUE)

# RE_data <- mutate_if(RE_data, is.numeric, as.factor)
#
train <- RE_data[1:891, ]
test <- RE_data[892:1309, ]

# convert variables into factor because RF can be Classification and Regression
train$Survived <- as.factor(train$Survived)
# convert other attributes which really are categorical data but in form of numbers
train$Pclass <- as.factor(train$Pclass)
train$Group_size <- as.factor(train$Group_size)
#confirm types
sapply(train, class)

# Build the random forest model1 uses Pclass, Sex, HasCabinNum, Deck and Fare_pp
set.seed(1234) #for reproduction
RF_model1 <- randomForest(Survived ~ Sex + Pclass + HasCabinNum
                          + Deck + Fare_pp,
                          data=train, importance=TRUE)

# Make your prediction using the validate dataset
RF_prediction1 <- predict(RF_model1, train)

#check up
conMat<- confusionMatrix(RF_prediction1, train$Survived)
conMat$table

# Misclassification error
paste('Accuracy =', round(conMat$overall["Accuracy"],2))
paste('Error =', round(mean(train$Survived != RF_prediction1), 2))

# produce a submit with Kaggle required format that is only two attributes: PassengerId and Survived
test$Pclass <- as.factor(test$Pclass)
test$Group_size <- as.factor(test$Group_size)

# make prediction, Kaggle score is: 0.76555
RF_prediction <- predict(RF_model1, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = RF_prediction)
# Write it into a file "RF_Result.CSV"
write.csv(submit, file = "RF_Result1.CSV", row.names = FALSE)

# Record the results
RF_model1_accuracy <- c(80, 84, 76.555)

### RE_model2 with more predictors
set.seed(2222)
RF_model2 <- randomForest(as.factor(Survived) ~ Sex + Fare_pp + Pclass + Title + Age_group + Group_size + Ticket_class  + Embarked,
                          data = train,
                          importance=TRUE)
# This model will be used in later chapters, so save it in a file and it can be loaded later.
save(RF_model2, file = "RF_model2.rda")

# RF_model2 Prediction
RF_prediction2 <- predict(RF_model2, train)
#check up
conMat<- confusionMatrix(RF_prediction2, train$Survived)
conMat$table
# Misclassification error
paste('Accuracy =', round(conMat$overall["Accuracy"],2))
paste('Error =', round(mean(train$Survived != RF_prediction2), 2))
# produce a submission and submit to Kaggle
test$Pclass <- as.factor(test$Pclass)
test$Group_size <- as.factor(test$Group_size)

# make prediction on test and submit. Score: 0.78947
RF_prediction <- predict(RF_model2, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = RF_prediction)
# Write it into a file "RF_Result.CSV"
write.csv(submit, file = "RF_Result2.CSV", row.names = FALSE)

# Record RF_model2's results
RF_model2_accuracy <- c(83.16, 92, 78.95)

### RF_model3 construction with the maximum predictors
set.seed(2233)
RF_model3 <- randomForest(Survived ~ Sex + Pclass + Age +
                            SibSp + Parch + Embarked +
                            HasCabinNum + Friend_size +
                            Fare_pp + Title + Deck +
                            Ticket_class + Family_size
                          + Group_size + Age_group,
    data = train, importance=TRUE)

# Display RE_model3's details
RF_model3
# Make a prediction on Train
RF_prediction3 <- predict(RF_model3, train)
#check up
conMat<- confusionMatrix(RF_prediction3, train$Survived)
conMat$table
# Misclassification error
paste('Accuracy =', round(conMat$overall["Accuracy"],2))
paste('Error =', round(mean(train$Survived != RF_prediction3), 2))

# produce a submit with Kaggle
test$Pclass <- as.factor(test$Pclass)
test$Group_size <- as.factor(test$Group_size)

# make prediction, Kaggle score: 0.77033
RF_prediction <- predict(RF_model3, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = RF_prediction)
# Write it into a file "RF_Result.CSV"
write.csv(submit, file = "RF_Result3.CSV", row.names = FALSE)

# Record RF_model3's results
RF_model3_accuracy <- c(83, 94, 77)


### RF_models Comparison
library(tidyr)
Model <- c("RF_Model1","RF_Model2","RF_Model3")
Pre <- c("Sex, Pclass, HasCabinNum, Deck, Fare_pp", "Sex, Fare_pp, Pclass, Title, Age_group, Group_size, Ticket_class, Embarked", "Sex, Pclass, Age, SibSp, Parch, Embarked, HasCabinNum, Friend_size, Fare_pp, Title, Deck, Ticket_class, Family_size, Group_size, Age_group")

Learn <- c(80.0, 83.16, 83.0)
Train <- c(84, 92, 78)
Test <- c(76.555, 78.95, 77.03)
df1 <- data.frame(Model, Pre, Learn, Train, Test)
df2 <- data.frame(Model, Learn, Train, Test)
knitr::kable(df1, longtable = TRUE, booktabs = TRUE, digits = 2, col.names =c("Models", "Predictors", "Accuracy on Learn", "Accuracy on Train", "Accuracy on Test"),
             caption = 'The Comparision among 3 Random Forest models'
)
# Plot bar comparison
df.long <- gather(df2, Dataset, Accuracy, -Model, factor_key =TRUE)
ggplot(data = df.long, aes(x = Model, y = Accuracy, fill = Dataset)) +
  geom_col(position = position_dodge())

### End 3 Model Construction ################################################
```
## TitanicDataAnalysis4_Model_Cross_Validation.R {- #ModelCrossValidation}

```
####################################################
# Model Cross Validation and Fine Tune
####################################################
# This file contains Cross Validation
#  1. CV-tree models
#  2. CV-random forest models
#
##### Predictor selection

library(caret)
library(rpart)
library(rpart.plot)

#read Re-engineered dataset
RE_data <- read.csv("RE_data.csv", header = TRUE)

#Factorize response variable
RE_data$Survived <- factor(RE_data$Survived)

#Separate Train and test data.
train <- RE_data[1:891, ]
test <- RE_data[892:1309, ]

# Setup model's train and valid dataset
set.seed(1000)
samp <- sample(nrow(train), 0.8 * nrow(train))
trainData <- train[samp, ]
validData <- train[-samp, ]

# set random for reproduction
set.seed(3214)
# specify parameters for cross validation
control <- trainControl(method = "repeatedcv",
                        number = 10, # number of folds
                        repeats = 5, # repeat times
                        search = "grid")

###############################################
# CV on Tree models
###############################################

### CV on tree model2
set.seed(1010)
# Create model from cross validation train data
# Tree_model2_cv <- train(Survived ~ Sex + Pclass + HasCabinNum + Deck + Fare_pp,
#                         data = trainData,
#                         method = "rpart",
#                         trControl = control)

# Due to the computation cost once a model is trained.
# it is better to save it and load later rather than compute a gain
#save(Tree_model2_cv, file = "Tree_model2_cv.rda")
load("Tree_model2_cv.rda")

# Show CV model's details
print.train(Tree_model2_cv)
# CV model's estimated accuracy
model_accuracy <- Tree_model2_cv$results$Accuracy[1]
paste("Estimated accuracy:", format(model_accuracy, digits = 4))

# Visualize cross validation tree
rpart.plot(Tree_model2_cv$finalModel, extra=4)
plot.train(Tree_model2_cv)

# Record the model's accuracy on *trainData*, *validData*,
# and *test* dataset. Remember *trainData* and *validData*
# are randomly partitioned from the train dataset.
### Access accuracy on different datasets

# prediction's Confusion Matrix on the trainData
predict_train <-predict(Tree_model2_cv, trainData)
conMat <- confusionMatrix(predict_train, trainData$Survived)
conMat$table
# prediction's Accuracy on the trainData
predict_train_accuracy <- conMat$overall["Accuracy"]
predict_train_accuracy

# prediction's Confusion Matrix on the validData
predict_valid <-predict(Tree_model2_cv, validData)
conMat <- confusionMatrix(predict_valid, validData$Survived)
conMat$table
# prediction's Accuracy on the validData
predict_valid_accuracy <- conMat$overall["Accuracy"]
predict_valid_accuracy

# predict on test
predict_test <-predict(Tree_model2_cv, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = as.factor(predict_test))
write.csv(submit, file = "Tree_model2_CV.CSV", row.names = FALSE)
# test accuracy 0.75837
paste("Test Accuracy:", 0.7584)

# accumulate model's accuracy
name <- c("Esti Accu", "Train Accu", "Valid Accu", "Test Accu")
Tree_model2_CV_accuracy <- c(model_accuracy, predict_train_accuracy, predict_valid_accuracy, 0.7584)
names(Tree_model2_CV_accuracy) <- name
Tree_model2_CV_accuracy

### CV on model3
#
set.seed(1234)
# Tree_model3_cv <- train(Survived ~ Sex + Fare_pp + Pclass + Title + Age_group + Group_size + Ticket_class  + Embarked,
#
#                        data = trainData,
#                        method = "rpart",
#                        trControl = control)
#
# save(Tree_model3_cv, file = "Tree_model3_cv.rda")
load("Tree_model3_cv.rda")
# show model details
print.train(Tree_model3_cv)
# CV model's estimated accuracy
model_accuracy <- Tree_model3_cv$results$Accuracy[1]
paste("Estimated accuracy:", format(model_accuracy, digits = 4))

#Visualize cross validation tree
rpart.plot(Tree_model3_cv$finalModel, extra=4)
plot.train(Tree_model3_cv)

### Access accuracy on different datasets
# prediction's Confusion Matrix on the trainData
predict_train <-predict(Tree_model3_cv, trainData)
conMat <- confusionMatrix(predict_train, trainData$Survived)
conMat$table
# prediction's Accuracy on the trainData
predict_train_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("trainData Accuracy:", predict_train_accuracy)

# prediction's Confusion Matrix on the validData
predict_valid <-predict(Tree_model3_cv, validData)
conMat <- confusionMatrix(predict_valid, validData$Survived)
conMat$table
# prediction's Accuracy on the validData
predict_valid_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("validData Accuracy:", predict_valid_accuracy)

#predict on test
predict_test <-predict(Tree_model3_cv, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = as.factor(predict_test))
write.csv(submit, file = "Tree_model3_CV.CSV", row.names = FALSE)

## test accuracy is 0.77751
paste("Test Accuracy:", 0.7775)

# accumulate model's accuracy
Tree_model3_CV_accuracy <- c(model_accuracy, predict_train_accuracy, predict_valid_accuracy, 0.7775)
names(Tree_model3_CV_accuracy) <- name
Tree_model3_CV_accuracy

##############################################
### Cross Validation on Random Forest Models
##############################################

# Random Forest model RF_model1_cv
# set seed for reproduction
set.seed(2307)
# RF_model1_cv <- train(Survived ~ Sex + Pclass + HasCabinNum +      Deck + Fare_pp,
#                        data = trainData,
#                        method = "rf",
#                        trControl = control)
# save(RF_model1_cv, file = "RF_model1_cv.rda")
load("RF_model1_cv.rda")

# Show CV mdoel's details
print(RF_model1_cv)
print(RF_model1_cv$results)

# Record model's accuracy
model_accuracy <- format(RF_model1_cv$results$Accuracy[2], digits = 4)
paste("Estimated accuracy:", model_accuracy)

### Access accuracy on different datasets
# prediction's Confusion Matrix on the trainData
predict_train <-predict(RF_model1_cv, trainData)
conMat <- confusionMatrix(predict_train, trainData$Survived)
conMat$table
# prediction's Accuracy on the trainData
predict_train_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("trainData Accuracy:", predict_train_accuracy)

# prediction's Confusion Matrix on the validData
predict_valid <-predict(RF_model1_cv, validData)
conMat <- confusionMatrix(predict_valid, validData$Survived)
conMat$table
# prediction's Accuracy on the validData
predict_valid_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("validData Accuracy:", predict_valid_accuracy)

# predict on test
predict_test <-predict(RF_model1_cv, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = as.factor(predict_test))
write.csv(submit, file = "RF_model1_CV.CSV", row.names = FALSE)

## test accuracy 0.74641
paste("Test Accuracy:", 0.7464)

# accumulate model's accuracy
RF_model1_cv_accuracy <- c(model_accuracy, predict_train_accuracy, predict_valid_accuracy, 0.7464)
names(RF_model1_cv_accuracy) <- name
RF_model1_cv_accuracy

###
# Random Forest model RF_model2_cv
##
# set seed for reproduction
set.seed(2300)

# RF_model2_cv <- train(Survived ~ Sex + Fare_pp + Pclass + Title + Age_group + Group_size + Ticket_class + Embarked,
#                        data = trainData,
#                        method = "rf",
#                        trControl = control)
# # This model will be used in chapter 12. so it is saved into a file for late to be loaded
# save(RF_model2_cv, file = "RF_model2_cv.rda")

load("RF_model2_cv.rda")

# Show CV mdoel's details
print(RF_model2_cv)
print(RF_model2_cv$results)

# Record model's accuracy
mode2_accuracy <- format(RF_model2_cv$results$Accuracy[2], digits = 4)
paste("Estimated accuracy:", mode2_accuracy)

### Access accuracy on different datasets
# prediction's Confusion Matrix on the trainData
predict_train <-predict(RF_model2_cv, trainData)
conMat <- confusionMatrix(predict_train, trainData$Survived)
conMat$table

# prediction's Accuracy on the trainData
predict_train_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("trainData Accuracy:", predict_train_accuracy)

# prediction's Confusion Matrix on the validData
predict_valid <-predict(RF_model2_cv, validData)
conMat <- confusionMatrix(predict_valid, validData$Survived)
conMat$table
# prediction's Accuracy on the validData
predict_valid_accuracy <- format(conMat$overall["Accuracy"], digits=4)
paste("validData Accuracy:", predict_valid_accuracy)

#predict on test
predict_test <-predict(RF_model2_cv, test)
submit <- data.frame(PassengerId = test$PassengerId, Survived = as.factor(predict_test))
write.csv(submit, file = "RF_model2_CV.CSV", row.names = FALSE)

## test accuracy 0.75119
paste("Test Accuracy:", 0.7512)

# accumulate model's accuracy
RF_model2_cv_accuracy <- c(mode2_accuracy, predict_train_accuracy, predict_valid_accuracy, 0.7512)
names(RF_model2_cv_accuracy) <- name
RF_model2_cv_accuracy

### make a comparison among tree and random forest models
library(tidyr)
Model <- c("Tree_M2","Tree_M3","RF_model1","RF_model2")

# Show individual models' accuracy
Tree_model2_CV_accuracy
Tree_model3_CV_accuracy
RF_model1_cv_accuracy
RF_model2_cv_accuracy

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

# show in table
knitr::kable(df1, longtable = TRUE, booktabs = TRUE, digits = 2, col.names =c("Models", "Predictors", "Accuracy on Learn", "Accuracy on Train", "Accuracy on Valid",  "Accuracy on Test"),
             caption = 'The Comparision among 4 CV models'
)

# plot results in bar chat
df.long <- gather(df2, Dataset, Accuracy, -Model, factor_key =TRUE)
ggplot(data = df.long, aes(x = Model, y = Accuracy, fill = Dataset)) +
  geom_col(position = position_dodge())

#############################################
## Multiple Models Comparison
#############################################
#
### Regression Model for Titanic
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

### Validate on validData
validData_Survived_predicted <- predict(LR_Model, newdata = validData, type = "response")
validData_Survived_predicted  <- ifelse(validData_Survived_predicted  > 0.5, 1, 0)  # set binary prediction threshold
conMat<- confusionMatrix(as.factor(validData$Survived),as.factor(validData_Survived_predicted))

Regression_Acc_Valid <-round(conMat$overall["Accuracy"]*100,2)
paste('Model Valid Accuracy =', Regression_Acc_Valid)

### produce a prediction on test data
library(pROC)
auc(roc(trainData$Survived,Valid_trainData))  # calculate AUROC curve
#predict on test
test$Survived <- predict(LR_Model, newdata = test, type = "response")
test$Survived <- ifelse(test$Survived > 0.5, 1, 0)  # set binary prediction threshold
submit <- data.frame(PassengerId = test$PassengerId, Survived = as.factor(test$Survived))

write.csv(submit, file = "LG_model1_CV.CSV", row.names = FALSE)
# Kaggle test accuracy score:0.76555

# record accuracy
Regr_Acc <- c(Regression_Acc_Train, Regression_Acc_Valid, 0.76555)

acc_names <- c("Train Accu", "Valid Accu", "Test Accu")
names(Regr_Acc) <- acc_names
Regr_Acc

### Support Vector Machine Model for Titanic
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

### Validate on validData
validData_Survived_predicted <- predict(SVM_model, validData) #produce confusion matrix
conMat<- confusionMatrix(as.factor(validData$Survived), as.factor(validData_Survived_predicted))
# output accuracy
AVM_Acc_Valid <- round(conMat$overall["Accuracy"]*100,4)
paste('Model Valid Accuracy =', AVM_Acc_Valid)

### make prediction on test
# SVM failed to produce a prediction on test because test has Survived col and it has value NA. A work around is assign it with a num like 1.
test$Survived <-1

# predict results on test
Survived <- predict(SVM_model, test)
solution <- data.frame(PassengerId=test$PassengerId, Survived =Survived)
write.csv(solution, file = 'svm_predicton.csv', row.names = F)

# prediction accuracy on test
SVM_Acc <- c(AVM_Acc_Train, AVM_Acc_Valid, 0.78947)
names(SVM_Acc) <- acc_names

# print out
SVM_Acc

### Neural Network Models
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

#How do we do on the valid data?
nn_pred_valid_class = predict(NN_model1, validData, type="class" )  # yields "0", "1"
nn_valid_pred = as.numeric(nn_pred_valid_class ) #transform to 0, 1
confusion_Mat<-confusionMatrix(as.factor(nn_valid_pred), validData$Survived)
# output accuracy
NN_Acc_Valid <- round(confusion_Mat$overall["Accuracy"]*100,4)
paste('Model valid Accuracy =', NN_Acc_Valid)

#make a prediction on test data
nn_pred_test_class = predict(NN_model1, test, type="class" )  # yields "0", "1"
nn_pred_test = as.numeric(nn_pred_test_class ) #transform to 0, 1
solution <- data.frame(PassengerId=test$PassengerId, Survived = nn_pred_test)
write.csv(solution, file = 'NN_predicton.csv', row.names = F)

###
# 0.8934,0.8547, 0.71052
NN_Acc <- c(NN_Acc_Train, NN_Acc_Valid, 0.71052)
names(NN_Acc) <- acc_names
NN_Acc

### Comparision among Different Models

library(tidyr)
Model <- c("Regression","SVM","NN", "Decision tree", "Random Forest")
Train <- c(Regression_Acc_Train, AVM_Acc_Train, NN_Acc_Train, 82.72, 83.16)
Valid <- c(Regression_Acc_Valid, AVM_Acc_Valid, NN_Acc_Valid, 81.01, 92)
Test <- c(76.56, 78.95, 71.05, 77.75, 78.95)
df1 <- data.frame(Model, Train, Valid, Test)

# show in table
knitr::kable(df1, longtable = TRUE, booktabs = TRUE, digits = 2, col.names =c("Models", "Accuracy on Train", "Accuracy on Valid","Accuracy on Test"),
             caption = 'The Comparision among 3 Machine Learning Models'
)

# plot in bar chat
df.long <- gather(df1, Dataset, Accuracy, -Model, factor_key =TRUE)
ggplot(data = df.long, aes(x = Model, y = Accuracy, fill = Dataset)) +
  geom_col(position = position_dodge())
### END 4 Model Cross Validation #############################################
```
## TitanicDataAnalysis5_Model_Fine_Tune.R {- #ModelFineTune}

```
###########################################################################
# Model Fine Tune
###########################################################################
#
###############################
### Tuning a model's Predictor
###############################
# load necessary library
library(randomForest)
library(plyr)
library(caret)

# load our re-engineered data set and separate train and test datasets
RE_data <- read.csv("RE_data.csv", header = TRUE)
train <- RE_data[1:891, ]
test <- RE_data[892:1309, ]

# Train a Random Forest with the default parameters using full attributes
# Survived is our response variable and the rest can be predictors except pasengerID.

rf.train <- subset(train, select = -c(PassengerId, Survived))

# we set rf.label for later use but it is the dependednt variable
rf.label <- as.factor(train$Survived)

# RandomForest cannot handle factors with over 53 levels
rf.train$Ticket <- as.numeric(train$Ticket)

set.seed(1234) # for reproduction
rf.1 <- randomForest(x = rf.train, y = rf.label, importance = TRUE)
rf.1

#rf.1 model with full house predictors has error rate: 15.49%

# Check the order of the predictors prediction power.
pre.or <- sort(rf.1$importance[,3], decreasing = TRUE)
pre.or
varImpPlot(rf.1)

## The idea in here is using the predictors order (prediction)
# to build all models each has one predictor less. so we can
# compare models to find which is has the best accuracy
# WE can take that model as the best to bench marking the predictor.

# rf.2 as an example
rf.train.2 <- subset(rf.train, select = -c(Parch))
set.seed(1234)
rf.2 <- randomForest(x = rf.train.2, y = rf.label, importance = TRUE)
rf.2
#The *rf.2*  model's accuracy 84.85%,  error` (15.15%).

### we can repeat the process to get all the models and their accuracy
# List themin a table
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

# load the best model and record its predictors
# save(rf.8, file = "rf_model.rda")
load("rf_model.rda")

Predictor <- c("Sex, Title, Fare_pp, Ticket_class, Pclass, Ticket, Age, Friend_size, Deck")
Predictor

#################################
### Tuning Training Data Samples
#################################
# Set Prediction Accuracy Benchmark

# Let's start with a submission of rf.8 to Kaggle
# to find the difference between model's OOB and the accuracy

# Subset our test records and features
test.submit.df <- test[, c("Sex", "Title", "Fare_pp", "Ticket_class", "Pclass", "Ticket", "Age", "Friend_size", "Deck")]
test.submit.df$Ticket <- as.numeric(test.submit.df$Ticket)

# Make predictions
rf.8.preds <- predict(rf.8, test.submit.df)
table(rf.8.preds)

# Write out a CSV file for submission to Kaggle
submit.df <- data.frame(PassengerId = test$PassengerId, Survived = rf.8.preds)

write.csv(submit.df, file = "RF8_SUB.csv", row.names = FALSE)
# After our submission we have scores 0.75598 from Kaggle,
# but the OOB predicts that we should score 0.8642.

#
### The 1 sampling methods: 10 Folds CV Repeat 10 Times
#
# check to see if the samples are the same or close to the same ratio
library(caret)
library(doSNOW)

set.seed(2348)
# rf.label is the Survived in the train dataset.
# ? createMultiFolds to find out more. train (891)
cv.10.folds <- createMultiFolds(rf.label, k = 10, times = 10)

# Check stratification: survived ratio in the train dataset
table(rf.label)
342 / 549

# check 10-folds random split each folds ratio (34 is an example)
table(rf.label[cv.10.folds[[34]]])
308 / 494
#confirmed the stratification both have the similer ratio

# Set up caret's trainControl object using 10-folds repeated CV
ctrl.1 <- trainControl(method = "repeatedcv", number = 10, repeats = 10, index = cv.10.folds)

# Model construction with "`10-folds repeated CV`" is a very expensive
# R has a package called **"doSNOW"**, that facilities the use of
# multi-core processor and permits parallel computing in
# a pseudo cluster mode

## Set up doSNOW package for multi-core training.
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
# # Set seed for reproducibility and train
# set.seed(34324)
#
# rf.8.cv.1 <- train(x = rf.train.8, y = rf.label, method = "rf", tuneLength = 3, ntree = 500, trControl = ctrl.1)
#
# #Shutdown cluster
# stopCluster(cl)
# save(rf.8.cv.1, file = "rf.8.cv.1.rda")
# Check out results
load("rf.8.cv.1.rda")
# Check out results
rf.8.cv.1

# prediction accuracy reduced from 0.8642 to 0.8511,
# but not pessimistic enough to the test accuracy, it is 0.75598.

#
### The 2 sampling methods: 5 Folds CV Repeat 10 Times
#
set.seed(5983)
# cv.5.folds <- createMultiFolds(rf.label, k = 5, times = 10)
#
# ctrl.2 <- trainControl(method = "repeatedcv", number = 5, repeats = 10, index = cv.5.folds)
#
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
#
# set.seed(89472)
# rf.8.cv.2 <- train(x = rf.train.8, y = rf.label, method = "rf", tuneLength = 3, ntree = 500, trControl = ctrl.2)
#
# #Shutdown cluster
# stopCluster(cl)
# save(rf.8.cv.2, file = "rf.8.cv.2.rda")
# # Check out results
load("rf.8.cv.2.rda")
# Check out results
rf.8.cv.2

# We can see that 5-fold CV is a little better.
# The accuracy is moved under 85%. The model's training data set
# is moved from 9/10 to 4/5, which is 713 now.

#
### The 3 sampling methods: 3 Folds CV Repeat 10 Times
#
set.seed(37596)
# cv.3.folds <- createMultiFolds(rf.label, k = 3, times = 10)
#
# ctrl.3 <- trainControl(method = "repeatedcv", number = 3, repeats = 10, index = cv.3.folds)
#
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
#
# set.seed(94622)
# rf.8.cv.3 <- train(x = rf.train.8, y = rf.label, method = "rf", tuneLength = 3, ntree = 500, trControl = ctrl.3)
#
# #Shutdown cluster
# stopCluster(cl)
#
# save(rf.8.cv.3, file = "rf.8.cv.3.rda")
# # # Check out results
load("rf.8.cv.3.rda")
# Check out results
rf.8.cv.3

# We can see the accuracy has further decreased (0.8387579).
# Let us also reduced the number of times that the samples
# are repeated used in the training (repeat times).
# Let us see if the sample repeat times reduce to 3,
# if the accuracy can be further reduced.

#
### The 4 sampling methods: 5 Folds CV Repeat 10 Times
#
# set.seed(396)
# cv.3.folds <- createMultiFolds(rf.label, k = 3, times = 3)
#
# ctrl.4 <- trainControl(method = "repeatedcv", number = 3, repeats = 3, index = cv.3.folds)
#
# cl <- makeCluster(6, type = "SOCK")
# registerDoSNOW(cl)
#
# set.seed(9622)
# rf.8.cv.4 <- train(x = rf.train.8, y = rf.label, method = "rf", tuneLength = 3, ntree = 50, trControl = ctrl.4)
#
# #Shutdown cluster
# stopCluster(cl)
#save(rf.8.cv.4, file = "rf.8.cv.4.rda")
# # # Check out results
load("rf.8.cv.4.rda")
# Check out results
rf.8.cv.4

#################################
### Tuning Model’s Parameters
#################################
# random  search to find mtry for RF

#library(caret)
#library(doSNOW)

### Random Search
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
# #save(RF_Random, file = "RF_Random_search.rda")
#
# #Shutdown cluster
# stopCluster(cl)

# Check out results
load("RF_Random_search.rda")
print(RF_Random)

# plot
plot(RF_Random)
### mtry =3 by random search

### Grid Search
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
# #save(RF_grid_search, file = "RF_grid_search.rda")

load("RF_grid_search.rda")
#print results
print(RF_grid_search)
#plot
plot(RF_grid_search)

##
### Manual Search we use control 1 random search
##  use n_tree in c(100, 500, 1000, 1500)
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
# save(model_list, file = "RF_manual_search.rda")
# # the above code comneted out for output book file

load("RF_manual_search.rda")
# compare results
results <- resamples(model_list)
summary(results)
dotplot(results)
# We can see with the default *mtry =3* setting,
# the best *ntree* value is 1500.
# The model can reach 84.31% accuracy

###submit the final result to Kaggle for evaluation
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
# save(Final_model, file = "Final_model.rda")
# # # the above code commented out for output book file

load("Final_model.rda")

# Make predictions
Prediction_Final <- predict(Final_model, test.submit.df)
#table(Prediction_Final)

# Write out a CSV file for submission to Kaggle
submit.df <- data.frame(PassengerId = test$PassengerId, Survived = Prediction_Final)

write.csv(submit.df, file = "Prediction_Final.csv", row.names = FALSE)

## We have got a score of 0.76076
##### End 5 Model Fine Tune ##############################################
```
## TitanicDataAnalysis6_Analyse_Report.R {- #AnalyseReport}

```
##########################################################################
# Reprot and futher improvment
# ####################################################
#prepare data for the code to be run independent of other chapters
data <- read.csv("data.csv", header = TRUE)
RE_data <- read.csv("RE_data.csv", header = TRUE)
train <- RE_data[1:891, ]
test <- RE_data[892:1309, ]

# model need to be loaded in to memory
load("RF_model2.rda")
RF_model2

# The model's estimated accuracy (by the model construction) is **83.16%**.
# The default parameters of the model are: `mtry = 2` and `ntree = 500`

# The Top 10 trees and the summary of OOB of RF_model2
head(RF_model2$err.rate, 10)
summary(RF_model2$err.rate)

#The cross validations on the model `RF_model2`,
load("RF_model2_cv.rda")
RF_model2_cv

# 2D Visualization of Model RE_model2's Prediciton
#install.packages("Rtsne")
# library(Rtsne)
# library(ggplot2)

# Rtsne needs a seed to ensure consistent output between runs.
set.seed(984357)
features <- c("Sex", "Fare_pp", "Pclass", "Title", "Age_group", "Group_size", "Ticket_class", "Embarked")
#generate 2-d coordinate
Model_tsne <- Rtsne(train[, features], check_duplicates = FALSE)
# Plot
ggplot(NULL, aes(x = Model_tsne$Y[, 1], y = Model_tsne$Y[, 2], color = as.factor(train$Survived))) +
  geom_point() +
  labs(color = "Survived")

# The importance of the predictorsRF_model2
# library(randomForest)
# library(caret)
varImpPlot(RF_model2, main = "")

# print out the values
pre.or <- sort(RF_model2$importance[,3], decreasing = TRUE)
print(pre.or)

###############################
## Further Analysis
###############################

# The decision tree of RF_model2
# library(rpart.plot)
load("model3.rda")
prp(model3, type = 0, extra = 1, under = TRUE)


## further re-engineering `Title` attribute
#let us further re-Engineer title check the value
table(RE_data$Title)

# Parse out title from the raw data
data$Title <- gsub('(.*, )|(\\..*)', '', data$Name)
table(data$Title)

# Further bin or bucket them into a more appropriate category
# We can do with the knowledge of nobility, locality (country of origin)
# and other knowledge such as time (at the beginning of the 20 century).
# For example, "`Dona`" and "`the Countess`" are female nobility equivalent
# to "`Lady`", and  "`Ms`" and "`Mlle`" are essentially the same with "`Miss`";
# "`Mme`" is a military title equivalent to "`Madame`", so it can be
# categorised as "`Mrs`"; "`Jonkheer`" is an honorific nobility in the
# Netherlands; and "`Don`" is title of a university lecturer, they can be
# categorises as "`Sir`"; "`Col`", "`Capt`", and "`Major`" are military
# ranks and can be replaced with a more general title "`Officer`".
# With all of these, we can reduce the numbers of title's category.

# Re-map titles to be more exact
data$Title[data$Title %in% c("Dona", "the Countess")] <- "Lady"
data$Title[data$Title %in% c("Ms", "Mlle")] <- "Miss"
data$Title[data$Title == "Mme"] <- "Mrs"
data$Title[data$Title %in% c("Jonkheer", "Don")] <- "Sir"
data$Title[data$Title %in% c("Col", "Capt", "Major")] <- "Officer"
table(data$Title)

# Collapse titles based on visual analysis
indexes <- which(data$new.Title == "Lady" |
                   (data$new.Title == "Dr" &
                      data$Sex == "female") |
                   (data$new.Title == "Officer"&
                      data$Sex == "female")
)
data$new.Title[indexes] <- "Mrs"

indexes <- which(data$new.Title == "Rev" |
                   data$new.Title == "Sir" |
                   (data$new.Title == "Officer" &
                      data$Sex == "male")|
                   (data$new.Title == "Dr" &
                      data$Sex == "male")  )
data$new.Title[indexes] <- "Mr"

table(data$new.Title)

# Check any other gender slip-ups?
length(which(data$sex == "female" &
               (data$new.Title == "Master" |
                  data$new.Title == "Mr")))
length(which(data$sex == "male" &
               (data$new.Title == "Miss" |
                  data$new.Title == "Mrs")))

# Visualize
ggplot(data[1:891,], aes(x = new.Title, fill = as.factor(Survived))) +
  geom_bar()

# check up the impact of the new-title
set.seed(2222)
RE_data$New_Title <- data$new.Title
RF_model2_new <- randomForest(as.factor(Survived) ~ Sex + Fare_pp + Pclass + New_Title + Age_group + Group_size + Ticket_class  + Embarked,
                              data = RE_data[1:891,],
                              importance=TRUE)

RF_model2_new

# The new model has increased the over accuracy with 0.45%.
# It is not a lot but it approves the point that features re-engineer
# is a place to do a model's performance improvement.
###########################################################################
```
