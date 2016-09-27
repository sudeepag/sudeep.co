---
title: Exploring Survival on the Titanic with Machine Learning
layout: post
comments: true
date: 2016-09-27 10:00:00 +GMT
author: Sudeep Agarwal
---

In the early morning of 15 April 1912, a British passenger liner sank in the North Atlantic Ocean after colliding with an iceberg. More than  1,500 passengers died in the sinking, making it one of the deadliest maritime disasters. Since then, the Titanic has become one of the most famous ships in history, her memory kept alive in various forms of pop culture, museums, books and films.

<div align="center">
  <img src="/images/post_images/2016-09-27-Exploring-Survival-on-the-Titanic-with-Machine-Learning/titanic.gif">
</div><br/>

We can use machine learning to explore some interesting questions. How much of role did a passenger's socio-economic status play on their chance of survival? Did their name or age make a difference? What about siblings, parents or children? Is one of these factors more significant than the rest? Using decision trees and a random forest model, we can analyze the passenger data from the ship, answer some of these interesting questions and create a classifier that can predict if a passenger survived the tragedy.

### Data Exploration
The dataset I'll be working with in this blog post is from the [Kaggle competition](https://www.kaggle.com/c/titanic) *Titanic: Machine Learning from Disaster*, which is conveniently classified into training and testing data. But before we start working on our classifier, let's first attempt to understand and visualize the data.

If you load the training data into a `pandas` dataframe, you'll realize that there are 12 columns. 

- `survival` A flag indicating if the passenger survived the sinking  (0 = No; 1 = Yes)
- `pclass` The passenger's cabin class, which is an indication of socio-economic status (1 = 1st; 2 = 2nd; 3 = 3rd) 
- `name` Passenger's name
- `sex` Passenger's gender
- `age` Passenger's age
- `sibsp` Number of siblings and spouses the passenger had on board
- `parch` Number of parents and children the passenger has on board
- `ticket` Passenger's ticket number
- `fare` The fare that the passenger paid for passage
- `cabin` The cabin that the passenger stayed in
- `embarked` The passenger's port of embarkation  (C = Cherbourg; Q = Queenstown; S = Southampton)

Let's first take a look at the demographics of the population to understand the breakdown of all the people on board.

<iframe width="800" height="500" frameborder="0" scrolling="no" src="https://plot.ly/~sagarwal88/188.embed"></iframe>

From the plot, we can see that the majority of the passengers were adults between the ages of 18 and 35. The mean age turns out be 29.7, with the most common age being 24. In total, there were 577 males and 314 females in this particular dataset, which explains why the number of males seems to be higher than females on average in each age group.

Let's analyze the average survival rate of the passengers between age and social class groups. 

<iframe width="800" height="400" frameborder="0" scrolling="no" src="https://plot.ly/~sagarwal88/228.embed"></iframe>

This is an interpolated heatmap of average survival rate for each age and social class group. We can see a very interesting trend here - as the age increases and the passenger class decreases (recall that class 1 is the best, and class 3 is the worst), the average survival rate decreases. In fact, the average survival rate for the worst passenger class is generally lower than that of the best passenger class. This brings up an interesting discussion about social class, and points us in a direction where we can ask our next question. 

### How Much Does Social Class Matter in a Disaster?

In any disaster or tragedy, every split second makes a difference. Each decision made could be the difference between life and death.  As the Titanic sank, who was allowed on to the life rafts, and who wasn't, largely determined who lived and who died. This brings up an interesting discussion about social class, and if that was a factor in determining who was allowed on to the life rafts. 

Were the passengers who had paid a higher fare, and lived in a higher class cabin allowed to board the life rafts before the passengers who had bought the cheapest tickets? If so, is this trend visible in the survival rates for each cabin class? Let's take a look.

<iframe width="800" height="500" frameborder="0" scrolling="no" src="https://plot.ly/~sagarwal88/224.embed"></iframe>

One of the most obvious observations is that a signifciantly greater percentage of women survived the sinking than men, regardless of their socio-economic status. The percentage of women who made it out of the ordeal alive is nearly twice or thrice as much as the percentage of men. One possible reason for this could be that in disasters like this, women and children are often directed to safety first. In a situation where every second matters, this could have led to a larger proportion of women being saved as they were sent to the lifeboats first.

But what about within the different social classes? As can be seen from the plot, the general trend for both men and women is that the passengers who lived in the more expensive cabins, and thus were more likely to have a higher socio-economic class, were also more likely to make it out alive. This is a stark reflection of our society even today, where people with a better socio-economic status often have better access to health care, healthier food, and ultimately have a better quality of life and a longer life expectancy.

We've seen that social class did in fact affect a passenger's chances of survival, but what about the other factors. More importantly, which factor had the most significant effect?

### Random Forests in Machine Learning

In any prediction problem, there are several approaches you can take to build your classifier. One category of approaches is called *ensemble learning*, which involves the combination of several models to solve a single prediction problem. The notion behind ensemble learning is that if we make a prediction using each individual model, and then combine all of these predictions, we get a prediction that would be as good or better than each of the individual predictions. 

Random forest is a type of ensemble learning that is commonly used for *feature selection*. In machine learning, we often want to know which features, or variables, are the most significant. In the case of the Titanic dataset, we can use random forests to find out which variable affected a passenger's survival the most, and which factor had almost no effect on the survival. Before we do that though, let's briefly take a look at how random forests work.

Using random samples from the data, we first construct decision trees, which are graphs that model decisions and their possible consequences. 

<div align="center">
  <img src="/images/post_images/2016-09-27-Exploring-Survival-on-the-Titanic-with-Machine-Learning/decisiontree.png" width="600px">
</div><br/>

In the decision tree above, for example, if an observation has a length less than 50, has blue eyes and 2 legs, it is going to be classified as *red*. In the case of our Titanic dataset, we can create similar decision trees with variables like the passenger's age and gender, the fare they paid, whether they were in a family or had children, and their social class. The labels, in this case, would simply be binary flags indicating if the passenger survived or not. 

<div align="center">
  <img src="/images/post_images/2016-09-27-Exploring-Survival-on-the-Titanic-with-Machine-Learning/randomforest.png">
</div><br/>

Since we're constructing decision trees from random samples, we'll get a bunch of random decision trees. As mentioned before, random forest is a type of ensemble learning - most of our trees will not be meaningful in the learning of survival patterns, but there's a good chance that some will yield really good predictions, and that's all we need. The predictions from all of our trees are combined and we get the random forest's overall prediction, which ends up being pretty accurate as we will see later.

Fortunately for us, scikit-learn has a random forest classifier, `sklearn.ensemble.RandomForestClassifier`, that we can use for this task instead of coding it from scratch. But before we construct the decision trees and make the predictions, let's look at another very important aspect of machine learning - feature engineering.

### Constructing Features for Insightful Predictions

According to Wikipedia, feature engineering is the process of 'using domain knowledge of the data to create features that make machine learning algorithms work.' It is the process of transforming our raw data into features that effectively represent the underlying problem we are trying to solve, resulting in a better accuracy in our prediction model. What does this mean in the context of this project? A *good* set of features would be one that effectively represents the passenger dataset that we have as a whole. Feature engineering is often referred to as an art, something that comes with a lot of practice. The features I have chosen to construct in this post are by no means the best possible representation you can have for this dataset, but I believe they give a pretty accurate representation for the questions we're trying to answer.

The features I've chosen are:

- **Male** (binary flag determining if the passenger was male)
- **Female** (binary flag determining if the passenger was female)
- **Family** (binary flag determining if the passenger was with family)
- **Child** (binary flag determining if the passenger was a child)
- **Class_1** (binary flag determining if the passenger has a 1st class ticket)
- **Class_3** (binary flag determining if the passenger has a 3rd class ticket)

I won't go too much in depth into constructing the features, since it mainly involves `pandas` dataframe manipulation. Once you move the columns around, and manipulate them to get the features that we want, you should get a dateframe that looks something like this.

<div align="center">
  <img src="/images/post_images/2016-09-27-Exploring-Survival-on-the-Titanic-with-Machine-Learning/table.png">
</div><br/>

### Training the Classifier

We're now ready to train the random forest classifier. To fit our data, we need two inputs to the fitting function - the training input samples (which is the dataframe we just created), and the target values. Since we want to predict the survival of the passengers, our target values will simply be booleans indicating if the corresponding passenger survived.

{% highlight python %}
	from sklearn.ensemble import RandomForestClassifier

	rf =  RandomForestClassifier()
	rf.fit(train_x, train_y)
{% endhighlight %}

Once we've created our classifier, we can fit our data using the two inputs, `train_x`, which is the features dataframe, and `train_y`, which is the target survival values. If you print `rf.score(train_x, train_y)`, depending on how you implemented your features and set up your random forests, you'll get a value which denotes the mean accuracy on the inputted test data and labels. In this example, I got a score of `0.810325476992`, which is not too bad (but could definitely be improved).

### Which Feature is the Most Important?

Here comes the exciting part - ranking the features! To get the relative importances of the features,

{% highlight python %}
	sorted(list(zip(classifier.feature_importances_, train_x.columns.tolist())),
		reverse=True)

	>> [(0.39221793519612758, 'Male'),
 	   (0.20912367099165802, 'Female'),
 	   (0.18058298031704362, 'Class_3'),
 	   (0.11673747109412265, 'Class_1'),
 	   (0.0659656702541013, 'Child'),
 	   (0.035372272146946729, 'Family')]
{% endhighlight %}

What does this tell us? Intuitively, the higher the importance of a feature, the more important it is. Let's briefly discuss the information that this ranking of the features gives us.

<iframe width="800" height="500" frameborder="0" scrolling="no" src="https://plot.ly/~sagarwal88/227.embed"></iframe>

#### Gender
`Male` has the highest importance, and is relatively much higher than `Female`. This tells us that being male affected survival to a higher degree than being female. While this doesn't tell us if being either male or female made a person more likely to survive, it does tell us that being male had more of an impact on a passenger's survival (regardless of whether he ended up surviving or not)

#### Social Class
`Class_3` has a higher importance than `Class_1`. This means that being a passenger in the lowest class had a greater effect on a passenger's survival than being a passenger in the highest class. This could perhaps be because the variance in survival amongst passengers in the first class is higher than that of the passengers in the third class, i.e. if a passenger was in the third class, he or she was more likely to not survive.

#### Other Factors
The importance of `Child` and `Family` are both relatively low compared to the importance of the other features. We can draw the conclusion that being a child, or being with a family on board did not have as significant of an effect on a a passenger's survival as compared to the other features.

### In Conclusion

This has been a very fun and insightful exploration into machine learning, random forests and decision trees. By analyzing the relative importance of factors like gender and social class in determining survival, we can get a clearer picture of the disaster and get some intuition as to how it played out.