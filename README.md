# dsci-100-final-project
## Introduction
  A research group in Computer Science at UBC, led by Frank Wood, is studying how people play video games. They have set up a Minecraft Server to record players' actions as they navigate through the world. After conducting the experiment and recording data from the Minecraft Server, Frank Wood and his research group created two comma-separated values (CSV) files; one that focuses on the players and the other that focuses on the sessions. The `player.csv` file includes a list of all unique players, including data about each player, while the `sessions.csv` file includes a list of individual play sessions by each player and records data about each session. Frank Wood had three broad questions of interest revolving around player characteristics, recruiting efforts, and session time window analysis. 

### Question
  Our group aims to answer Question 2: Which 'kinds' of players are most likely to contribute a large amount of data, allowing us to target them in recruiting efforts? To address this, we will analyze the relationship between `gender` (grouping variable) and `played_hours` and `Age` (explanatory variables) to identify engagement patterns and optimize recruitment strategies. The `players.csv` dataset is most relevant, as it includes key demographic and gameplay details. By focusing on age and played hours, we can determine which age groups engage the most and identify players likely to contribute substantial data. 

 ### Data Description
  The file contains 196 observations and 7 variables, including player experience (expertise level), subscription status (TRUE/FALSE), total hours played, player name, gender, and age. To ensure participant confidentiality, the `hashedEmail` variable is used for anonymization using hashing, but it does not provide meaningful insights, so it will be excluded from the analysis. In the data set, each row represents an individual player and their data. 
 
  The `players.csv` dataset satisfies the tidy data criteria: each row is a single observation, each column is a single variable, and each value is a single cell. This is important because it ensures that there is a single, consistent format that every function in the tidyverse recognizes, consequently, it can be manipulated, plotted, and analyzed using the same tools; overall, it is easier for humans to interpret. Beyond that, tidy data is more accessible to others and less error-prone. 

There are a variety of data types in the dataset: 
* `expertise` (factor) – Categorical variable with levels: _Pro, Veteran, Amateur, Regular, Beginner_. Represents the player's experience level. 
* `subscribe` (logical) – A logical variable indicating whether a player has an active subscription. Values are `TRUE` or `FALSE`. 
* `hashedEmail` (character) – An anonymous string used for privacy purposes. Not used in the analysis.
* `played_hours` (double) – Numeric variable with decimal values (e.g., 12.5 hours). Measures the total number of hours a player has played.
* `name` (character) – String representing the player’s name, used for identification purposes. 
* `gender` (factor) – Categorical variable representing gender identity. Possible labels include: _Male, Female, Prefer not to say, Agender, Non-binary, Two-spirited._
* `Age` (integer) – Whole number values indicating player age in years (e.g., 21, 35). 

While the `players.csv` dataset provides valuable insight into player demographics and engagement, for our analysis, it is important to consider the limitations, particularly in the context of our analysis. The variable `played_hours` does not account for session quality or context—a player may be logged in but not actively engaged, leading to overestimation of true gameplay involvement. This can heavily impact our analysis since our analysis focuses on three variables: `played_hours`, `age`, and `gender`. As a result, interpretations based on `played_hours` must be made with caution, and their limitations must be recognized. 
