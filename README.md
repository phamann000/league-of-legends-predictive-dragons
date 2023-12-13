# league-of-legends-predictive-dragons
Predictive modeling using a dataset from 2022, competitive league matches about which team can secure more dragons. Originally done as a class project for DSC80 by Annie Pham.  
  
## Problem Identification  
**Question:** Can you predict whether a team took more dragons based on the side they start on and their stats at 15 minutes?  
  
This question is based on an exploratory data analysis I conducted on the same 2022 dataset, which can be found at [league-of-legends-neutral-obj-analysis](https://phamann000.github.io/league-of-legends-neutral-obj-analysis/).  
During my exploration, I found that teams which had taken more dragons, an important neutral objective that can be taken by either team, than their enemy seemed to have a tendency to win their game more often than teams which had taken less dragons than their enemies.  
With this in mind, I wanted to see if I could predict whether a team would end with more dragons or not. The first thing I did was clean the data. One of the biggest parts of this included dropping the rows that referred to individual players, while keeping rows that were the team summary, which combined all players on one side, for that game. I also kept a comparatively small number of columns (or features) compared to the entirety of the original dataframe.  
The features I chose to try to predict this response variable are the side the team starts on and the stats of the team at 15 minutes, like <mark>'golddiffat15'</mark> or <mark>'csdiffat15'</mark>. I chose these features because they can be recorded as the game is still in progress before we know whether or not a team will end with more dragons or not. Because of this, these features would make good predictors. More information about the features I used and how I used them can be found in more detail below in the **Baseline Model** and **Final Model** section respectively.  
In working with these features I chose to use **Binary Classifiers** with the column <mark>'more_drags'</mark> as a response variable, either True if the team ended with more dragons taken and False otherwise. 
To measure how "good" my model is at predicting, I will be using <mark>accuracy</mark>. I chose this because of the binary nature of the response model, and whether a team ends with more dragons or not depends on their opponent, leading to the makeup of True:False values being reasonably around 50/50. Because of this even split, I believe that accuracy makes a good measure of prediction as opposed to precision, recall, or F-1 score which may be better under circumstances where either False Postives or False Negatives is more important.  
  
## Baseline Model  
In my Baseline Model, I wanted to use the following features:  
- <mark>'side':</mark> Nominal Feature, refers to which side of the map a team is playing on for that game. Either 'blue' or 'red'.  
- <mark>'csdiffat15':</mark> Quantitative Feature, cs stands for creep score which is a measure of how many minions and small monsters are slain for gold. This feature speficially refers to how ahead or behind the team is in creep score at 15 minutes.  
- <mark>'golddiffat15':</mark> Quantitative Feature, gold is an important resource that allows players to buy items that will boost how strong they are, allowing them to do things like do more damage or take more damage. This feature refers to the difference in gold between the two teams.  
  
These features were chosen because they would measure how ahead or behind in gold each team is with 'golddiffat15' being a direct measure and 'csdiffat15' being a more indirect measure. Additionally, 'csdiffat15' can also be indicator of how much pressure one team has over the other because in order for a player to increase their cs, they will often be in a position where the enemy can attempt to hurt or otherwise drive them away. Overall these two features can show how strong a team is, and I predicted that since dragons are a larger objective that is more difficult to take, stronger teams would have an advantage in taking them. For the baseline model, I chose not to transform these two features and just left them as is in the model.  
I also chose to use the <mark>'side'</mark> column as a feature by OneHotEncoding it. I chose to include this feature because one side, the 'red' side actually has easier access to the location of the dragon as there are more direct routes to access it while the 'blue' side is blocked off by the back wall of where the dragon is located.  

Additionally my response variable is:  
- <mark>'more_drags':</mark>  Nominal Variable, True (or 1) when the team ends with more dragons than their opponent and False (or 0) otherwise.  

### Conclusion of the Baseline Model  
To predict my response variable using the features stated above, I decided to use a DecisionTreeClassifier() as my binary classifier, splitting my data up into a training set and testing set. These were my results...  
- *Training Accuracy:* 0.9980972150147034
- *Testing Accuracy:* 0.6075073516692614  
  
As can be seen above, my initial model has a much lower accuracy on it's testing dataset as opposed to it's training dataset. This means that my baseline model is terrible at generalizing, with a 30% difference in accuracy between the two datasets. With this in mind, I wanted to make some changes to make a model that is better at generalizing to unseen data.  
  
## Final Model
The first thing I wanted to do was choose the combination of features and the modeling algorithm I wanted to use. I did this by creating 10 different models, 5 of which used a RandomForestClassifier and 5 of which used a DecisionTreeClassifier. Each of the 5 models within each modeling algorthim also had a different combination of features which are as follows...  
  
- 'all_stats': uses all of the features left in the dataframe which are listed as follows ['goldat15', 'xpat15', 'csat15', 'golddiffat15', 'xpdiffat15', 'csdiffat15', 'killsat15','assistsat15', 'deathsat15', 'side']
- 'kill_par': uses the features having to do with kill participation, ['killsat15','assistsat15', 'deathsat15', 'side']
- 'diff_stats': uses the features that calculate the difference between the two teams, ['golddiffat15','xpdiffat15', 'csdiffat15', 'side']
- '15_stats': uses all of the features except the ones included in 'diff_stats', ['goldat15', 'xpat15', 'csat15', 'killsat15', 'assistsat15', 'deathsat15', 'side']
- 'diff_stats + kill_par': uses the features within 'kill_par' and 'diff_stats', ['golddiffat15', 'xpdiffat15', 'csdiffat15', 'killsat15', 'assistsat15', 'deathsat15', 'side']

  
I performed 5-fold cross-validation in order to see which combination of models and features would have the highest accuracy on average, these are the results.  
  
|   all_stats, RandomForest |   all_stats, DecisionTree |   kill_par, RandomForest |   kill_par, DecisionTree |   diff_stats, RandomForest |   diff_stats, DecisionTree |   15_stats, RandomForest |   15_stats, DecisionTree |   diff_stats + kill_par, RandomForest |   diff_stats + kill_par, DecisionTree |
|--------------------------:|--------------------------:|-------------------------:|-------------------------:|---------------------------:|---------------------------:|-------------------------:|-------------------------:|--------------------------------------:|--------------------------------------:|
|                  0.70712  |                  0.629865 |                 0.66561  |                 0.652061 |                   0.690689 |                   0.613433 |                 0.690401 |                 0.619775 |                              0.701355 |                              0.611415 |
|                  0.696743 |                  0.627558 |                 0.663304 |                 0.658115 |                   0.671087 |                   0.613433 |                 0.692707 |                 0.629576 |                              0.69386  |                              0.624099 |
|                  0.707408 |                  0.629865 |                 0.6806   |                 0.669357 |                   0.6806   |                   0.616028 |                 0.695878 |                 0.623234 |                              0.693283 |                              0.608533 |
|                  0.711938 |                  0.646194 |                 0.676471 |                 0.668108 |                   0.687716 |                   0.61015  |                 0.711361 |                 0.6312   |                              0.709631 |                              0.625721 |
|                  0.713956 |                  0.63812  |                 0.665513 |                 0.655998 |                   0.690888 |                   0.618224 |                 0.705017 |                 0.628893 |                              0.704729 |                              0.617359 |
  
  
From this, I found that using all_stats, or all of the features in combination with the RandomForestClassifier would give me the best results.  

### The Features
Here are my beliefs on why each feature ended up increasing the accuracy of the modeling algorithm...
- <mark>'goldat15':</mark> *Quantitative feature*, more gold leads to the ability to purchase more items. Items will increase the stats of the players, making them do more damage, take more damage, or overall increase their ability to help their team. Because of this, 'goldat15' is an overall measure of how strong the players on a team are. With stronger teams being more willing to take Dragons. 
- <mark>'golddiffat15':</mark> *Quantitative feature*, the gold difference a team has over their opponent is a measure of how strong a team is compared to their opponent, not just overall. 
- <mark>'xpat15':</mark> *Quantitative feature*, experience, or xp for short, is another measure of a player's strength. Players with more xp will be higher level and being higher level leads to higher base stats, like ones that will allow the player to take more damage, as well as let players increase the strength of their unique abilities. These unique abilities are often the core of how much damage a player can output or how hard a player is to kill, so teams with more xp will have stronger abilities and therefore be stronger overall.
- <mark>'xpdiffat15':</mark> *Quantitative feature*, the xp difference a team has over their opponent measures how strong their abilties and base stats are in comparison to the team they are fighting. Teams that are stronger than their opponent may want to take more Dragons. 
- <mark>'csat15':</mark> *Quantitative feature*, As described under **Baseline Model** cs stands for 'creep score' and it measures the amount of minions or small monsters a player has killed. Killing these minions and small monsters increases a players experience and gold, while also measuring the pressure a player may have on the map since in order to increase one's creep score, a player must put themselves into a potentially dangerous position. Therefore 'csat15' is a good measure of how strong a team is with higher creep score often leading to higher gold and experience, as described above, while also measuring how strong a team might feel. 
- <mark>'csdiffat15':</mark> *Quantitative feature*, for the same reasons above, except instead of just being an overall measure across all teams, this marks the difference between the current team and the enemy team.
- <mark>'killsat15':</mark> *Quantitative feature*, measures the amount of times a player on the team gets a kill on their opponent. Kills increase experience, gold, and measures how aggressive a team is. More aggressive teams may have a tendency to go for bigger objectives like Dragons which take a longer time to take as Dragons are tankier and do more damage compared to the smaller monsters around the map. Dragons also have a central position on the map while giving permanent buffs to your entire team, meaning the opponent will try to stop you from taking it or try to take it for themselves. Because of this, kills are a good measure of how willing a team is to take dragons with teams with higher kills being overall stronger and more willing to fight over this game changing objective.
- <mark>'assistsat15':</mark> *Quantitative feature*, Assists are gotten when a player helps their teammate get a kill. Because of this, 'assistsat15' can be a measure of how much a team likes fighting together. With Dragons being such a big objective, players often have to work together with the members of their team in order to take it, so teams with more assists may have a tendency to take more dragons. 
- <mark>'deathsat15':</mark> *Quantitative feature*, When a player dies a death timer starts which measures how long a player has until they start playing again. This death timer grows longer the later it is into the game. When a player is dead, they lose out on the ability to gain gold or experience which, as stated above, helps increase a player's strength. Because of this, high deaths can be indicator of how behind a team is and teams that are behind may not feel as confident in trying to take or contest dragons. 
- <mark>'side':</mark> *Nominal feature*, As stated under **Baseline Model** the 'red' side of the map has easier access to the dragon due to the positioning of the walls and therefore may be more likely to take dragons.  
  
Along with OneHotEncoding 'side', I also chose to binarize 'assistsat15' with a threshold of 20 and 'xpdiffat15' at a threshold of 5000. I chose to binarize 'assistsat15' because, as stated before, Dragons are a neutral objective that often takes entire portions of the team working together to take. Since 'assistsat15' is a measure of how much a team is working together, I thought that teams with high assists (20+) will have a greater tendency to take dragons, but otherwise the specific number of assists didn't matter. I also chose to binarize 'xpdiffat15' at a threshold of 5000 for a similar reason. Teams that are ahead, like as xpdiffat15 measures, will try to push their advantage by doing many things, including taking neutral objectives like dragons. Because of this, I binarized 'xpdiffat15' to represent only teams that are ahead.  
### The Modeling Algorithm
As seen in the dataframe, RandomForestClassifier had a tendency to have better accuracy on average. This is because DecisionTreeClassifiers have a tendency to overfit while the RandomForestClassifier, which uses many DecisionTreeClassifiers and averages them, is better at generalizng.  
However RandomForestClassifier can still have tendencies to overfit, even if they tend to be better than DecisionTreeClassifiers. To limit this overfitting, I created models that found the accuracy of different combinations of 'max_depth' and 'n_estimators' to see which one would perform best on unseen data on average. Here is the head of the resulting dataframe...  
  
| depth, estimator   |   train_score |   test_score |
|:-------------------|--------------:|-------------:|
| (2, 2)             |      0.703684 |     0.707317 |
| (2, 5)             |      0.707259 |     0.713198 |
| (2, 10)            |      0.707951 |     0.715966 |
| (2, 20)            |      0.709681 |     0.714409 |
| (2, 50)            |      0.707605 |     0.714755 |  
  
And here is the best combination of depth and estimator, found by the max value of test_score.  
  
| depth, estimator   |   train_score |   test_score |
|:-------------------|--------------:|-------------:|
| (10, 20)           |      0.783198 |     0.726691 |

With all of this combined I now have my final model.  
### The Results  
When running the baseline model on unseen data, I got an accuracy of 0.6075073516692614. However with my final model, my accuracy on unseen data resulted in an accuracy of 0.724096177131984 which I think is a huge improvement. It shows that my final model ended up being much better at generalzing to unseen data, which is exactly what I wanted.  
With this I was happy with how my final model turned out, and ran the entire 2022 competitive matches dataset to get the following confusion matrix.  
![Confusion Matrix of Predicting Which Team has More Dragons](/assets/league_conf_mat.png)  
As is illustrated above, my final model ends up being pretty good at predicting which team has more_drags.