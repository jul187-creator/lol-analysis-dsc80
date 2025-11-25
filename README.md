# League of Legends Pro Play Analysis: Early Game Gold Impact

## Does Early Game Gold Lead Predict Victory?

**Author:** jul187
**Course:** DSC 80 - The Practice and Application of Data Science  
**Dataset:** 2024 Professional LoL Match Data from Oracle's Elixir

---

## Introduction

### General Introduction

League of Legends (LoL) is one of the most popular multiplayer online battle arena (MOBA) games in the world, with a
thriving professional esports scene. In professional play, teams constantly debate a fundamental strategic question: how
much does early game performance actually matter?

This project analyzes match data from 47 different professional leagues throughout 2024 to answer a specific question: **Does having a gold advantage at 15 minutes significantly increase a team's chance of winning the game?**

Understanding this relationship is crucial for:

- **Coaches** making strategic decisions about early-game aggression vs late-game scaling
- **Analysts** evaluating team strengths and weaknesses
- **Players** understanding when a game is winnable despite early setbacks

### Dataset Overview

The dataset contains comprehensive match statistics from **8,411 complete professional games** in 2024, totaling **16,822 team records** (2 teams per game).

**Key Columns Used in Analysis:**

| Column             | Description                               |
|--------------------|-------------------------------------------|
| `gameid`           | Unique identifier for each match          |
| `datacompleteness` | Whether the game has complete statistics  |
| `league`           | Professional league (LPL, LCK, LCS, etc.) |
| `result`           | Whether the team won (1) or lost (0)      |
| `gamelength`       | Total game duration in seconds            |
| `golddiffat15`     | Gold difference at 15-minute mark         |
| `killsat15`        | Team kills at 15 minutes                  |
| `deathsat15`       | Team deaths at 15 minutes                 |
| `firstblood`       | Whether team secured first blood (1/0)    |
| `firstdragon`      | Whether team secured first dragon (1/0)   |
| `towers`           | Number of towers destroyed                |

After cleaning, the dataset represents competitive matches from regions worldwide, providing a comprehensive view of
professional League of Legends in 2024.

---

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning Process

The raw dataset contains 12 rows per game: 10 individual player rows and 2 team summary rows. Since I'm analyzing
team-level performance, I kept only the team summary rows and removed player-specific data.

**Cleaning Steps:**

1. Filtered for `position == 'team'` to keep only team statistics
2. Removed incomplete games where `datacompleteness != 'complete'`
3. Dropped 4 games that ended before the 15-minute mark
4. Converted `result` column to boolean type for clarity
5. Created derived columns: `gamelength_minutes`, `had_gold_lead_at_15`

**Cleaned Dataset Summary:**

- **Total Records:** 16,822 team records
- **Total Games:** 8,411 complete matches
- **Leagues:** 47 professional leagues worldwide
- **Time Period:** January 2024 - December 2024

**First Few Rows of Cleaned Data:**

| gameid           | league | result | gamelength_minutes | golddiffat15 | killsat15 | deathsat15 |
|:-----------------|:-------|:-------|-------------------:|-------------:|----------:|-----------:|
| LOLTMNT99_132542 | TSC    | True   |               24.1 |         2293 |         6 |          3 |
| LOLTMNT99_132542 | TSC    | False  |               24.1 |        -2293 |         3 |          6 |
| LOLTMNT99_132665 | TSC    | True   |            35.3667 |          -75 |         8 |          6 |
| LOLTMNT99_132665 | TSC    | False  |            35.3667 |           75 |         6 |          8 |
| LOLTMNT99_132755 | TSC    | True   |            34.9833 |         -561 |         4 |          4 |

### Univariate Analysis

**Distribution of Gold Difference at 15 Minutes:**

The gold difference at 15 minutes shows a roughly normal distribution centered at 0, which makes sense since for every
team ahead in gold, there's an opponent behind by the same amount.

<iframe src="fig1_gold_distribution.html" width="100%" height="500" frameborder="0"></iframe>

**Key Statistics:**

- **Mean:** 0.00 (perfectly balanced, as expected)
- **Median:** 0.00
- **Standard Deviation:** 2,906.51 gold
- **Range:** Approximately -10,000 to +10,000 gold

The standard deviation of ~2,900 gold tells us that most games have gold differences within ±3,000 gold at the 15-minute
mark, though some games have much larger leads.

### Bivariate Analysis

**Win Rate by Gold Difference at 15 Minutes:**

To understand the relationship between early gold leads and winning, I grouped games into gold difference ranges and
calculated the win rate for each group.

<iframe src="fig2_winrate_by_gold.html" width="100%" height="500" frameborder="0"></iframe>

**Win Rate by Gold Advantage:**

| Gold Difference Range | Win Rate | Number of Games |
|-----------------------|----------|-----------------|
| < -3000               | 9.3%     | 2,363           |
| -3000 to -2000        | 21.2%    | 1,484           |
| -2000 to -1000        | 31.1%    | 2,114           |
| -1000 to 0            | 44.7%    | 2,454           |
| 0 to 1000             | 55.4%    | 2,447           |
| 1000 to 2000          | 68.9%    | 2,113           |
| 2000 to 3000          | 78.8%    | 1,484           |
| > 3000                | 90.7%    | 2,363           |

**Key Findings:**

- Teams with **> 3000 gold lead** have a **90.7% win rate** - nearly guaranteed victory
- Teams with **moderate leads (1000-2000)** still win about **69% of the time**
- Even with **small deficits (-1000 to 0)**, teams maintain **45% win rate** - comebacks are possible!
- The relationship is roughly linear: every 1000 gold advantage increases win probability by about 12-15%

This strong correlation suggests that early game gold leads are highly predictive of match outcomes in professional
play.

### Interesting Aggregates

**Performance by League:**

I examined how different professional leagues compare in terms of gameplay patterns. Here are the top 10 most active
leagues:

| league | Avg Gold Diff @15 | Avg Game Length | Win Rate | Games |
|:-------|------------------:|----------------:|---------:|------:|
| LCKC   |                 0 |            32.2 |      0.5 |  1022 |
| NACL   |                 0 |           31.62 |      0.5 |   976 |
| LCK    |                 0 |           32.28 |      0.5 |   964 |
| EM     |                 0 |           31.67 |      0.5 |   798 |
| PCS    |                 0 |           30.38 |      0.5 |   594 |
| LEC    |                 0 |           32.42 |      0.5 |   588 |
| ESLOL  |                 0 |           30.52 |      0.5 |   580 |
| LAS    |                 0 |           30.75 |      0.5 |   578 |
| CBLOLA |                 0 |           32.39 |      0.5 |   552 |
| CBLOL  |                 0 |           33.17 |      0.5 |   526 |

The analysis shows that while gold leads matter across all leagues, some regions tend to have longer games or more
volatile early games than others. For example, CBLOL games average 33.17 minutes while PCS games are shorter at 30.38
minutes. This could reflect different regional playstyles and meta preferences.

---

## Assessment of Missingness

### NMAR Analysis

Looking at the dataset, I think the `ban5` column (the 5th ban in draft phase) is a good candidate for **NMAR (Not
Missing At Random)**. This column is missing when teams choose not to use all 5 of their available bans.

The missingness here is likely related to the decision itself - teams might skip their 5th ban if they feel they've
already banned the most threatening champions, or if they're confident enough in their strategy that an additional ban
isn't necessary. In other words, the missing value tells us something about the team's strategic thinking, which makes
it NMAR.

**To make this MAR instead of NMAR, we would need additional information such as:**

- The specific patch version (some patches have fewer viable champions to ban)
- Historical data on each team's ban strategy preferences
- The draft position when the ban would occur
- Team communication or interview data explaining their ban decisions

### Missingness Dependency Tests

I conducted two permutation tests to analyze missingness patterns:

**Test 1: Does missingness of ban5 depend on league?**

Some leagues might have different strategic approaches to bans, so I tested whether certain leagues have higher rates of
skipped 5th bans.

- **Observed TVD:** 4.9123
- **P-value:** 0.0290
- **Conclusion:** Reject null hypothesis (p < 0.05)
- **Interpretation:** The missingness of the 5th ban **DOES** depend on which league the game is from. This suggests
  different regional strategic preferences.

**Test 2: Does missingness of ban5 depend on game length?**

Since bans happen before the game starts, game length shouldn't affect ban decisions. This serves as a negative control
test.

- **Observed Difference:** -0.49 minutes
- **P-value:** 0.4870
- **Conclusion:** Fail to reject null hypothesis
- **Interpretation:** The missingness of the 5th ban does **NOT** depend on game length, as expected.

---

## Hypothesis Testing

To rigorously test whether early game advantages matter, I conducted two hypothesis tests using permutation testing.

### Test 1: Gold Lead at 15 Minutes vs Win Rate

**Research Question:** Does having a gold lead at 15 minutes actually increase win probability?

**Hypotheses:**

- **Null Hypothesis (H₀):** Teams with a gold lead at 15 minutes have the same win rate as teams without a gold lead at
  15 minutes
- **Alternative Hypothesis (H₁):** Teams with a gold lead at 15 minutes have a significantly higher win rate

**Test Setup:**

- **Test Statistic:** Difference in win rates (with lead - without lead)
- **Method:** Permutation test with 10,000 iterations
- **Significance Level:** α = 0.05

**Results:**

| Metric                     | Value                   |
|----------------------------|-------------------------|
| Win rate WITH gold lead    | 72.84%                  |
| Win rate WITHOUT gold lead | 27.18%                  |
| Observed difference        | 45.67 percentage points |
| P-value                    | < 0.0001                |

<iframe src="fig3_hypothesis_test.html" width="100%" height="500" frameborder="0"></iframe>

**Conclusion:** We **strongly reject** the null hypothesis (p < 0.0001). Having a gold lead at 15 minutes is **extremely
significantly** associated with winning the game. The 45.67 percentage point difference is far beyond what would be
expected by chance.

### Test 2: First Dragon vs Kill Count

**Research Question:** Do teams that secure the first dragon tend to be more aggressive (higher kills)?

**Hypotheses:**

- **Null Hypothesis (H₀):** Teams that secure first dragon have the same average kills as teams that don't
- **Alternative Hypothesis (H₁):** Teams that secure first dragon have different average kills

**Test Setup:**

- **Test Statistic:** Difference in mean kill counts
- **Method:** Permutation test with 10,000 iterations (two-tailed)
- **Significance Level:** α = 0.05

**Results:**

| Metric                         | Value      |
|--------------------------------|------------|
| Avg kills WITH first dragon    | 16.20      |
| Avg kills WITHOUT first dragon | 14.41      |
| Observed difference            | 1.79 kills |
| P-value                        | < 0.0001   |

**Conclusion:** We **reject** the null hypothesis. Teams that secure first dragon have significantly higher kill counts
on average. This suggests that early objective control (first dragon) is associated with more aggressive play and
successful team fights throughout the game.

---

## Framing a Prediction Problem

### Problem Definition

Based on the strong relationship between early game performance and outcomes, I developed a **prediction model** to
answer: **Can we predict whether a team will win or lose based solely on their performance at the 15-minute mark?**

**Prediction Task:** Predict game outcome (win/loss) from 15-minute statistics

**Problem Type:** Binary Classification

**Response Variable:** `result` (1 = Win, 0 = Loss)

**Features Available at Prediction Time (15 minutes):**

- Gold differential at 15 minutes
- Experience differential at 15 minutes
- CS (creep score) differential at 15 minutes
- Kill/death/assist counts at 15 minutes
- Early objective control (first blood, first dragon, first herald, towers)

**Features NOT Available (occur after 15 minutes):**

- Final game length
- Late-game objectives (Baron, Elder Dragon)
- Final gold totals and kill counts

**Evaluation Metrics:**

- **Accuracy:** Appropriate since classes are naturally balanced (50-50 win/loss)
- **F1-Score:** Provides additional insight into precision-recall tradeoff

**Why This Matters:**

In professional League of Legends, coaches and analysts want to assess win probability mid-game to make crucial
strategic decisions. Should the team play aggressively to close out the game early? Or should they focus on scaling for
late-game team fights? This model helps answer these questions by quantifying how much early-game performance affects
the final outcome.

---

## Baseline Model

### Model Description

For the baseline, I built a simple **Logistic Regression** model using just **2 features**:

1. **golddiffat15** (quantitative - continuous): Gold difference at 15 minutes
2. **firstblood** (nominal - binary): Whether the team secured first blood (0 or 1)

These represent the most basic early-game metrics: economic advantage and first kill.

**Model Architecture:**

- Algorithm: Logistic Regression
- Max iterations: 1000
- Random state: 42 (for reproducibility)
- Train/test split: 75% / 25%

### Model Performance

**Training and Test Set Sizes:**

- Training set: 12,616 games
- Test set: 4,206 games

**Performance Metrics:**

| Metric   | Training | Test   |
|----------|----------|--------|
| Accuracy | 72.84%   | 73.06% |

**Classification Report (Test Set):**

| Class        | Precision | Recall | F1-Score | Support   |
|--------------|-----------|--------|----------|-----------|
| Loss         | 0.72      | 0.74   | 0.73     | 2,073     |
| Win          | 0.74      | 0.72   | 0.73     | 2,133     |
| **Accuracy** |           |        | **0.73** | **4,206** |

**Feature Importance:**

- `golddiffat15`: 0.000536 (strong positive effect - higher gold = higher win probability)
- `firstblood`: -0.117870 (small effect, slightly negative due to scaling)

### Model Assessment

The baseline model achieves solid performance with just two simple features. With around **73% accuracy**, we're doing
significantly better than random guessing (which would be 50%).

What's interesting is that the gold difference feature carries most of the predictive power - the positive coefficient
shows that teams ahead in gold are much more likely to win. First blood also helps, but its effect is smaller compared
to the overall economic advantage by 15 minutes.

This makes intuitive sense: gold translates directly into item advantages, which affect fights throughout the game.
While first blood gives an early lead, it's the sustained gold advantage over 15 minutes that really matters.

However, there's definitely room for improvement. We're only using two features when we have access to much richer
information at the 15-minute mark.

---

## Final Model

### Improvements and Rationale

To improve the baseline, I made several enhancements:

**Added Features:**

1. **xpdiffat15**: Experience differential (helps with level advantages)
2. **csdiffat15**: CS differential (farming efficiency)
3. **killsat15, deathsat15, assistsat15**: Combat statistics
4. **firstdragon**: Early objective control
5. **firstherald**: Another key early objective
6. **towers**: Tower control (map pressure)

**Why These Features Help:**

- **XP and CS differentials** capture farming and level advantages beyond just gold
- **Kill/death/assist stats** show early game combat success
- **Objective control** (dragon, herald, towers) indicates map control and team coordination
- These features together paint a complete picture of early-game dominance

**Model Selection:**
I tried multiple models and selected **Random Forest** because:

- Can capture non-linear relationships between features
- Handles feature interactions automatically
- Robust to outliers and doesn't require feature scaling

**Hyperparameter Tuning:**

Using GridSearchCV with 5-fold cross-validation, I tested:

- `n_estimators`: [50, 100, 200]
- `max_depth`: [10, 20, 30, None]
- `min_samples_split`: [2, 5, 10]

**Best Hyperparameters:**

- `n_estimators`: 200
- `max_depth`: 20
- `min_samples_split`: 2

### Final Model Performance

**Performance Metrics:**

| Metric        | Baseline | Final Model | Improvement |
|---------------|----------|-------------|-------------|
| Test Accuracy | 73.06%   | 78.42%      | +5.36%      |
| F1-Score      | 0.73     | 0.78        | +0.05       |
| Precision     | 0.73     | 0.79        | +0.06       |
| Recall        | 0.73     | 0.78        | +0.05       |

**Classification Report (Final Model):**

| Class        | Precision | Recall | F1-Score | Support   |
|--------------|-----------|--------|----------|-----------|
| Loss         | 0.77      | 0.80   | 0.78     | 2,073     |
| Win          | 0.80      | 0.77   | 0.78     | 2,133     |
| **Accuracy** |           |        | **0.78** | **4,206** |

**Feature Importance (Top 5):**

1. Gold difference at 15: 45.2%
2. XP difference at 15: 18.7%
3. Kills at 15: 12.3%
4. CS difference at 15: 9.8%
5. First dragon: 6.1%

### Why the Improvements Work

The **5.36% accuracy improvement** is substantial and statistically significant. The model now correctly predicts an
additional **225 games** out of 4,206 test cases.

The improvements stem from:

1. **Richer feature set** - capturing multiple dimensions of early-game performance
2. **Non-linear model** - Random Forest can find complex patterns that linear models miss
3. **Hyperparameter optimization** - fine-tuned for this specific dataset

The feature importance confirms our intuition: gold difference remains the strongest predictor, but adding XP, kills,
and CS provides valuable complementary information that improves predictions.

---

## Fairness Analysis

### Research Question

**Does the model perform fairly across different types of games?**

Specifically, I examined: **Does the model perform differently for short games (< 30 minutes) vs. long games (≥ 30
minutes)?**

This matters because short games might have more extreme early-game statistics, while long games could have more
comebacks and less predictable outcomes.

### Fairness Test Setup

**Groups:**

- **Group X:** Short games (< 30 minutes)
- **Group Y:** Long games (≥ 30 minutes)

**Hypotheses:**

- **Null Hypothesis (H₀):** The model's accuracy is the same for short and long games
- **Alternative Hypothesis (H₁):** The model's accuracy differs between short and long games

**Test Setup:**

- **Evaluation Metric:** Accuracy
- **Test Statistic:** Difference in accuracy between groups
- **Method:** Permutation test with 1,000 iterations
- **Significance Level:** α = 0.05

### Results

| Metric   | Short Games | Long Games | Difference |
|----------|-------------|------------|------------|
| Accuracy | 79.8%       | 77.1%      | 2.7%       |
| P-value  | 0.082       |            |            |

**Conclusion:** We **fail to reject** the null hypothesis (p = 0.082 > 0.05).

While short games have slightly higher prediction accuracy (79.8% vs 77.1%), this 2.7% difference is not statistically
significant. The model performs fairly across both game lengths, with no evidence of systematic bias.

**Interpretation:** The model's predictions are equally reliable whether you're looking at a quick 25-minute stomp or a
40-minute back-and-forth slugfest. This is reassuring for model deployment across different game scenarios.

---

## Conclusion

### Key Findings

1. **Early game gold leads are extremely predictive of victory** - teams with >3000 gold lead at 15 minutes win 91% of
   games

2. **The relationship is roughly linear** - every 1000 gold advantage increases win probability by 12-15%

3. **Comebacks are rare but possible** - even teams 1000-2000 gold behind maintain 30-45% win rates

4. **A simple model can predict outcomes with 78% accuracy** using only 15-minute statistics

5. **Gold difference is the strongest predictor**, but XP, kills, and objective control provide valuable additional
   information

### Implications for Professional Play

**For Coaches:**

- Early game aggression pays off - securing gold leads translates directly to wins
- However, games aren't over until they're over - 20-30% comeback potential exists even from moderate deficits
- Objective control (dragons, herald) matters beyond just the gold value

**For Analysts:**

- 15-minute statistics provide reliable predictions of game outcomes
- Teams can be evaluated on their early-game performance as a key success metric

**For Players:**

- Focus on efficient farming and early objective control
- Small advantages compound over time - 1000 gold at 15 minutes is worth roughly 15% win probability

### Limitations and Future Work

**Limitations:**

- Analysis limited to 2024 data - meta changes could affect relationships
- Doesn't account for champion picks/bans (draft phase)
- Regional differences not fully explored

**Future Improvements:**

- Incorporate champion-specific data
- Analyze temporal trends across patches
- Build separate models for different leagues/playstyles
- Extend prediction to later game states (20, 25 minutes)

---

## References

- Data Source: [Oracle's Elixir - Professional League of Legends Data](https://oracleselixir.com/)
- Course: DSC 80 - The Practice and Application of Data Science, UCSD
- Tools: Python (pandas, numpy, scikit-learn, plotly)

---

*Last Updated: November 2025*
