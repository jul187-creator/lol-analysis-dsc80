# League of Legends Pro Play Analysis: Early Game Gold Impact

## Introduction

This project analyzes professional League of Legends match data from 2024 to understand how early-game performance, particularly gold advantages at 15 minutes, affects the final outcome of games.

**Research Question:** Does having a gold advantage at 15 minutes significantly increase a team's chance of winning the game?

## Dataset

- **Source:** Oracle's Elixir 2024 Professional LoL Match Data
- **Games Analyzed:** 8,411 complete professional matches
- **Leagues:** 47 different professional leagues worldwide
- **Time Period:** January 2024 - December 2024

## Key Findings

### Win Rate by Gold Difference at 15 Minutes

Our analysis reveals a strong relationship between early gold leads and victory:

- Teams with gold lead > 3000: **90.7% win rate**
- Teams with small gold lead (0-1000): **55.4% win rate**  
- Teams with small gold deficit (-1000-0): **44.7% win rate**
- Teams with gold deficit > 3000: **9.3% win rate**

This demonstrates that early game advantages are highly predictive of final outcomes in professional play.

## Hypothesis Testing

### Test 1: Gold Lead vs Win Rate
- **Null Hypothesis:** Teams with gold lead at 15 min have the same win rate as those without
- **Result:** Rejected (p < 0.001)
- **Conclusion:** Gold advantages at 15 minutes significantly increase win probability

### Test 2: First Dragon vs Kill Count  
- **Null Hypothesis:** Teams securing first dragon have same average kills as those who don't
- **Result:** Rejected (p < 0.001)
- **Conclusion:** First dragon is associated with significantly higher kill counts

## Predictive Modeling

### Baseline Model
- **Type:** Logistic Regression
- **Features:** Gold difference at 15 min + First blood
- **Test Accuracy:** ~73% (results may vary)

### Improvement Plans
For the final model, we plan to incorporate additional features including:
- Experience and CS differentials
- Objective control (dragons, heralds, towers)
- Kill/death/assist statistics
- Feature engineering and interaction terms

## Methodology

1. **Data Cleaning:** Filtered for complete games, kept only team-level statistics
2. **Exploratory Analysis:** Examined distributions and relationships between variables
3. **Missingness Analysis:** Assessed missing data mechanisms (NMAR, MAR)
4. **Hypothesis Testing:** Conducted permutation tests to validate relationships
5. **Predictive Modeling:** Built classification models to predict game outcomes

## Conclusion

Early game performance, measured by gold differential at 15 minutes, is a strong predictor of victory in professional League of Legends. Teams with significant gold leads rarely lose, while teams with deficits face an uphill battle. This information can help coaches and analysts make strategic decisions about whether to prioritize early-game aggression or late-game scaling.

---

*This project was completed as part of DSC 80: The Practice and Application of Data Science*

