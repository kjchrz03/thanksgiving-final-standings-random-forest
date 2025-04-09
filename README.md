# Predicting the Final Playoff Standings for NHL Teams

There’s an old saying in hockey that if your team is in a playoff berth by Thanksgiving, they will make it to playoffs by April. As a self-titled math nerd, I wanted to know if this old wives’ tale held water by building a model to test it out. This is an evolving model, currently in the first version. I have written a more narrative description of the model <a href="https://tapetotapemk.substack.com/p/predicting-final-standings-part-1"> here </a>  on my Substack.

## The Question
Is simply being inside the playoff structure enough to predict the final standings of a season? Or is there something else going on? Do other elements need to be in place in order to reliably make it into the playoffs by April?

## The Data
For my first version, I accessed NHL API data from 2000-2024. This dataset did not contain any data for 2004-2005 (total lockout year), 2012-2013 (partial lockout), 2019-2021 (Covid shortened seasons). The partial seasons were excluded for simplicty. 

The two datasets used to build the training data were:
- Standings
- Game Summaries

## The Features
Keeping it simple, I chose to use points, points percentage, goal differential, conference rank, win/loss streak, strength of schedule, conference, and (because the number of teams in the league and therefore each conference chanegd over the last 20+ years) a conference playoff chance baseline.

## The Models
### Random Forest
  

                     precision   recall     f1-score    support
             0       0.65        0.65       0.65        65
             1       0.69        0.69       0.69        75

    accuracy                                0.67       140
    macro avg        0.67        0.67       0.67       140
    weighted avg     0.67        0.67       0.67       140
    tx_points: 0.1383
    tx_points_pct: 0.2444
    tx_goal_diff: 0.2358
    tx_conference_rank: 0.1402
    tx_SOS: 0.1530
    conf_playoff_chance_baseline: 0.0308
    tx__-1: 0.0152
    tx__0: 0.0100
    tx__1: 0.0159
    conference__Western: 0.0163

### XGBoost
precision    recall  f1-score   support

                    precision    recall    f1-score    support
            0       0.66         0.65      0.65        65
            1       0.70         0.71      0.70        75           
    
    accuracy                               0.68        140
    macro avg       0.68      0.68         0.68       140
    weighted avg    0.68      0.68         0.68       140
    tx_points: 0.0959
    tx_points_pct: 0.2574
    tx_goal_diff: 0.1201
    tx_conference_rank: 0.0655
    tx_SOS: 0.0729
    conf_playoff_chance_baseline: 0.0968
    tx__-1: 0.0912
    tx__0: 0.0867
    tx__1: 0.0481
    conference__Western: 0.0655


## Hyperparameter Tuning
#Random Forest Grid
Fitting 5 folds for each of 48 candidates, totalling 240 fits
Random Forest Best Params: {'bootstrap': True, 'max_depth': None, 'min_samples_leaf': 2, 'min_samples_split': 2, 'n_estimators': 200}
Random Forest CV Score: 0.7660714285714286
Random Forest Test Report:
              

                   precision    recall    f1-score    support
           0       0.63         0.63      0.63        65
           1       0.68         0.68      0.68        75
    accuracy                              0.66        140
    macro avg      0.66         0.66      0.66        140
    weighted avg   0.66         0.66      0.66        140
    tx_points: 0.1383
    tx_points_pct: 0.2444
    tx_goal_diff: 0.2358
    tx_conference_rank: 0.1402
    tx_SOS: 0.1530
    conf_playoff_chance_baseline: 0.0308
    tx__-1: 0.0152
    tx__0: 0.0100
    tx__1: 0.0159
    conference__Western: 0.0163

### XGBoost Grid
Fitting 5 folds for each of 1296 candidates, totalling 6480 fits
XGBoost Best Params: {'colsample_bytree': 0.8, 'gamma': 0, 'learning_rate': 0.01, 'max_depth': 3, 'n_estimators': 100, 'reg_alpha': 0, 'reg_lambda': 10, 'subsample': 0.8}
XGBoost CV Score: 0.7714285714285714
XGBoost Test Report:
               
                   precision    recall    f1-score    support
           0       0.68         0.66      0.67        65
           1       0.71         0.73      0.72        75
    accuracy                              0.70       140
    macro avg      0.70         0.70      0.70       140
    weighted avg   0.70         0.70      0.70       140


## Initial Results
When it comes to conference, there is more variability in the standings in the Eastern Conference compared to the Western Conference; meaning the standings at American Thanksgiving are slightly more rigid in the West vs the East. These 'bubble' teams fall into one of two categories: Drop Out Teams and Late Surge Teams.

![image](https://github.com/user-attachments/assets/06efd687-5f05-4d69-8697-26e52479cc05)

## Drop Out Teams
These are teams that were within the playoff structure at American Thanksgiving, but failed to qualify for playoffs by the end of the season.

Drop Out Teams East:
Average points distance to 8th spot: 2.43
Median points distance to 8th spot: 2.0
Most frequent Points distance to 8th spot: 0
Avg change in points percentage: 0.1

Drop Out Teams West:
Average points distance to 8th spot: 2.58
Median points distance to 8th spot: 2.0
Most frequent Points distance to 8th spot: 0
Avg change in points percentage: 0.09

## Late Surge Teams
These are teams that were not within the playoff strucutre at American Thanksgiving, but did make the playoffs at the end of the season.

Eastern Late Surge Teams:
Average points distance to 8th spot: -2.89
Median points distance to 8th spot: -3.0
Most frequent Points distance to 8th spot: -2
Avg change in points percentage: -0.1

Western Late Surge Teams:
Average points distance to 8th spot: -2.05
Median points distance to 8th spot: -1.0
Most frequent Points distance to 8th spot: 0
Avg change in points percentage: -0.1

## Predictions
After running my test data through the best model, I also added a projected points calculation. The simple, standard formula is calculated as: `(points / games_played) * 82`
<table>
  <tr>
    <th>Team Name</th>
    <th>Points Percentage</th>
    <th>Goal Differential</th>
    <th>Strength of Schedule</th>
    <th>Projected Points</th>
    <th>Prediction</th>
  </tr>
  <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
    <tr>
    <td>cell 1</td>
    <td>cell 2</td>
    <td>cell 3</td>
  </tr>
</table>
**Eastern Conference**
Carolina Hurricanes	33	0.750000	30	1	1	0.494506	0.5	Eastern	123.0	1	Make Playoffs
Washington Capitals	31	0.704545	30	3	1	0.479726	0.5	Eastern	116.0	1	Make Playoffs
1	New Jersey Devils	32	0.640000	19	2	-1	0.507510	0.5	Eastern	105.0	1	Make Playoffs
3	Toronto Maple Leafs	28	0.636364	8	4	-1	0.507319	0.5	Eastern	104.0	1	Make Playoffs
5	New York Rangers	25	0.595238	11	6	-1	0.463625	0.5	Eastern	98.0	1	Make Playoffs
4	Florida Panthers	27	0.586957	3	5	1	0.499246	0.5	Eastern	96.0	1	Make Playoffs
7	Tampa Bay Lightning	24	0.571429	14	8	-1	0.547343	0.5	Eastern	94.0	1	Make Playoffs
8	Buffalo Sabres	23	0.522727	3	9	-1	0.490399	0.5	Eastern	86.0	0	Miss Playoffs
6	Boston Bruins	25	0.520833	-18	7	1	0.478487	0.5	Eastern	85.0	0	Miss Playoffs
9	Philadelphia Flyers	23	0.500000	-13	10	1	0.501047	0.5	Eastern	82.0	0	Miss Playoffs
10	Detroit Red Wings	22	0.500000	-11	11	1	0.475995	0.5	Eastern	82.0	0	Miss Playoffs
11	Columbus Blue Jackets	21	0.500000	-3	12	0	0.511354	0.5	Eastern	82.0	0	Miss Playoffs
12	Ottawa Senators	21	0.477273	-1	13	1	0.486595	0.5	Eastern	78.0	1	Make Playoffs
13	New York Islanders	21	0.456522	-11	14	-1	0.482864	0.5	Eastern	75.0	0	Miss Playoffs
15	Montréal Canadiens	19	0.431818	-21	16	1	0.509299	0.5	Eastern	71.0	0	Miss Playoffs
14	Pittsburgh Penguins	20	0.416667	-33	15	1	0.501447	0.5	Eastern	68.0	0	Miss Playoffs

## V2 Questions
I plan to explore the following in the next iteration of my model:
- Weighting more recent seasons
- Reducing the number of seasons included in the training data
- Shooting percentage
- Rush chances
- Danger chances/scoring location
- Posession metrics
