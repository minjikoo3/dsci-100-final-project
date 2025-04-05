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

### Methods
This analysis was conducted to understand the relationships between gender, played hours, and age among players on a Minecraft server. The goal was to clean and prepare the data for predictive modeling while also exploring patterns in player engagement. Below, each step of the analytical process is described in detail, from data loading to cross-validation setup.

### Loading Data
The first step in any data analysis is to load the dataset. In this case, we worked with a file named `players.csv`, which contains information on each player's gender, age, and the total number of hours they’ve played. We used the `read_csv()` function from the `readr` package to read the data into R as a data frame. This forms the foundational dataset for our entire analysis.

library(tidyverse)
library(repr)
library(tidymodels)
options(repr.matrix.max.rows = 5)
# source('cleanup.R')

player_url <- "https://raw.githubusercontent.com/minjikoo3/DSCI_100_Project_Planning/refs/heads/main/players.csv"
players <- read_csv(player_url)
head(players)

###  Data Cleaning and Preprocessing
Raw data often includes unnecessary or inconsistent information, so cleaning is essential. In this step, we:

##### Selected relevant columns
(`gender`, `played_hours`, `Age`) that are important for our analysis.

##### Removed missing values 
By using `drop_na()` to ensure we don’t introduce bias or errors in our models.

##### Converted the gender column into a categorical factor
Since gender is a non-numeric variable and needs to be treated differently in modeling and plotting.
This step ensures that the dataset is tidy, consistent, and ready for analysis.

players_tidy <- players |>
select(gender, played_hours, Age) |>
drop_na() |> 
mutate(gender = as.factor(gender))

### Data Standardization
To make comparisons and model training more effective, we standardized the numerical variables — `played_hours` and `Age`. Standardization involves transforming the data so that it has a mean of 0 and a standard deviation of 1. We do this because variables on different scales (e.g., age in years vs. hours played in hundreds) can disproportionately influence model behavior. Scaling ensures that each variable contributes equally to the analysis.

players_scaled <- players_tidy |>
mutate(played_hours = scale(played_hours), Age = scale(Age))

### Data Splitting (Training & Testing Sets)
To evaluate how well our models will perform on new, unseen data, we split our dataset into two parts:

Training set (75%) – used to train and tune models.

Testing set (25%) – held back for evaluating model performance.

We used stratified sampling with `gender` as the stratifying variable. This ensures that the gender proportions in both sets are consistent with the overall dataset, which helps preserve class balance during training and testing.

set.seed(123)
players_split <- initial_split(players_scaled, prop = 0.75, strata = gender)
players_train <- training(players_split)
players_test <- testing(players_split)

players_train1 <- players_train |> mutate(set = "Train")
players_test1 <- players_test |> mutate(set = "Test")

players_combined <- bind_rows(players_train1, players_test1)

### Implementing Five-Fold Cross-Validation
Instead of relying on just one train-test split, we implemented five-fold cross-validation using `vfold_cv()`.

In five-fold CV:

The training data is split into 5 subsets (or "folds").

The model is trained on 4 folds and validated on the remaining one.

This process repeats 5 times, each time using a different fold for validation.

This technique helps detect overfitting and provides a better estimate of how the model is expected to perform on new data. Stratification by gender is used again here to ensure balance across folds.

players_vfold <- vfold_cv(players_train, v = 5, strata = gender)
players_vfold

### Data Visualization
To understand the data better, we created two visualizations using `ggplot`. These help explore patterns and potential relationships between gender, age, and played hours.

Figure 1: Total Played Hours by Gender
This bar chart shows the total number of hours played by each gender group. It helps identify which gender groups are more active or more represented in the game environment

gender_bar <- players_tidy |>
ggplot(aes(x = gender, y = played_hours, fill = gender)) +
geom_col() +
xlab("Gender of Players") +
ylab("Total Played Hours") +
ggtitle("Figure 1: Total Played Hours by Gender") +
scale_fill_brewer(palette = "Set2") +
theme(text = element_text(size = 20)) +
theme(axis.text.x = element_text(angle = 45, hjust = 1))
gender_bar

Figure 2: Gender Distribution by Age
This stacked bar chart displays how player age is distributed across different gender categories. It gives us insight into which age groups are dominant within each gender and how engagement may vary by demographic.

players_age_line <- players_tidy |>
ggplot(aes(x = Age, y = gender, fill = gender)) + 
geom_bar(stat = "identity") + 
xlab("Age of the Players") +
ylab("Gender of Players") +
ggtitle("Gender vs Age of the Players") +
scale_fill_brewer(palette = "Set2") + 
theme(text = element_text(size = 20))
players_age_line


Figure 3 shows how played hours vary across different genders, separately for the training and testing sets. We use boxplots because they effectively highlight the median, spread, and potential outliers in the data.
By faceting by set (Train vs Test), we check whether the model has a balanced representation of each gender and their play behavior in both splits. This ensures that training and testing sets are comparable and that the model is not learning from a skewed or biased subset. If the distribution looked drastically different between the two, it would raise concerns about the reliability of model performance metrics.

options(repr.plot.width = 8, repr.plot.height = 6)
ggplot(players_combined, aes(x = gender, y = played_hours, fill = gender)) +
geom_bar(stat = "identity") +
facet_wrap(~ set) +
xlab("Gender") +
ylab("Standardized Played Hours") +
ggtitle("Figure 3: Played Hours by Gender (Train vs Test)") +
scale_fill_brewer(palette = "Set2") +
theme(text = element_text(size = 20)) +
theme(axis.text.x = element_text(angle = 45, hjust = 1))

Figure 4 uses histograms to visualize how age is distributed by gender in the training and testing sets. This helps us confirm whether the age distribution is similar between the two splits and whether gender proportions are maintained across different age ranges.
We chose a histogram because it’s ideal for seeing the frequency distribution of a continuous variable like age. The alpha blending allows overlapping bars to show where genders co-occur more frequently. Ensuring these distributions align across splits is important for the validity and generalizability of the model, especially since age is a key explanatory variable in our analysis.

ggplot(players_combined, aes(x = Age, fill = gender)) +
geom_histogram(position = "identity", bins = 15) +
facet_wrap(~ set) +
xlab("Standardized Age") +
ylab("Count") +
ggtitle("Figure 4: Age Distribution by Gender (Train vs Test)") +
scale_fill_brewer(palette = "Set2") +
theme(text = element_text(size = 20))
