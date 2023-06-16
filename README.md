### League of Legends Win Prediction
In this project, we used League of Legends data from 2022, titled the "League of Legends Competitive Matches", in order to predict whether or not an individual player won a game.

---

## Framing the Problem ##
For our prediction problem, we wanted to specifically take individual player data from the dataset, assuming we had all data after a game except the actual result of the game, and use that data to predict whether a player was on a winning or losing team. Therefore, for this problem, we used a **decision tree classifier** and used **binary classification**, with a 1 representing a win and 0 representing a loss. Our response variable was **result**, or the result of the game (win or loss). This was clearly the variable we wanted to predict. For our metrics, we used **accuracy**, knowing that the dataset will likely not be skewed in classes as every player has to either win or lose, and for every winning player there will therefore be an equivalent losing player. Because of this reason, we felt comfortable using accuracy as our primary metric.

---

## Baseline Model ##
In order to make a model specifically, for player prediction, we first had to clean our dataset by taking away any overall team data. After using only player data, we narrowed down the features we were using to these: 
- **league** (nominal): The specific league the player played in. Useful feature as being in a more competitive league could increase the possibility of a loss.
- **position** (nominal): The position/role of the player in the game. Different positions each have their own unique skills. 
- **champion** (nominal): The "character" in the game, each with their own skills/play type
- **dpm** (quant): Damage given per minute. Useful metric to see if a player delivered a lot of damage throughout the game, as more damage likely increases likelihood of winning the game.
- **damagetakeperminute** (quant): Damage the player received, also a good feature as more damage taken is usually bad for a player.
- **kills** (quant): Number of kills in a game for a player. More kills, greater likelihood of winning.
- **earnedgpm** (quant): Earned gold per minute, similar to kills and dpm, good for a player


For the nominal data, we used a OneHotEncoder in our pipeline to represent each unique league, position, and champion. 

Our model's performance:
- **Training Accuracy**: 1
- **Testing Accuracy**: 0.758

Clearly, this is not an ideal model. Our training accuracy is perfect, essentially showing that our decision tree right now is simply memorizing the data, with not necessarily a terrible performance for testing accuracy with 84.9%. Howevever, our model will likely generalize better if we decrease the complexity of the tree and engineer more features to make the current numerical features we have less noisy. 

---

## Final Model ##
For our quantitative features, specifically **gpm**, **dpm**, and **kills**, we decided to create 3 new features by quantiling these data. Given that the 3 columns are a bit noisy in terms of data, we quantiled in order to give the model features with considerably less outliers and more organized data. Furthermore, since we are not certain about the normality of these features, quantiling cleans up that data. We also decidied to binarize **damagetakenperminute**, as after exploring the data, we learned that players with more damage won less games on average. Therefore, binarizing the data with a threshold of 500 in order to make the feature simpler for the model and to better stress the impact of **damagetakenperminute**. We realized that this could have the negative effect of possibly erasing numerical data from the feature. However, given that the difference in damage taken is likely only siginficant between thresholds (for example, damage taken of 50 and 100 should probably not be seen as a "significant feature"), we decided to continue with the binarization of the damage taken feature. 

For the nominal features, we decided to keep the encoding the same. We also decided to stick with our decision tree model. 

For our hyperparameters, we focused on **criterion**, **max_depth**, and **min_samples_split**, ensuring that our model wasn't overcomplicated and was using the right criteria to split its nodes. Since we were using a basic decision tree, in order to find our hyperparamters we used the in-built sklearn GridSearchCV class, with a k-value of 5 for our k-folds validation. These were the best paramaters we found: 
- **criterion**: entropy
- **max_depth**: 13
- **min_samples_split**: 50

We incorporated these hyperparamters in our final decision tree model, and had these metrics as a result: 

- **Training Accuracy**: 0.842
- **Testing Accuracy**: 0.805

Clearly, our final model is an improvement on our baseline model. It is no longer simply memorizing the training set, as we have added our hyperparamters into the model. Also, the testing accuracy has gone up, showing that our newly egineered features also aided alongside the hyperparameters. The model has improved from the baseline by being less complicated with the tree depth and also making the features as a whole more relevant and consistent with our standardization. The training accuracy is still slightly higher than our testing accuracy, so there is likely some additional engineering/tinkering we can do with our model. However, overall, the model seems consistent with good performance. 

---

## Fairness Analysis ##
In order to ensure that our model was fair and not biased towards a specific group, we also conducted a fairness analysis on our model. Specifically, we were interested on whether or not the model was biased towards a specific league: LCK--the Korean League and one of the most competitive in the world. We wanted to treat this league as group X, and compare the model's performance on this league to all other leagues, our group Y.

We decided to stick with accuracy and make our metric **accuracy parity**, as accuracy would not be hindered by class imbalance. We then conducted a permuation test to determine if there was any significant difference in the model's accuracy for these two groups. 

**Ho**: There is **no** difference between the performance accuracy of the model on predicting the game's result for players in the LCK league and the performance accuracy for players not in that league.

**Ha**: The model is **more** accurate predicting for players in the LCK league than for players not from the league. 

The most useful test statistic for this test would therefore be the **signed** difference between model accuracy for the two groups. We chose a standard significance level of 0.05. 

After running the test, we got a p-value  of 0.45, far greater than our significance level and illustrating that there is **no** evidence that our model is more accurate for LCK players. Essentially, our model is likely not biased towards LCK and likekly dependable for predictions for that league. 
 




