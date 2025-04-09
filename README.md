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
### Random Forest Grid
`Fitting 5 folds for each of 48 candidates, totalling 240 fits
Random Forest Best Params: {'bootstrap': True, 'max_depth': None, 'min_samples_leaf': 2, 'min_samples_split': 2, 'n_estimators': 200}
Random Forest CV Score: 0.7660714285714286
Random Forest Test Report:`              

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
`Fitting 5 folds for each of 1296 candidates, totalling 6480 fits
XGBoost Best Params: {'colsample_bytree': 0.8, 'gamma': 0, 'learning_rate': 0.01, 'max_depth': 3, 'n_estimators': 100, 'reg_alpha': 0, 'reg_lambda': 10, 'subsample': 0.8}
XGBoost CV Score: 0.7714285714285714
XGBoost Test Report:`
               
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

**Drop Out Teams East:**
<ul>Average points distance to 8th spot: 2.43</ul>
<ul>Median points distance to 8th spot: 2.0</ul>
<ul>Most frequent Points distance to 8th spot: 0</ul>
<ul>Avg change in points percentage: 0.1</ul>

**Drop Out Teams West:**
<ul>Average points distance to 8th spot: 2.58</ul>
<ul>Median points distance to 8th spot: 2.0</ul>
<ul>Most frequent Points distance to 8th spot: </ul>0</ul>
<ul>Avg change in points percentage: 0.09

## Late Surge Teams
These are teams that were not within the playoff strucutre at American Thanksgiving, but did make the playoffs at the end of the season.

**Eastern Late Surge Teams:**
<ul>Average points distance to 8th spot: -2.89</ul>
<ul>Median points distance to 8th spot: -3.0</ul>
<ul>Most frequent Points distance to 8th spot: -2</ul>
<ul>Avg change in points percentage: -0.1</ul>

**Western Late Surge Teams:**
<ul>Average points distance to 8th spot: -2.05</ul>
<ul>Median points distance to 8th spot: -1.0</ul>
<ul>Most frequent Points distance to 8th spot: 0</ul>
<ul>Avg change in points percentage: -0.1</ul>

## Predictions
After running my test data through the best model, I also added a projected points calculation. The simple, standard formula is calculated as: `(points / games_played) * 82`
These results are predicting the end of the 2024-2025 NHL season.

**Eastern Conference:**
| Team Name               | Points Percentage | Goal Differential | Strength of Schedule | Projected Points | Prediction      | Playoff Probability |
|-------------------------|-------------------|--------------------|----------------------|------------------|------------------|----------------------|
| Carolina Hurricanes     | 0.750000          | 30                 | 0.494506             | 123.0            | Make Playoffs    | 0.658114             |
| Washington Capitals     | 0.704545          | 30                 | 0.479726             | 116.0            | Make Playoffs    | 0.658114             |
| New Jersey Devils       | 0.640000          | 19                 | 0.507510             | 105.0            | Make Playoffs    | 0.665092             |
| Toronto Maple Leafs     | 0.636364          | 8                  | 0.507319             | 104.0            | Make Playoffs    | 0.660284             |
| New York Rangers        | 0.595238          | 11                 | 0.463625             | 98.0             | Make Playoffs    | 0.658001             |
| Florida Panthers        | 0.586957          | 3                  | 0.499246             | 96.0             | Make Playoffs    | 0.655622             |
| Tampa Bay Lightning     | 0.571429          | 14                 | 0.547343             | 94.0             | Make Playoffs    | 0.631949             |
| Buffalo Sabres          | 0.522727          | 3                  | 0.490399             | 86.0             | Miss Playoffs    | 0.414062             |
| Boston Bruins           | 0.520833          | -18                | 0.478487             | 85.0             | Miss Playoffs    | 0.343755             |
| Philadelphia Flyers     | 0.500000          | -13                | 0.501047             | 82.0             | Miss Playoffs    | 0.293880             |
| Detroit Red Wings       | 0.500000          | -11                | 0.475995             | 82.0             | Miss Playoffs    | 0.313680             |
| Columbus Blue Jackets   | 0.500000          | -3                 | 0.511354             | 82.0             | Miss Playoffs    | 0.382591             |
| Ottawa Senators         | 0.477273          | -1                 | 0.486595             | 78.0             | Miss Playoffs    | 0.393101             |
| New York Islanders      | 0.456522          | -11                | 0.482864             | 75.0             | Miss Playoffs    | 0.280545             |
| Montréal Canadiens      | 0.431818          | -21                | 0.509299             | 71.0             | Miss Playoffs    | 0.278458             |
| Pittsburgh Penguins     | 0.416667          | -33                | 0.501447             | 68.0             | Miss Playoffs    | 0.280545             |

**Western Conference**
| Team Name              | Points Percentage | Goal Differential | Strength of Schedule | Projected Points | Prediction      | Playoff Probability |
|------------------------|-------------------|--------------------|----------------------|------------------|------------------|----------------------|
| Winnipeg Jets          | 0.782609          | 34                 | 0.511890             | 128.0            | Make Playoffs    | 0.633411             |
| Minnesota Wild         | 0.727273          | 17                 | 0.528428             | 119.0            | Make Playoffs    | 0.666733             |
| Vegas Golden Knights   | 0.673913          | 17                 | 0.516662             | 111.0            | Make Playoffs    | 0.665092             |
| Dallas Stars           | 0.619048          | 16                 | 0.458403             | 102.0            | Make Playoffs    | 0.659042             |
| Calgary Flames         | 0.608696          | -1                 | 0.491892             | 100.0            | Make Playoffs    | 0.587584             |
| Vancouver Canucks      | 0.595238          | 0                  | 0.512361             | 98.0             | Make Playoffs    | 0.588707             |
| Los Angeles Kings      | 0.586957          | 4                  | 0.472435             | 96.0             | Make Playoffs    | 0.655622             |
| Colorado Avalanche     | 0.565217          | -7                 | 0.505343             | 93.0             | Make Playoffs    | 0.522112             |
| Edmonton Oilers        | 0.545455          | -5                 | 0.507190             | 89.0             | Miss Playoffs    | 0.485127             |
| Seattle Kraken         | 0.500000          | 0                  | 0.512910             | 82.0             | Miss Playoffs    | 0.388062             |
| Anaheim Ducks          | 0.500000          | -7                 | 0.480321             | 82.0             | Miss Playoffs    | 0.400432             |
| St. Louis Blues        | 0.479167          | -15                | 0.521079             | 79.0             | Miss Playoffs    | 0.288690             |
| Utah Hockey Club       | 0.477273          | -9                 | 0.502174             | 78.0             | Miss Playoffs    | 0.321610             |
| Chicago Blackhawks     | 0.409091          | -9                 | 0.456579             | 67.0             | Miss Playoffs    | 0.356767             |
| Nashville Predators    | 0.391304          | -19                | 0.487793             | 64.0             | Miss Playoffs    | 0.278458             |
| San Jose Sharks        | 0.380000          | -23                | 0.496682             | 62.0             | Miss Playoffs    | 0.278458             |

## V2 Questions
I plan to explore the following in the next iteration of my model:
- Weighting more recent seasons
- Reducing the number of seasons included in the training data
- Shooting percentage
- Rush chances
- Danger chances/scoring location
- Posession metrics
