---
title: "Datascience exploration 2: ELO performance vs bet profitability in tennis"
date: 2018-09-12
tags: [ "python", "datascience" ]
---

I wrote a function to genetically evolve the parameters to an ELO algorithm (code not included here) and compared the performance of these parameters across bookmakers when using the ELO prediction to place hypothetical bets. Interestingly there is a tiny advantage if you can secure the absolute best odds. The underdogs with higher ELO looks too good to be true and that's because the number of available bets where an underdog has a higher ELO is very small.

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import random
from collections import namedtuple

%matplotlib inline 
```


```python
filenames = [(str(x) + ".xls") for x in range(2000, 2013)] + [(str(x) + ".xlsx") for x in range(2013, 2019)]
all_data = pd.concat([pd.read_excel(name, parse_dates=['Date']) for name in filenames], sort=False, ignore_index=True)
```


```python
# Drop columns not in the latest year's dataframe

df = all_data[years_data[-1].dtypes.index]
```


```python
# Drop columns we're not interested in

df = df.drop(['ATP', 'Location', 'Tournament', 'Series','Best of', 'Round', 'W1', 'L1', 'W2', 'L2', 'W3', 'L3', 'W4', 'L4', 'W5', 'L5', 'Wsets', 'Lsets', 'Comment'], axis=1)
```


```python
# Add num played games for each player

s = df[['Winner','Loser']].stack()
matchcounts = s.groupby(s).cumcount().unstack().add_suffix('_matches')
df = df.join(matchcounts)
df.columns
```




    Index(['Date', 'Court', 'Surface', 'Winner', 'Loser', 'WRank', 'LRank', 'WPts',
           'LPts', 'B365W', 'B365L', 'EXW', 'EXL', 'LBW', 'LBL', 'PSW', 'PSL',
           'MaxW', 'MaxL', 'AvgW', 'AvgL', 'Winner_matches', 'Loser_matches'],
          dtype='object')




```python
# Find the max winner/loser odds out of the ones we have

df['MaxLocalW'] = df[['PSW', 'B365W', 'EXW', 'LBW']].max(axis=1)
df['MaxLocalL'] = df[['PSL', 'B365L', 'EXL', 'LBL']].max(axis=1)
```


```python
# Populate ELO columns

Player = namedtuple("Player", ["rating", "numMatches"])

def calcElo(winner, loser, k_params, r_params):
    
    evolved_k_params = [414, 6, 0.406072194388297]
    fte_k_params = [250, 5, 0.4]
    default_k_params =  evolved_k_params

    evolved_r_params = [4, 684]
    fte_r_params = [10, 400]
    default_r_params =  evolved_r_params
    
    def fK(numMatches, params=default_k_params):
        return params[0]/(numMatches+params[1])**params[2]
    
    def fR(rating, params=default_r_params):
        return (float(params[0])**(rating/params[1]))

    rW = fR(winner.rating, params=r_params)
    rL = fR(loser.rating, params=r_params)
    eW = rW/(rW+rL)
    eL = rL/(rW+rL)
    eloW = winner.rating + fK(winner.numMatches, params=k_params) * (1 - eW)
    eloL = loser.rating + fK(loser.numMatches, params=k_params) * (0 - eL)
    return (round(eloW), round(eloL))

def populateELO(k_params=None, r_params=None):
    elo = {}
    winnerElos = []
    loserElos = []
    winnerElosPrevious = []
    loserElosPrevious = []

    for index, row in enumerate(df.itertuples(), 1):
        winnerName = row.Winner
        loserName = row.Loser
        if not winnerName in elo:
            elo[winnerName] = 1500
        if not loserName in elo:
            elo[loserName] = 1500

        winner = Player(elo[winnerName], row.Winner_matches)
        loser = Player(elo[loserName], row.Loser_matches)

        c = calcElo(winner, loser, k_params, r_params)
        
        winnerElosPrevious.append(elo[winnerName])
        loserElosPrevious.append(elo[loserName])
        
        elo[winnerName] = c[0]
        elo[loserName] = c[1]

        winnerElos.append(c[0])
        loserElos.append(c[1])
        
    winnerSeries = pd.Series(winnerElos)
    loserSeries = pd.Series(loserElos)

    winnerSeriesPrev = pd.Series(winnerElosPrevious)
    loserSeriesPrev = pd.Series(loserElosPrevious)

    df['ELO_W_Exit'] = winnerSeries.values
    df['ELO_L_Exit'] = loserSeries.values
    df['ELO_W_Entry'] = winnerSeriesPrev.values
    df['ELO_L_Entry'] = loserSeriesPrev.values
    
populateELO(default_k_params, default_r_params)
```


```python
def getReturns(dataset, bookie, eloMinMatches=10, eloMinDiff=0):
    bW = bookie + "W"
    bL = bookie + "L"
    subset = dataset.dropna(subset=[bW, bL])
    subset = subset[pd.to_numeric(subset[bW], errors='coerce').notnull()]
    subset = subset[pd.to_numeric(subset[bL], errors='coerce').notnull()]    
    subset = subset.dropna(subset=["WRank", "LRank", bW, bL, 'MaxLocalW', 'MaxLocalL', 'MaxW', 'MaxL', 'AvgW', 'AvgL'])
    subset = subset[(subset['Winner_matches'] > eloMinMatches) & (subset['Loser_matches'] > eloMinMatches)]
    subset = subset[abs(subset.ELO_W_Entry - subset.ELO_L_Entry) > eloMinDiff]
        
    elo_predicts_underdog_correctly_subset = subset[(subset[bW] > subset[bL]) & (subset.ELO_W_Entry > subset.ELO_L_Entry)]
    elo_predicts_underdog_incorrectly_subset = subset[(subset[bW] < subset[bL]) & (subset.ELO_W_Entry < subset.ELO_L_Entry)]
    elo_predicts_fav_correctly_subset = subset[(subset[bW] < subset[bL]) & (subset.ELO_W_Entry > subset.ELO_L_Entry)]
    elo_predicts_fav_incorrectly_subset = subset[(subset[bW] > subset[bL]) & (subset.ELO_W_Entry < subset.ELO_L_Entry)]
    
    roi_smallest_odds = subset[bW][subset[bW] < subset[bL]].sum() / len(subset)
    roi_highest_odds = subset[bW][subset[bW] > subset[bL]].sum() / len(subset)
    roi_best_ranking = subset[bW][subset.WRank < subset.LRank].sum() / len(subset)
    roi_random = subset[bW].sample(int(len(subset)/2)).sum() / len(subset)
    roi_elo = subset[bW][subset.ELO_W_Entry > subset.ELO_L_Entry].sum() / len(subset)                                                                   
    roi_elo_underdog = elo_predicts_underdog_correctly_subset[bW].sum() / (len(elo_predicts_underdog_correctly_subset) + len(elo_predicts_underdog_incorrectly_subset))
    roi_elo_fav = elo_predicts_fav_correctly_subset[bW].sum() / (len(elo_predicts_fav_correctly_subset) + len(elo_predicts_fav_incorrectly_subset))                                                                  
    
    toROI = lambda x: (x -1) * 100    
    return [
        toROI(roi_smallest_odds),
        toROI(roi_highest_odds),
        toROI(roi_best_ranking), 
        toROI(roi_random), 
        toROI(roi_elo),
        toROI(roi_elo_underdog),
        toROI(roi_elo_fav)
    ]

results = pd.DataFrame()
bookie_labels = ['Pinnacle', 'Bet365', 'Ladbrokes', 'Expekt', 'Oddsportal max odds', 'Max odds in this database']
for i, bookie in enumerate(['PS', 'B365', 'LB', 'EX', 'Max', 'MaxLocal']):
    all_values = getReturns(df, bookie, eloMinMatches=10, eloMinDiff=0)
    results = results.append(pd.DataFrame([all_values], 
                                          index=[bookie_labels[i]], 
                                          columns=['Every favourite', 'Every underdog', 'Best ATP ranking', 'Random', 'Highest ELO', 'Underdogs with higher ELO', 'Favourites with higher ELO']))

results.plot.bar(figsize=(15,10), width=0.8)
plt.ylabel("Return on investment %")
plt.legend(loc=4)
plt.show()

```


![png](/images/output_7_0.png)

  
