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


