# Business-Intelligence-Project
Anime Recommendation System

by Group 1
2019/06/09
 
## 0.	 Executive summary

In this report a design of recommendation system will be suggested. The procedures for data-mining studies are strictly followed by starting with data-selection, data-preprocessing, data-mining and evaluation. Varieties of metrics measuring association, quality and improvement would be introduced for better recommendation which will be introduced later. Finally contains, limitations, and managerial decisions will be included.

## 1.	  Background and motivation
Anime has long been essential entertainment for teens or even the middle-aged and occupied great proportion of people’s leisure time. In this age of information explosion, audience of amines requires recommendations of anime that they like to save time for screening amongst the enormously number of alternatives. The goal of this project is to design a recommendation system that analyze both the users’ and videos’ historical data and find personalized recommendation for each individual user. 
 
The system will recommend videos that are highly related to what users are interested in. Also, the system will find the kind of videos that users are potentially interested in but they have never tried. In other words, the system will recommend two types of videos to users: one of them is unwatched and related videos, this type of videos is selected based on what users like watching and find those unwatched and of the same kind. Another type of videos is selected on the purpose of helping users explore those unwatched and fresh videos to them. The reason of recommending the second type of videos is to avoid the homogeneity and increase the diversity of the recommended videos. In addition, both types of recommended videos are of high quality measured by a quality score which will be defined later.

## 2.	Data-mining study and System design

### 2.1 data description
The input data is the only source where we can dig out the useful information about users’ habits and the videos. The data set applied was from Kaggle, the animation recommendation database (Kaggle.com, 2017). On hand, we have two data files: one is animation file with the information about the ID, name, genre and rating for each animation. Another one is user file with the information about the users’ ID and their rating for every animation they have watched.

Here are descriptions for two original data set.

|Field|	Description|
|------|:-------:|
|anime_id|	Myanimelist.net's unique id identifying an anime|
|name	|Full name of anime|
|genre	|Comma separated list of genres for this anime|
|type	|Movie, TV, OVA, etc|
|episodes|	How many episodes in this show. (1 if movie)|
|Rating	|Average rating out of 10 for this anime|
|member|	Number of community members that are in this anime's "group"|

|Field	|Description  |   
|---|:-----:|
|user_id	|Non identifiable randomly generated user id|
|anime_id	|The anime that this user has rated|
|rating|	Rating out of 10 this user has assigned (-1 if the user watched it but didn't assign a rating)|

### 2.2	Data preprocessing

#### 2.2.1 Data cleaning
Standardization or normalization method are not considered in our project as only two numerical variables are included in datasets, rating and episodes. No data transformation is conducted to the variable of rating because the actual value of rating has important effect on our further model building. Thus, there are three steps to clean the dataset. First, find the unique anime id of anime.csv in rating.csv and get new dataset named animes. There are 32 missing values of genre variable, 1 missing value of type variable and 3 missing values of rating variable. We drop these missing values from animes. Second, select known subset from animes and drop the unknown subset. We replace those unknown values as missing values and drop them. Third, the origin type of episodes variable is object and need to change to integer or numeric type. We use “astype” command. 

The following is description of the final dataset we get.  

|anime_id	|episodes	|rating|	members|
|-------|:----:|:-----|:--------:|
|count	|11158.000000	|11158.000000|	11158.000000	|1.115800e+04|
|mean|	12315.026976|	12.552160	|6.519313	|1.943773e+04|
|std	|10475.081940	|46.688997|	0.987973|	5.680266e+04|
|min	|1.000000|	1.000000|	1.670000	|2.900000e+01|
|25%|	3072.250000	|1.000000	|5.950000	|3.132500e+02|
|50%	|8959.500000	|2.000000	|6.600000|	1.826000e+03|
|75%	|21058.000000	|12.000000	|7.200000	|1.089750e+04|
|max	|34519.000000	|1818.000000|	9.500000|	1.013917e+06|

 
#### 2.2.2 Data visualization
To further analyze the dataset, we visualize it by matplotlib and seaborn:
a.	Plot type, members, episodes variables on rating variables by pair, we find some interesting patterns: 
 
There tends to be positive relationship between members and rating, and rating tends to follow normal distribution.
b.	we joint the members and rating together and find that most animes have less than 200 thousand members watched and those who have more than 200 thousand members watched tend to have higher ratings.


c.	Then, we visualize the linear line by each type. TV type has largest coefficients and more members of TV audience have higher rating, but when number of members too large, its impact on rating is getting weaker. 
 
d. the relationship between episodes and rating is studied:
 
It is concluded that rating is not influenced by the number of episodes:
e.	The distribution plot shows that the distribution of ratings is almost normal and centered on 7. 
 

f.	As type is categorical variable, we count it and see the distribution of different types. It is clearly that the dataset mainly consists of TV, OVA and Movie. 
 

g.	 Per the box plot, TV type is of the highest average rating of 7. Movie and Special type are of almost 6.5 average rating. However, movie type has largest scope of rating, from 3 to 10. 
 
h.	By counting, the five most popular genres are found: Comedy, Action, Adventure, Fantasy and Sci-Fi. The heatmap of Genre was attached to study the correlations  
 
 


 

### 2.3 the model
After preparation and preprocessing, a recommendation was built per the following logic:
 
The details for each part will be explained: 
![](https://github.com/jincai-zheng/Business-Intelligence-Project/blob/master/1.png)

### 2.4 Q score
Based on our goal of recommendation, the recommended videos are supposed to be of high quality. Q score is regarded as a more objective measurement to filter out the videos below the threshold. Q score is defined as the composition of three parts:
1.  View counts score: the number of views is counted for each video, and if the video’s view counts is larger than the average view counts of all videos, then the view counts score for this video is 1, otherwise 0.
2.  Rate percent score: not all the viewed video has been rated, so the rate percent for each video is the number of rates divided by the number of views, and if the video’s rate percent is larger than the average rate percent of all videos, then the rate percent score for this video is 1, otherwise 0.
3.  Average rating score: the average rating for each video is calculated as the average value of all the ratings it receives. If the average rating of this video is larger than the mean value of all videos, then the average rating score is 1, otherwise 0.
 
Thus, Q score is the summation of the three binary scores, ranging from 0 to 3. Then the Q score attribute will be added into the user data, in the new data frame, we can have a view on user ID and its watched videos and the corresponding Q score. Next, a threshold will be set to filter out the low-quality videos by deleting the observations with Q score lower than the threshold. What is left in the data frame is each user and their interested videos with high quality.
 

### 2.5 Related videos
#### 2.5.1 R score
From the result above, we can define seed videos for each user, a bag of seed videos for a user is all the watched high-quality videos that he/she is interested in. Each user may have a bag of different seed videos.
 

Based on the seed videos of each user, we can find a few related videos for every seed video. R score is defined to indicate the relation between two videos. R score is calculated as total simultaneous occurrence counts of two videos divided by the product of two videos’ occurrence counts separately (The function is same as Lift function in association rule) (James, Benjamin, Junning, Palash, & Taylor, 2010). 
 
Then there is R score for every pair of videos.
  

#### 2.5.2 Layers of related videos
For the purpose of recommending some fresh videos, the second type, to users, we need to define two layers of related videos.
 
The first layer can be interpreted as a direct relation and the second layer as indirect relation. For a seed video of a user, it will have a R score with every other videos. Since R score measures the relation between two videos by finding cooccurrence, the higher the R score is, the stronger the relation is. Then, a threshold for R score can be set to select those mostly related videos with high R score as a bag. In this bag, filter out those videos that appear in the seed bag. Then, this bag of related videos to a seed video is noted as first layer related videos. The second layer can be seen as mostly and indirectly related to the prior seed video. To find out the second layer, we select a video from the first layer, and find its bag of mostly related videos by the same threshold. In this new bag of videos, filter out those videos that appear in first layer or in seed bag. Then, this bag of videos is in second layer, and for each video in the first layer bag, there is a corresponding second layer bag.
 
The whole layer logic grows like a tree: starting from seed bag of each user, it has branches connected to related bags of videos in the first layer, with one seed connected to one first-layer bag. Then, each first-layer bag has branches connected to related bags of videos in the second layer, with one first-layer video connected to one second-layer bag.
  

 
#### 2.6 Candidates for recommendation
All the related videos in the first layer and second layer are regarded as the candidates for recommendation. The next step is to select the truly recommended videos from the candidate pool.  	

The candidate videos for a user may come from layer 1 or layer 2, they have different paths connected to each seed video, thus one candidate may connect to several seed videos. Here we define NSR score for each candidate. NSR, which stands for “Number of Seeds Related”, means how many different seed videos connect to this candidate. This measurement is applied to rank the candidates into a list, the higher the NSR score, the better chance will it be for the candidate to be chosen as recommended video.
 

 
## 3.	Evaluation
Due to limitation for future data-collection, only off-line experiment is applicable for our evaluation (Guy & Asela, 2011). The idea of Hit was applied for evaluation with which the performance of recommendation was measured. Detailed procedures are explained as following:

### 3.1	Train-set split
Due to the absences of time series data, the seed bag videos, which are the videos being watched by a user, is separated into “train bag” and “test bag”, the splitting ratio is 50%:


The “train bag” will be used to for generating recommendations will the “test bag” will be the “future video the user will watch”. 

The recommendation generated from “train bag” will be denoted as “recom bag”. Another set of videos, which is with the same size as that of the recom bag and randomly picked from the total video list is denoted as “random bag”. Noticed that no seed video for the user will be appear in the “random bag” and videos with view counts below 3 will not be included as newly introduced video with low view counts will have low R score and would be irrelevant to our evaluation.
 

### 3.2	metrics for evaluation
The idea of HIT from the LIFT model is applied for measuring performance improvement. For illustration purpose, an example was demonstrated: 

2 video, 24 and 241 are find both in the “recom bag” and “test bag”. Thus, the hit for this recommendation set is 2. Similarly, the HIT for the “random bag” is 1 as the “test bag” and “random bag” share one identical video, video 2878. The LIFT is calculated as: (HITrecom - HITrandom)/ HITrandom * 100%= 200%
 


For special cases when recom Hit is 0, the LIFT is defined as( -1 * HITrandom) while when random Hit is 0, the LIFT is defined as (HITrecom).

 
  


As it is believed that the two special case might appear rapidly and generating LIFT with extremely large/ small value. The purpose of our study was not the measure the extent to which the recommendation system helps but if the recommendation system helps. Thus, keeping these extreme improvement/ deteriorations in performance as a value close to normal improvement would help clarifying if our design bring improvement or not.

Noticed that for the recommendation video that does not hit the test bag, no evidence support that the user will not watch these videos forever and they are just “not yet watched” thus we can not define it as false negative prediction. Thus, the HIT, which are the videos that successfully being predicted are the only concern for the study. 
### 3.3	Findings
 


By taking the recommendation system and evaluation metrics, 100 users and 10000 videos were studied from the data-set, the above illustrated output was generated. The R-score was set as 0.1 as the threshold for affirming an association. The “LIFT” are the average LIFT for the LIFT of recommendation bag for each user randomly picked. “Layer” is the maximum value of layer allowed. A layer of 2 means the video indirectly recommended was allowed. That is, videos that are not directly associated with seed videos are also added to act as diversity or “surprise” for user. 

Overall, all the average LIFT are positive, ranging from 0.1 to 2.2 indicating that the recommendation system is performing better than random group and do create value for the audiences. Moreover, when “surprises”, or indirectly related videos are included, the LIFT increases by almost 100%.  
### 3.4	 Limitation and alternatives
First, a lack of time-series data and future data make online-experiment impossible, with which evaluation approach like A/B test can be implemented (Guy & Asela, 2011). 
Second, seasonal effect and timing effect might be neglect. Users might prefer different animations in different timeframe. A lack of timestamps make use impossible to control the effect of time.
Third, an alternative approach can be introduced for evaluating performances but was not tried due to lack of enough computing power: 

A metrics, average N score (ANS), is introduced measuring the strength of association between the recommendation video and the seed video. For Recommendation R1, can be recommended via the following route:

| Seed   |   To     |   ANS   |
|----    |:--------:|--------:|
|S1- R2-R3-R1   	  |  -->| layer / N score = 3|
|S2 – R2 -R1	      | --> |layer / N score = 2|
|S3 – R2- R5-R6-R1 |-->  |layer / N score = 4|

An N score of 3 indicating that it take 3 steps for R1 to relate to the seed video. The average N score of R1 would be calculated: (3+2+4)/ 3 = 3. Noticed that the lower the N score, the more associated the recommendation videos are with the seed videos(the video watched and liked by the a user).
The ANS of all the recommendation video for a user can be calculated, averaged, and denoted as ANS-Recom. A random set of videos with same size as the recommendation can be created and the corresponding ANS for each random videos can be calculated. Taking the average all the ANS value of the random set, we have ANS-Random. If ANS-Recom is smaller than ANS-random, we affirmed that the recommendation is of videos more associated with the seed bag and, thus, are improving the performance. 

### 3.5	managerial decision and application
Base on the result, for the manager of the animation video site or applications, association rules would help for making recommendations. Though limiting the recommendations that are only directly related to the user history might limit the recommendation within a certain set of videos. Though the video that are indirectly associated but still might be interested by the users might be omitted. These videos might be surprise to users if being included.
Normally, users find a video by searching or clicking the recommendation list. For the case of searching, video that a more related should be suggest (Gary, D, & H, 2013). While for the other case when a user random click the video in recommendation bar, adding the indirectly related videos might bring user better experiences. Users might find the videos that are slightly different from the result of direct searching while still related to what the users are interested in. 
 
## 4.	Conclusion
Our study applied the data-mining study procedures, designed and implemented a recommendation system like the recommendation engineer applied in Youtube. The performance of the system was better than the random group(average LIFT above 0) and it is believed that this model would help users find videos they like. 

## 5. References
Gary, H., D, S., & H, M. B. (2013). Ad click prediction: a view from the trenches. Ad click prediction: a view from the trenches. Proceedings of the 19th ACM SIGKDD international conference on Knowledge discovery and data mining. ACM. 
Guy, S., & Asela, G. (2011). Evaluating recommendation systems. Recommender Systems Handbook, 257-297.
James, D., Benjamin, L., Junning, L., Palash, N., & Taylor, V. V. (2010). The YouTube Video Recommendation System. ACM Conference on Recommender Systems, (pp. 26-30). Barcelona, Spain.
Kaggle.com. (2017). Anime Recommendations Database. 


## 6. Appendix:
Coding: https://colab.research.google.com/drive/1pbUN4yNqzkxAgIwZytP6auw96f21RVdd
Dataset: https://www.kaggle.com/CooperUnion/anime-recommendations-database#anime.csv

