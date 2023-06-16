### League of Legends Win Prediction
In this project, we used League of Legends data from 2022, titled the "League of Legends Competitive Matches", in order to predict whether or not an individual player won a game.

---

## Framing the Problem ##
For our prediction problem, we wanted to specifically take individual player data from the dataset, assuming we had all data after a game except the actual result of the game, and use that data to predict whether a player was on a winning or losing team. Therefore, for this problem, we used a **decision tree classifier** and used **binary classification**, with a 1 representing a win and 0 representing a loss. Our response variable was **result**, or the result of the game (win or loss). This was clearly the variable we wanted to predict. For our metrics, we used **accuracy**, knowing that the dataset will likely not be skewed in classes as every player has to either win or lose, and for every winning player there will therefore be an equivalent losing player. Because of this reason, we felt comfortable using accuracy as our primary metric.

---

## Baseline Model ##
In order to make a model specifically, for player prediction, we first had to clean our dataset by taking away any overall team data (we decide to keep team_kills so that our decision tree could associate overall team kills with a player's kills, possibly using team contribution). After using only player data, we narrowed down the features we were using to these: 
- **league** (nominal): The specific league the player played in. Useful feature as being in a more competitive league could increase the possibility of a loss.
- **position** (nominal): The position/role of the player in the game. Different positions each have their own unique skills. 
- **champion** (nominal): The "character" in the game, each with their own skills/play type
- **dpm** (quant): Damage given per minute. Useful metric to see if a player delivered a lot of damage throughout the game, as more damage likely increases likelihood of winning the game.
- **damagetakeperminute** (quant): Damage the player received, also a good feature as more damage taken is usually bad for a player.
- **kills** (quant): Number of kills in a game for a player. More kills, greater likelihood of winning.
- **earnedgpm** (quant): Earned gold per minute, similar to kills and dpm, good for a player
- **teamkills** (quant): overall kills from team, added to ????? tf we do with this

For the nominal data, we used a OneHotEncoder in our pipeline to represent each unique league, position, and champion. 

Our model's performance:
- **Training Accuracy**: 1.0
- **Testing Accuracy**: 0.849

Clearly, this is not an ideal model. Our training accuracy is perfect, essentially showing that our decision tree right now is simply memorizing the data, with not necessarily a terrible performance for testing accuracy with 84.9%. Howevever, our model will likely generalize better if we decrease the complexity of the tree and engineer more features to make the current numerical features we have less noisy. 

---

## Final Model ##
Given that our nominal features were already encoded, we decided to see if we could transform any of our quantitative data into more meaningful features. Given our knowledge of League of Legends, we realized that our current features, **earned gpm**, **dpm**, and **damagetakenperminute**, would likely vary considerably on the way a certain player is playing the game. For example, a player playing with a specific champion would have less dpm taken if they are more on defense than another champion. A similar relationship exists for the position the player is playing at. Because of this, we decidided to standardize these columns by group, specifically standardizing **earned gpm** and **dpm** within each **position**, and **damagetakenperminute** within champion. These features are useful as they are essentially making the data more relative to the way the player is playing a game. For example, our baseline model would seek a lot of damage taken and possibly penalize that player regardless of the character they are playing as. Now, if a character/champion is more aggressive but in general leads to more wins, damage taken is now standardized relative to the champion, making our model far more consistent and balanced. 

For the nominal features, we decided to keep the encoding the same. We also decided to stick with our decision tree model. 

For our hyperparameters, we focused on **criterion**, **max_depth**, and **min_samples_split**, ensuring that our model wasn't overcomplicated and was using the right criteria to split its nodes. Since we were using a basic decision tree, in order to find our hyperparamters we used the in-built sklearn GridSearchCV class, with a k-value of 5 for our k-folds validation. These were the best paramaters we found: 
- **criterion**: gini
- **max_depth**: 13
- **min_samples_split**: 50

We incorporated these hyperparamters in our final decision tree model, and had these metrics as a result: 

- **Training Accuracy**: 0.904
- **Testing Accuracy**: 0.881

Clearly, our final model is an improvement on our baseline model. It is no longer simply memorizing the training set, as we have added our hyperparamters into the model. Also, the testing accuracy has gone up, showing that our newly egineered features also aided alongside the hyperparameters. The model has improved from the baseline by being less complicated with the tree depth and also making the features as a whole more relevant and consistent with our standardization. The training accuracy is still slightly higher than our testing accuracy, so there is likely some additional engineering/tinkering we can do with our model. However, overall, the model seems consistent with good performance. 

## Fairness Analysis ##
In order to ensure that our model was fair and not biased towards a specific group, we also conducted a fairness analysis on our model. Specifically, we were interested on whether or not the model was biased towards a specific league: LCK--the Korean League and one of the most competitive in the world. We wanted to treat this league as group X, and compare the model's performance on this league to all other leagues, our group Y.

We decided to stick with accuracy and make our metric **accuracy parity**, as accuracy would not be hindered by class imbalance. We then conducted a permuation test to determine if there was any significant difference in the model's accuracy for these two groups. 

**Ho**: There is **no** difference between the performance accuracy of the model on predicting the game's result for players in the LCK league and the performance accuracy for players not in that league.

**Ha**: The model is **more** accurate predicting for players in the LCK league than for players not from the league. 

The most useful test statistic for this test would therefore be the **signed** difference between model accuracy for the two groups. We chose a standard significance level of 0.05. 

After running the test, we got a p-value  of 0.45, far greater than our significance level and illustrating that there is **no** evidence that our model is more accurate for LCK players. Essentially, our model is likely not biased towards LCK and likekly dependable for predictions for that league. 
 




