# Understand Problem {#prob}

\begin{figure}

{\centering \includegraphics[width=0.8\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/Titanic} 

}

\caption{The sink of Titanic. Credits: Canoe1967/wikipeida.org}(\#fig:unnamed-chunk-1)
\end{figure}

Most of Data Science projects initiated by business organizations and having specific objectives. The list of the objective can be endless ranging from finding out problems, abstract patterns, and predict the future. It is essential to have fully understood problems and well-defined objectives for a successful data science project. In this book, I will choose the famous "Titanic problem".   
The sinking of the RMS Titanic occurred on the night of 14 April 1912 in the North Atlantic Ocean, four days into the ship's maiden voyage from Southampton, UK to New York City, USA. The largest passenger liner in service at the time, Titanic had an estimated 2,224 people on board when she struck an iceberg at around 23:40 (ship's time) on Sunday, 14 April 1912. Her sinking two hours and forty minutes later at 02:20 (05:18 GMT) on Monday, 15 April resulted in the deaths of more than 1,500 people, which made it one of the deadliest peacetime maritime disasters in history.

Later, in 1997 American film director James Cameron turned this disastrous and tragic event into an epic romance film. The film star Leonardo DiCaprio and Kate Winslet's outstanding performance in the film makes it a best-selling movie in the year 1997. 

Perhaps people are touched not only by the love story but also by the humanity norms in the life and death situation that is famous - **"Lady and children first"**. Now it is considered "the motto of the sea".


## Kaggle Competion {#Competion}

Kaggle (https://www.kaggle.com/), a subsidiary of Google LLC, is the world's largest data science community with powerful tools and resources to help you achieve your data science goals. Kaggle was founded in 2010 with the idea that data scientists need a place to come together and collaborate on projects, learning new techniques, and share each other's experiences. This has transformed into a network with more than 1,000,000 registered users and has created a safe place for data science learning, sharing, and competition.  

Using the human competitive spirit, Kaggle created a platform for organizations to host competitions that have fuelled new methodology and techniques in data science, and given organizations new insights from the data they provided. 

\begin{figure}

{\centering \includegraphics[width=0.8\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/Kagglecomp} 

}

\caption{Kaggle Competition web site}(\#fig:unnamed-chunk-2)
\end{figure}

Generally, each competition has a host, and each host has to prepare and provide data. When providing data, the host has the opportunity to give additional information such as a description, evaluation method, timeline, and prize for winning. Although this may not be an ideal real-world data problem, which data scientists may face in the business. But it provides a good starting point for learners. In the real world, you may need to start by understanding the business and find data sources by yourself. Although the competition host has provided data. You cannot assume the data provided are clean data and ready for analysis. Cleaning and preprocess data are part of the competition. Therefore, any solution can be tested to see how good a participant is with the whole process of a data science project.

## Titianic at Kaggel

Titanic perhaps is the oldest and most participated competition on the Kaggle competition site. Even Kaggle used it as a sample project to show how people can participate in a competition and submit their results. 

\begin{figure}

{\centering \includegraphics[width=0.8\linewidth]{C:/Users/gangmin.li/Documents/Books/Bookdown-Do-Data/images/Titaniccompetition} 

}

\caption{Kaggle Competition on Titanic}(\#fig:unnamed-chunk-3)
\end{figure}

We take Titanic as an example through this tutorial because of the following reasons:

1. The story is well known and easy to understand and communicates any actions and the cause of the actions in the analysis process. 
2. The competition has the largest participants, so any issues are most likely have been studied already. So explore the discussion and other sources that can help to solve any problem you may have.
3. It is well studied, so there are plenty of alternative training materials available for your reference. 
4. Lastly, the problem itself is an interesting one that has the characteristic of only has a better solution and no best solution. So people are still working in it and uses the latest technologies. 

## The Titanic Problem

The objective of the Titanic problem defined on the Kaggle website as stated in the following:

"The sinking of the Titanic is one of the most infamous shipwrecks in history.

On April 15, 1912, during her maiden voyage, the widely considered “unsinkable” RMS Titanic sank after colliding with an iceberg. Unfortunately, there weren’t enough lifeboats for everyone on board, resulting in the death of 1502 out of 2224 passengers and crew.

While there was some element of luck involved in surviving, it seems some groups of people were more likely to survive than others.

In this challenge, we ask you to build a **predictive model**\index{predictive model} that answers the question: “what sorts of people were more likely to survive?” using passenger data (i.e. name, age, gender, socio-economic class, etc.)."

### The Challenge {-}

The competition is simple: we want you to **use the Titanic passenger data** (name, age, price of the ticket, etc.) to try to **predict who will survive and who will die**.

The requirement is to predict passengers' ** survival**. Like many other real data science problems, [Prediction](#predictive) is to build a model which takes input data and produces an output. A prediction model is a mathematical formula that takes input from historical facts reflecting past events and produces an output that to make predictions about future or otherwise unknown events. A simple way to understand a model is to think a model in the following three ways:

1. The relationship between input and output can be expressed by some kind of a math formula. It is generally called a definable model, the math formula can be as simple as a function of Polynomial expression or as complected as a regression model, or other statistical models.
2. Some models can not be explicitly expressed with math formulas, instead, they are expressed in rules. those are rule-based models. 
3. Other models can not be expressed in a math formula nor in rules. The solution is to build a neural networks\index {neural networks} to do prediction. A Neural network can be regarded as a "black box", which takes input and produce output, the internal connections are transparent to users. Machine learning is more focused on models rooted in Neural networks. 

Any model fundamentally expresses relationships between inputs and outputs. So as part of understanding the problem, We could interpret that the Kaggle Titanic challenge is to find creditable relationships between input data and output data (which survive or not). Once the relationship is found, we can express using either a math formula, a set of rules, or a Neural Network model.

### The Data {-}

Kaggle competition usually provides competition data. There is a "Data" tab on any competition site. Click on the Data tab at the top of the competition page, you will find the raw data provided and most of the time there is a brief explanation of the data attributes^[We have used Data Science terminology here. Data represent objects in the natural world. Object properties are represented by attributes. That is a data record has a number of attributes representing a natural object with a number of properties. records are also called observations or samples in statistics, the property is also called variables, parameters or dimensions] too.  

There are three files in the Titanic Challenge: 

(1) train.csv, 
(2) test.csv, and 
(3) gender_submission.csv.

The training set is supposedly used to build your models. The training set provides the outcome (also known as the “ground truth”) for each passenger. Your model will be based on attributes like passengers’ gender and class. You can also use feature engineering to create new features.

The test set should be used to see how well your model performs on unseen data. For the test set, there is no ground truth for each passenger is provided. It is your job to predict these outcomes. For each passenger in the test set, use the model you trained to predict whether or not they survived the sinking of the Titanic.

The data sets have also include gender_submission.csv, a set of predictions that assume all and only female passengers survive, as an example of what a submission file should look like.

### The Submission {-}

Submission at the Titanic competition is equivalent to the requirements on the final report of any data science project. that is one of the questions you need to understand at the beginning of the project. 

Titanic competition requires the results need be submitted in the file. The file structure is demonstrated in the "gender_submission.csv". It is also provided as an example that shows how you should structure your results, which means predictions. 

The example submission in "Gender_submission" predicts that all female passengers survived, and all male passengers died. It is clearly biased. Your hypotheses regarding survival will probably be different, which will lead to a different submission file. Properly it is a good idea now to rename the "Gender_submission.csv" file into "My_submission.csv" now. So you know that you have to submit "my_submission.csv" as the final report of your project and the submission indicates the completion of your project. 

\BeginKnitrBlock{rmdaction}
Do it yourself:

1. Download data file from Kaggel web site.(https://www.kaggle.com/c/titanic/data)
2. Unzip it into your working directory (eg. "./data/").
3. Rename "Gender_submission.csv" file into "My_submission.csv".

\EndKnitrBlock{rmdaction}

Make sure your submission should have:

1. "PassengerId" column containing the IDs of each passenger from test.csv.
2. "Survived" column (that you will create!) with a "1" for the rows where you think the passenger survived, and a "0" where you predict that the passenger died.

## Summary {-}

The purpose of this book is to provide a hand on practical exercise in doing a data science project. Clearly, we cannot cover the complete available methods, models, and algorithms for a data science project. The most important thing is to understand the process of doing a data science project. The first step, as indicated by the 6-step process in section \@ref(process), is "understand the problem". 

We have chosen to use the Titanic problem to demonstrate the whole data analytical process. However, a real-world problem is far more complicated than this well-defined problem. Most business organizations may not know the exact problem (that is part of the reason why they want to do data analysis or business analysis) or they know the problem (in general) but the problem can not be expressed explicitly. 

I have met a situation that a business organization that has created a data center and collected all their business operational data. The boss asked to analyze these data and find:

1. Is there are problems? 
2. If yes, how to overcome these problems?
3. If not, how to improve the business operations?

You see, here the problem is how to define the problem? how to convert the business problem into a data science problem.

For example, the first problem in the above list needs to know what is the normal or expected performance? How to evaluate the performance? In terms of turnover or profit? In what time scale? It could be short of profit at the moment but it not causes alarm because of the recent investment for developing a new market. In a long run, it will have a great ROI (Return on Investment). The second problem demands to identify the cause of the problem and the third to identify the KIP (Key Performance Indicators). they are both to identify the relationships between predictor and dependent variables. But they can be completely different sets.  

Understand problem is actually more complicated in the real world. Until you have completely understood it and turned it into a list of analytical problems you can move to the next step.

With the Titanic problem, combining the story and the requirements on the Kaggle website, I would consider these:

- On April 14 and 15, 1912, during her maiden voyage, the Titanic sank after colliding with an iceberg, killing 1502 out of 2224 passengers and crew. The overall survival rate is 32%.
- One of the reasons that the shipwreck led to such loss of life was that there were not enough lifeboats for the passengers and crew.
- Although there was some element of luck involved in surviving the sinking, some groups of people were more likely to survive than others, such as women, children, and the upper-class.
- The story tells us that when they were getting on board the lifeboats, they applied a policy of "women and children first" and also "the ship crew is the last".
- Sometimes the family was boarding the lifeboat together and some of the family members were swimming together too. 

Those thoughts form some kinds of assumptions in mind. They will guide more detailed data explorations later. 

## Exercises  {-}

1. Go to Kaggle website, explore the challenge and data provided for the Titantic problem.
2. Based on what you know about the Titanic story, who you think can survive? can you describe the people you believe can survive in terms of age, sex, cabin, title, social status?
3. Generate 3 assumptions of the Titanic problem, thinking of how could you possibly prove or disprove them.
4. Looking into data given by the Titanic competition, what is your impression of the data?

