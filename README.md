# A movie is born: how to make the most profitable movie?

> Author: Zhuoying "Joy" Lin; Yasi Chen; Lindsay Trinh

As the box office of Avengers: Endgame reached a historical high in the worldwide, people were once again impressed by how lucrative a “right” movie could be and many investors rushed to find the next “right” movie to invest. While factors such as the budget of the movie or the popularity of the casts seem to be the logical contributors to the revenue of a movie, our generation has witnessed many surprisingly unsuccessful movies despite high expectations, such as The Tourist. Is the movie industry indeed so unpredictable that no such factors exist that can ever ensure the revenue of a movie?  

Our team aims to achieve effective planning, both long-term planning accordingly to a strategy and short-term planning that well respond to dynamic market situations. The core task of this project is to identify the factors that have impacts on the revenue of a movie and the suggestions on how to apply such information to effectively structure future movie campaigns or to strategically evaluate investment potentials. Those who work in the industry will benefit from our insights to focus the real revenue boosters, while those who are seeking investment opportunities will thrive to know how they can effectively evaluate a movie proposal in front of them.

## Data understanding & Preparation

We obtained the MovieLens1 dataset from grouplens.org developed by the University of Minnesota. It was generated on November 21, 2019, and contains movies rated by users who have rated at least 20 movies. In compensating for the lack of movie background information in the original dataset, we used API to collect extra information from [The Movie DB](https://www.themoviedb.org/). We are particularly interested in the information such as the number of high profile actors and actresses as it is an easy market point, as well as keywords, overviews, and reviews as we can use the insights from these variables to create more appealing marketing campaigns. 

###### Data Understanding 

Even though watching movies is a common activity in our daily life and we are so used to it, there are three main occasions that will drive a person to watch a specific movie: 

1. Word of mouth. As one of the most common reasons to watch a movie, a marketing company has controls over it to some degree. While we cannot control what the general public thinks about our movie, we are indeed able to present our movie in the way that we want the audiences to perceive. In order to do this, we can analyze the reviews from previous movies to understand what kind of words are positively correlated to the higher revenue. Later in our modeling section, we will investigate the impact of the percentage of positive words for reviews. 

2. Domain expertise. Movies are artistic subjects and rely strongly on the story itself. A movie can have a big budget, top directors, and most talented actors and actresses but terrible story that pushes the audiences away. While marketing company has very little controls over the story, we can potentially attract experts to watch our movie by conveying an appealing story. As a result, we will conduct sentiment analysis and topic modeling on the overview of the movie to identify some key components that affect the revenue.  

3. Random picks. Many people like to hang out at the movie theaters and decide the movie to watch based on the casts, directors or genres. In that case, if the movie has the famous casts or director or belongs to trendy genre groups, it will be likely to be selected. Thus, those variables may be significant to the revenue of a movie. We can examine the causal relationship between cast and revenue to see if popular casts help increase the movie revenue.  

###### Data Exploration 

1. K means 

We decided to use K-means method to examine the relationship between the popularity and the vote average (rating) of a movie and its impact on revenue. We divided the dataset into two parts, one with revenue higher than average and the other with revenue below average. After implementing the elbow method, we found that 6 would be the optimized numbers of clusters for the analysis. 

<p align="center">
  <img src="https://media-exp1.licdn.com/dms/image/C4E12AQFIJSSBogYh8g/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=McWWrgpKGzuojEeV60VJ5JPyUGog55A37v0PPcTbzIU">
</p>

When we cluster the movies into 6 different groups, the red group represents 2733 movies which are not popular and have medium vote average, the green group represents 2609 movies with very low popularity and very high vote average, the blue group represents 1817 movies with very low popularity and vote average, the light blue group represents 673 movies with medium popularity and low vote average, the purple group represents high popularity and vote average and the yellow group represents medium popularity and low vote average.

<p align="center">
  <img src="https://media-exp1.licdn.com/dms/image/C4E12AQEf3URGG2WCJA/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=kZcFe-BhfsdjFe8WlpLi9axMTkTcEg0LaFd4xpEd6Vk">
</p>

We draw some interesting insights from the k-mean clustering analysis. The red and green groups, which account for 60 percent of the dataset, have a very low chance of getting high revenue even though they have very high ratings. Despite being popular and high-rated, the chance of generating high revenue of the purple group is still lower than the yellow group, which consists of movies with medium popularity and low ratings. Therefore, our conclusion is that vote average and popularity are not significant in determining revenue and shall be excluded from the model.

2. Topic Modeling

As the overview of a movie plays an important role to attract domain experts to watch the movie, if we can convert each overview to a mixture of topics with different probabilities, we can quantify overviews and compare them across movies to understand its impact on revenue. Topic Modeling stands out as the best model to achieve this goal. However, the differences among topics are not significant enough to differentiate each other clearly. For example, the top 10 terms for the first six topics are highly overlapped in “love”, “family” and “young”. The gamma – the probability of each topic for each overview – presents similar results. The probability of each topic for each overview varies from 0.024 to 0.032, which means the overview is relatively neutral to each topic. Therefore, Topic Modeling will not add many insights to our analysis, so we decided not to include the probability of each topic in revenue prediction.

<p align="center">
  <img src="https://media-exp1.licdn.com/dms/image/C4E12AQEzKqLRBv6TVw/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=9zrhYkYFiJpHAkXIer52rqa1agm9Hzt0c7QKP8o1-yI">
</p>

###### Data Cleaning

While the casts of each movie are available, it is unrealistic to interpret the impact of each actor and each actress. Therefore, we obtained a list of “[Top 1000 Actors and Actresses](https://www.imdb.com/list/ls058011111/)” from [IMDb](https://www.imdb.com/) and made the top 100 A-list, top 101-200 B-list, the rest 800 C-list and those not in the list D-list. We counted the number of people in each group. We want to check if a higher number of A-list stars in the movie are associated with higher revenue.

To ensure revenue accuracy, we removed any movies that were released in the fourth quarter of 2019. Since our dataset contains movies randomly picked from as old as a hundred years ago, the release year of the movie is not valuable for us to predict future revenue. Therefore, we converted the release date to the release month to understand the impact of seasonality on the revenue.

As each movie is assigned one or more keywords, we did Feature Engineering for keywords and removed the keywords with less than 1% appearance of all the movies. Even though some movies are so special that a unique keyword can explain their high revenue, such cases are not in our control as our goal is to identify general factors that impact the revenue the most. On the other hand, such cases are so rare that they are purely outliers that can have little effect on our models. 

During the data exploration process, we realized Topic Modeling is not valuable for the overview. Therefore, we conducted sentiment analysis to quantify overviews, and add the scores of ten emotions to the dataset. Similarly, the reviews of each movie have different levels of positive sentiment so that we created a variable to include the percentage of positive words for reviews of each movie. Such conversion lost statistical information such as the number of reviews for each movie. Therefore, we calculated the number of keywords, the number of words per overview and the length of each overview to weigh the effect of sentiments.

## Modeling & Evaluation

In order to evaluate the performance of our models, we decided to use out of sample (OOS) R2 as the performance metric with the R2 of the null model as the benchmark. To eliminate the effect of randomness, we ran 10-fold cross-validation and compared the average R2 for five different models – Linear Regression, Lasso, Post-Lasso, Random Forest, and Extreme Gradient Boost. Based on the OOS R2, we observe models are equally or slightly more efficient than the null model except for the Random Forest because it works best for classification.

<p align="center">
  <img src="https://media-exp1.licdn.com/dms/image/C4E12AQG5q831DUM2xw/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=4Dm-VKOaImGpyDS7z0ss45R58uFF8nl6bc3d3SDTulo">
</p>

Amongst all other models, the Linear Regression model explains the same percentage of variance as the null model. Lasso and Post-Lasso models exceed the null model by less than 1% so that it does not outrun much as we expected. We believe the week improvement of efficiency on these models over the null model is because the dataset itself contains too many dummy variables, especially those for overviews and keywords. Those non-continuous variables bring complexity to the model but models such as Linear Regression are not designed to handle text variables. Thus, they aren’t appropriate for a dataset with a sparsity of 77.79%. Therefore, advanced machine learning technique – Extreme Gradient Boost – is preferred and it does outperformance all other models. While it doesn’t significantly improve R2, every small improvement on the accuracy of such a complex problem worth thorough investigation, and we will go through each model to uncover some useful insight for the business. 

###### Model1: Linear regression

We first ran Linear Regression with the hope to discover direct interpretation between independent variables and revenue. After running the model using all the variables, we realized that the model does not work well in addressing the aforementioned business issue. Only 23.68% of the variables are statistically significant at 0.05 level, and 7.01% if we use Bonferroni since it ensures the significance of the overall model with a large number of variables.

Unfortunately, not all of the significant variables can well explain their impact on the revenue. On the one hand, for example, if a movie belongs to a collection (Toy Story, Star Wars and so on), it will increase the revenue by 36.27 million dollars. Such discovery aligns with the impact of keyword “sequel”. If a movie has “sequel” as one of the keywords, the revenue tends to be 22.94 million higher. It makes some sense since the movies that have their own collections must be more popular, and the producers can leverage that popularity to make the sequel. On the other hand, the coefficient for positive review contrasts to common sense that if a review of a movie has 1 percent more of positive words, the revenue will reduce by 99.7 million dollars. What’s more, if the movie has 1 more A-list star, its revenue will go down by 5.73 million dollars.

###### Model 2: Lasso

Lasso and Post-Lasso models penalize the number of variables and take the model efficiency into consideration at the same time. The higher the lambda, the lower the number of variables, the greater the loss of variation. With the lambda growing, the linear regression model variable’s number decrease to one. According to the fitting graph for CV Lasso, instead of choosing a lambda at mean square error (530283.6) level with 83 significant non-zero coefficients variables, we can have a much lower number of variables (3 in total) from the model at the first standard error level (16575159) to achieve a similar result. Compared to the Lasso selection, the simple linear regression model tends to be fit narrowly with a huge burden of variables and therefore leads to a clumsy product.

The model with lambda at min level chooses more than 80 variables, including many keywords, cast and many other variables which are highly sensitive case by case, on that even with the regulation of the lambda, the variables number in the model are unnecessarily high. One interesting observation from the lambda at min level lasso model: if released in April, November, and December, the revenue can be 7million, 2 million and 6 million higher compared to January, holding other variables constant. For the movie investor, they may want to choose the holiday month to release the movie to capture the leisure time and money from the customers.

<p align="center">
  <img src="https://media-exp1.licdn.com/dms/image/C4E12AQGPEJPZ-OtqyQ/article-inline_image-shrink_400_744/0?e=1587600000&v=beta&t=Dh0lmpUNZiGQNdpJjauSyJcXa4lF0FrfUI9BXwjDbPQ">
</p>

###### Model 3: Post-Lasso

We also introduced Post-Lasso model based on previous Lasso, which regulates the linear regression model further, and the post lasso model performs well in this case with 76% of OOS R square. The Post-Lasso model with lambda at one standard error level extracts three variables, including the budget, vote count and collection. From the Post-Lasso (lambda at 1se), one more dollar invested in the movie, the mean of the revenue prediction will be 1.73 dollars higher, holding other constant. For the movie investors, they can invest more to retain higher net present value of the movie project to reap the profit from the promising market. Vote count is related to the popularity of the movie, the more popular the movie is, the more people will want to watch the movie. Therefore, for the investor, the investment should also focus on advertisement or sales gimmick before and after the release of a movie to boost the hit of the movie.

###### Model 4: Random Forest

With the cross-validation approach, we find out that Random Forest model performs less efficiently compared to the lasso model, post lasso model or even the simple linear regression model. Random Forest, due to its high accuracy and ability to handle large features with small samples, serves as a decision tree for non-linear regression model, and therefore it would be better used for the non-linear prediction and not the revenue prediction in our case. Compared to other models of linear regression prediction, Random Forest performs poorly (67%) in the revenue prediction compared to the null model, and the other linear regression models all meet 70% at the same time.

###### Model 5: Extreme Gradient Boost

Boosting is a family of techniques that provide power to Machine Learning Models to improve their accuracy of prediction by converting weak learners to strong learners. Extreme Gradient Boost gives the flexibility to handles regression. Thus, it is appropriate for our purpose. Three options are available for measuring feature importance in XGBoost:

1. Frequency. The number of times a feature is used to split the data across all trees.

2. Gain. The average training loss reduction gained when using a feature for splitting.

3. Cover. The number of times a feature is used to split the data across all trees weighted by the number of training data points that go through those splits.

<p align="center">
  <img src="https://media-exp1.licdn.com/dms/image/C4E12AQG9sBVuuwHKVA/article-inline_image-shrink_1500_2232/0?e=1587600000&v=beta&t=M7QizFik71bos1sTjjGGWkOVdgh73qqKhm0Cq-ToxH8">
</p>

For all three methods, budget and vote count are the most important variables, which aligns with our intuition. Big budget movies tend to have higher revenue, otherwise, no one is ever going to invest in any big-budget production. The higher the number of votes usually means the more people watched the movie, which leads to more revenue. More importantly, run time, release month, if a movie belongs to a collection, the ratio positive words in reviews, overview word count, keyword count also play important roles in revenue predicting and are more actionable as a marketing company.

###### Model 6: Causal Model

To further investigate the variables above, we established Causal Model to test the causal relationship between the top variables and the revenue. The treatment variables such as collection and cast show some causal relationships with the revenue. Take collection as an example, if the movie is within a collection like the Star Wars series, it will induce more revenue than those single production movies. On the contrary to the simple linear regression model, the cast plays a significant role to boost the revenue. Therefore, if presented the chance to invest in the movie, an investor should choose those series movies and A-stars to profit more. Other treatments like certain words in the overview show no significant causal relationship to the revenue. Because the overview is usually short, people may not pay attention or get much information from it, and therefore play no causal role in generating revenue.

## Deployment

1. All our analysis shows that big-budget sequel movies indeed take more advantage of getting higher revenue. We can even find the causality between collection and revenue. while this is quite intuitive, it is not practical for most investors and marketers as only limited people and companies have the opportunities to involve in such a movie. For our next stage exploration, we can remove related variables from modeling to see how other variables influence the revenue.

2. Keyword “dystopia” stands out as it apparently plays a relatively important role in revenue prediction and negatively impacts revenue identified by several models. While topics like this tend to stimulate thorough discussion and exploration of human society and are valuable from literary and artistic points of view, we should not expect such a movie to deliver high financial value. If an investor is only looking for maximizing return, a movie focused on such a topic should be avoided.

3. It is surprising to see the sentiments on “overview” have little impact on the revenue. It may be due to the fact that “overview” is relatively short and within such a short paragraph, it is hard to recognize the quality of the story. For the next phase, it may be helpful to run topic modeling and sentiment analysis on a much more details brief of the movies, if not go directly to analyze the whole scripts.

4. The movie industry is indeed a relatively complicated and unpredictable industry. The difficulty of revenue prediction of a movie may come from the fact that many factors in this domain are correlated to each other in some way. For example, while we found causal relationships between budget and A-list cast with revenue, we can't rule out the interaction between budget and A-list cast as big-budget production tends to be able to have more high-profile casts. Therefore, for the next phase, we need to investigate the interaction between different variables and then to understand their relationships with revenue.

Reference
1. F. Maxwell Harper and Joseph A. Konstan. 2015. The MovieLens Datasets: History and Context. ACM Transactions on Interactive Intelligent Systems (TiiS) 5, 4: 19:1–19:19. https://doi.org/10.1145/2827872

2. https://grouplens.org/datasets/movielens/

3. Murzintcev Nikita 2019. Select number of topics for LDA model.

4. Scott Lundberg 2018. Interpretable Machine Learning with XGBoost

