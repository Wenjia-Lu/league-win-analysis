# League of Legends: Match at 20?
Final Project for EECS 398 Practical Data Science

## Introduction

In the high-stakes world of professional League of Legends, every second and every decision can tip the balance between victory and defeat. While matches officially conclude when a team’s Nexus is destroyed, competitive outcomes are often shaped much earlier through resource advantages and map control. Among the wealth of in-game data available, four metrics — creep score (CS), gold, map control — consistently stand out as key indicators of a team’s control over the game. Creep Score (CS) tracks how efficiently players are farming minions, a primary source of gold and experience that also reflects the player's mechanical expertise in last-hitting. Gold is used to purchase items, which help teams gain combat advantages. Map control measures a team’s control over the map, which considers factors from vision score (how much of the map is visible thanks to your team's wards?) to objective control (how many heralds or dragons has your team slain?).

This analysis focuses on determining whether it’s possible to predict the final outcome of a match by the 20-minute mark using these performance metrics. The 20-minute threshold is a pivotal moment in professional play, often marking the transition from early-game skirmishes to larger, decisive teamfights and map objectives. By investigating the relationship between early-game advantages and eventual match results, this project aims to better understand how reliably teams can convert leads into wins at the highest level of play.

Our dataset of 115152 rows comes from Oracle's Elixir, and contains data on individual player statistics and teamwide statistics in 9596 professional games. We will analyze our data using the teamwide statistics, a total of 19,192 rows of the dataset. The relevant columns we use are as follows:

`result`: outcome of a match; '0' indicates defeat, '1' indicates victory

`gameid`: unique identifier for each game.

`side`: either 'Red' or 'Blue', indicating where the team starts the game; we have later numerically encoded this to 0 (Blue) and 1 (Red)

`firstherald`: whether the team has secured the first herald; '1' indicates true, '0' indicates false

`heralds`: number of heralds the team has slain

`firstdragon`: whether the team has secured the first dragon

`firstblood`: whether the team was the first to slay an enemy player

`goldatX`: total gold earned for the team at the X-minute mark; indicative of strength in terms of economy, for X = 10, 15, 20

`golddiffatX`: difference in total gold earned between teams at the X-minute mark; indicative of strength in terms of economy, for X = 10, 15, 20

`xpatX`: total experience points earned for the team at the X-minute mark; indicative of strength in terms of levels, for X = 10, 15, 20

`xpdiffatX`: difference in experience points earned between teams at the X-minute mark; indicative of strength in terms of levels, for X = 10, 15, 20

`csatX`: total creep score (CS) for the team at the X-minute mark; contributes to overall gold and XP advantage, for X = 10, 15, 20

`csdiffatX`: difference in creep score (CS) between teams at the X-minute mark; contributes to overall gold and XP advantage, for X = 10, 15, 20

`killsatX`: number of kills by the team at the X-minute mark, for X = 10, 15, 20

`assistsatX`: number of assists by team at the X-minute mark, for X = 10, 15, 20

`deathsatX`: number of deaths by team at the X-minute mark, for X = 10, 15, 20

Here's an example:
GRAPH HEREREJLRJELRJSLDKFJSLDKFJSDLFKJSDL:FKJSD:LFKJSL:DFKJ


## Data Cleaning and Exploratory Data Analysis

### Data Cleaning
To prepare the dataset for analysis, the following data preprocessing steps were implemented:

#### Column Selection and Transformation:
> blah blah


After cleaning, we have:

GRAPH HEREREJLRJELRJSLDKFJSLDKFJSDLFKJSDL:FKJSD:LFKJSL:DFKJ


### Univariate Analysis
<iframe
  src="assets/uni_team_cs_per_minute.html"
  width="700"
  height="700"
  frameborder="0"
></iframe> The histogram displays the distribution of win rates for teams playing on the red side. While the frequency of win rates peaks around 50%, an interesting secondary peak emerges within the 35-45% win rate range, suggesting that starting on the red side may present a disadvantage.

<iframe
  src="assets/uni_team_gold_per_minute.html"
  width="700"
  height="500"
  frameborder="0"
></iframe> a

### Bivariate Analysis
<iframe
  src="assets/bi_team_gold_at_10.html"
  width="700"
  height="500"
  frameborder="0"
></iframe> a

<iframe
  src="assets/bi_team_vision_score.html"
  width="700"
  height="500"
  frameborder="0"
></iframe> etc

### Interesting Aggregates

GRAPH HEREREJLRJELRJSLDKFJSLDKFJSDLFKJSDL:FKJSD:LFKJSL:DFKJ

The table highlights...

### Imputation
No missing values were filled in. There were only two scenarios for missing values: (1) missing 20-minute data when the `datacompleteness` was partial, and (2) missing 20-minute data when games ended before 20 minutes. In the first scenario, attempting to fill in missing values using statistics from other games would create frameworks that oversimplify the unique and unpredictable dynamics of each game. In second scenario, imputation would not be effective as the data did not occur in the first place, i.e., it was missing not at random. As such, dropping these rows were more appropriate than imputation. 

## Framing a Prediction Problem
With evidence pointing to map side influencing game outcomes, we can explore a prediction problem: can we accurately predict a game’s result based on the starting side, alongside other in-game statistical metrics? Specifically, we aim to predict a game's outcome at the 20-minute mark, using the team's starting side and their accumulated in-game statistics up to that point.

The classifier being built performs binary classification, with the response variable being `outcome`, as that determines if a team won the match. Accuracy was chosen as the evaluation metric because it provides a clear measure of the model's performance and is suitable for this dataset, which has balanced classes with one win and one loss per game. Since there is no specific emphasis on minimizing false positives or false negatives, metrics such as AUROC or F1-score are not prioritized. 

### Baseline Model
The baseline model was trained using logistic regression using the following features: `side`, `killsdiffat20`,`deathsdiffat20`, `assistsdiffat20`. `side` is a nominal feature, for which one hot encoding was performed upon it. The other features were quantative, and were left as is. The dataset was split 70:30 and training and testing.

After fitting the model, the baseline model achieved an accuracy of **0.7356**.

In the next section, we will refine this model by expanding the feature set to include more strategic metrics, as well as applying appropriate scaling to the quantitative features.

### Final Model

These enhancements provide the model with a more comprehensive understanding of the data, leading to better predictive accuracy and a stronger alignment with the underlying mechanics of match outcomes compared to the baseline model. This is reflected in the final model's accuracy of **0.7** on the test set, demonstrating a clear improvement over the baseline model.