# league-of-legends-predictive-dragons
Predictive modeling using a dataset from 2022, competitive league matches about which team can secure more dragons. Originally done as a class project for DSC80 by Annie Pham.  
  
## Problem Identification  
**Question:** Can you predict whether a team took more dragons based on the side they start on and their stats at 15 minutes?  
  
This question is based on an exploratory data analysis I conducted on the same 2022 dataset, which can be found at [league-of-legends-neutral-obj-analysis](https://phamann000.github.io/league-of-legends-neutral-obj-analysis/). During my exploration, I found that teams which had taken more dragons, an important neutral objective that can be taken by either team, than their enemy seemed to have a tendency to win their game more often than teams which had taken less dragons than their enemies.  
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
- <mark>'more_drags':</mark>  Nominal Feature, True (or 1) when the team ends with more dragons than their opponent and False (or 0) otherwise.  

### Conclusion of the Baseline Model  
To predict my response variable using the features stated above, I decided to use a DecisionTreeClassifier() as my binary classifier, splitting my data up into a training set and testing set. These were my results...  
- *Training Accuracy:* 0.9980972150147034
- *Testing Accuracy:* 0.6075073516692614  
  
As can be seen above, my initial model has a much lower accuracy on it's testing dataset as opposed to it's training dataset. This means that my baseline model is terrible at generalizing, with a 30% difference in accuracy between the two datasets. With this in mind, I wanted to make some changes to make a model that is better at generalizing to unseen data.  
  
## Final Model
