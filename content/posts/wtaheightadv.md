---
title: "Datascience: WTA Height Advantages"
date: 2018-09-11
tags: [ "python", "datascience" ]
---

I've been playing with Pandas and the Jupyter Notebook to learn how to clean up and extract insights from large datasets. Here's an example of discovering the relationship between player height and win %.


### What advantage does height infer in Women's Tennis? ###

Using dataset: [https://www.kaggle.com/joaoevangelista/wta-matches-and-rankings#wta.zip](https://www.kaggle.com/joaoevangelista/wta-matches-and-rankings#wta.zip)


```python
import pandas as pd 
%matplotlib inline
import matplotlib.pyplot as plt

df = pd.read_csv('/Users/sam/Downloads/wta/matches.csv', low_memory=False, dtype={
})
df.shape
```




    (50577, 33)




```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>best_of</th>
      <th>draw_size</th>
      <th>loser_age</th>
      <th>loser_entry</th>
      <th>loser_hand</th>
      <th>loser_ht</th>
      <th>loser_id</th>
      <th>loser_ioc</th>
      <th>loser_name</th>
      <th>loser_rank</th>
      <th>...</th>
      <th>winner_hand</th>
      <th>winner_ht</th>
      <th>winner_id</th>
      <th>winner_ioc</th>
      <th>winner_name</th>
      <th>winner_rank</th>
      <th>winner_rank_points</th>
      <th>winner_seed</th>
      <th>year</th>
      <th>Unnamed: 32</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>3</td>
      <td>128</td>
      <td>17.859001</td>
      <td>NaN</td>
      <td>R</td>
      <td>NaN</td>
      <td>200002</td>
      <td>CRO</td>
      <td>Mirjana Lucic</td>
      <td>49.0</td>
      <td>...</td>
      <td>R</td>
      <td>170.0</td>
      <td>200001.0</td>
      <td>SUI</td>
      <td>Martina Hingis</td>
      <td>1.0</td>
      <td>6003.0</td>
      <td>1.0</td>
      <td>2000.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>3</td>
      <td>128</td>
      <td>27.118412</td>
      <td>Q</td>
      <td>R</td>
      <td>NaN</td>
      <td>200004</td>
      <td>AUS</td>
      <td>Kerry Anne Guse</td>
      <td>133.0</td>
      <td>...</td>
      <td>R</td>
      <td>167.0</td>
      <td>200003.0</td>
      <td>BEL</td>
      <td>Justine Henin</td>
      <td>63.0</td>
      <td>510.0</td>
      <td>NaN</td>
      <td>2000.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>128</td>
      <td>31.378508</td>
      <td>NaN</td>
      <td>R</td>
      <td>NaN</td>
      <td>200005</td>
      <td>USA</td>
      <td>Jolene Watanabe Giltz</td>
      <td>118.0</td>
      <td>...</td>
      <td>R</td>
      <td>NaN</td>
      <td>200006.0</td>
      <td>SVK</td>
      <td>Karina Habsudova</td>
      <td>53.0</td>
      <td>574.0</td>
      <td>NaN</td>
      <td>2000.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>128</td>
      <td>22.006845</td>
      <td>NaN</td>
      <td>R</td>
      <td>NaN</td>
      <td>200007</td>
      <td>CRO</td>
      <td>Silvija Talaja</td>
      <td>23.0</td>
      <td>...</td>
      <td>R</td>
      <td>182.0</td>
      <td>200008.0</td>
      <td>AUS</td>
      <td>Alicia Molik</td>
      <td>116.0</td>
      <td>245.0</td>
      <td>NaN</td>
      <td>2000.0</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>3</td>
      <td>128</td>
      <td>24.821355</td>
      <td>NaN</td>
      <td>R</td>
      <td>NaN</td>
      <td>200010</td>
      <td>ITA</td>
      <td>Rita Grande</td>
      <td>60.0</td>
      <td>...</td>
      <td>R</td>
      <td>165.0</td>
      <td>200009.0</td>
      <td>THA</td>
      <td>Tamarine Tanasugarn</td>
      <td>72.0</td>
      <td>439.0</td>
      <td>NaN</td>
      <td>2000.0</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 33 columns</p>
</div>




```python
df.year.value_counts().sort_index()
```




    2000.0    2893
    2001.0    3098
    2002.0    3140
    2003.0    2930
    2004.0    2805
    2005.0    2843
    2006.0    2787
    2007.0    2778
    2008.0    2790
    2009.0    2722
    2010.0    2781
    2011.0    2804
    2012.0    2910
    2013.0    2776
    2014.0    2785
    2015.0    2651
    2016.0    2900
    2017.0    2181
    Name: year, dtype: int64



We have data on 50577 matches from 2000-2007


```python
# Clean data
df.drop(df[df['winner_ht'] == 'R'].index, inplace=True, axis='rows')
df['winner_ht'] = df['winner_ht'].astype(float)
df.drop(df.columns[df.columns.str.contains('unnamed',case = False)], axis=1, inplace=True)
```


```python
# New subset
hts = pd.DataFrame(df[['winner_ht', 'loser_ht']])
hts.dropna(inplace=True)

# Add absolute height difference column
hts['ht_diff'] = abs(hts['winner_ht'] - hts['loser_ht'])

# Add boolean 'taller player won' column
hts['winner_taller'] = hts['winner_ht'] > hts['loser_ht']

hts.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>winner_ht</th>
      <th>loser_ht</th>
      <th>ht_diff</th>
      <th>winner_taller</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>42</th>
      <td>163.0</td>
      <td>168.0</td>
      <td>5.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>53</th>
      <td>180.0</td>
      <td>170.0</td>
      <td>10.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>58</th>
      <td>165.0</td>
      <td>169.0</td>
      <td>4.0</td>
      <td>False</td>
    </tr>
    <tr>
      <th>64</th>
      <td>170.0</td>
      <td>167.0</td>
      <td>3.0</td>
      <td>True</td>
    </tr>
    <tr>
      <th>79</th>
      <td>168.0</td>
      <td>158.0</td>
      <td>10.0</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



Let's check how many matches we have for each absolute height difference:


```python
hts.ht_diff.value_counts()
```




    2.0     1764
    3.0     1704
    5.0     1627
    7.0     1499
    1.0     1434
    4.0     1397
    6.0     1304
    8.0     1248
    10.0    1209
    9.0      997
    12.0     899
    11.0     892
    0.0      889
    13.0     632
    14.0     604
    15.0     553
    16.0     450
    17.0     430
    19.0     290
    18.0     278
    20.0     186
    21.0     159
    22.0     104
    23.0      78
    24.0      46
    25.0      39
    27.0      27
    26.0      20
    29.0      13
    28.0      12
    31.0       6
    32.0       3
    30.0       2
    Name: ht_diff, dtype: int64




```python
# Remove rows where there was no difference in height (as 'winner_taller' always false)
hts.drop(hts[hts.ht_diff == 0].index, inplace=True)

# Remove heights differences for which we don't have enough data
hts.drop(hts[hts.ht_diff > 23].index, inplace=True)
hts.shape
```




    (19738, 4)



We ended up with 19,738 matches where there was a height difference, for which we have enough data to make a meaningful comparison


```python
# Plot the absolute height difference against the mean of whether taller player won (0.0 -> 1.0)
plt.figure(figsize=(10,5))
plt.plot(hts.groupby('ht_diff')['winner_taller'].mean())
plt.xlabel('Height difference (cm)')
plt.ylabel('Taller player win %')
plt.show()
```


![png](/images/output_11_0.png)

