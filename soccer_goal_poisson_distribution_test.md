Title: Soccer Goal Distribution Analysis
Date: 2023-12-14
Tags: soccer, statistics

I have lately been fascinated with mathematical modeling for soccer games. Expected Goal (xG) models are very fun to look at after a game. I also see a lot of interesting visuals from Soccermatics course (https://soccermatics.readthedocs.io/en/latest/index.html)

My first analysis is on the total number of goals a team can score in a game. StatsBomb provides free data for several games. I consumed all of the available LaLiga games (https://github.com/statsbomb/open-data/tree/master/data/matches/11).


Download the data files:

```bash
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/1.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/2.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/21.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/22.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/23.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/24.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/25.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/26.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/27.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/278.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/37.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/38.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/39.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/4.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/40.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/41.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/42.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/11/90.json
```

Read each file and stored the number of goals each team scored. It seems that the title of each file (90.json) corresponds to a specific season within that competition.

```
import json

goals_scored = []

# for each file data file
for competition_season_id in competition_season_ids:
    data_file = f'./{competition_season_id}.json'
    print(f'reading {data_file}')

    with open(data_file) as f:
        matches = json.load(f)
        # each data file has matches
        for _match in matches:
            goals_scored.append(_match["home_score"])
            goals_scored.append(_match["away_score"])
```

View the distribution of the raw data. It is very interesting that there are no games where a team has not scored 9 goals, but there are few instances where a team can score 10 goals.

```python
import matplotlib.pyplot as plt
import numpy as np

plt.hist(
    goals_scored,
    bins = np.arange(0, max(goals_scored) + 1) 
)

plt.title('Observed data')
plt.xlabel('Number of Goals Scored')
plt.ylabel('Count')
plt.legend()
plt.show()

```
![Histogram of observed goal counts]({filename}/images/observed_goal_distribution_12142023.png)


This clearly does not follow a normal distribution. Does it follow a poisson distribution?

First visualize the distribution:

```python
import numpy as np
from scipy.stats import poisson, chisquare

# fit poission distribution
mu = np.mean(goals_scored)
poisson_dist = poisson(mu)

# plot observed data
plt.hist(goals_scored, bins = np.arange(0, max(goals_scored) + 1) , density=True, alpha=0.7, label='Observed data')

# plot probability mass function
x_values = np.unique(goals_scored)
plt.plot(x_values, poisson_dist.pmf(x_values), 'bo-', label='Poisson distribution fit')

plt.xlabel('Number of Goals Scored')
plt.ylabel('Probability')
plt.legend()
plt.show()
```

![Histogram of observed goal counts with poisson distribution]({filename}/images/goals_poission_distribution_chart_12142023.png)

Seems to like an ok fit. Also created a table comparing the actual counts vs estimated with poisson pmf.

![Table of observed goal counts with poisson distribution]({filename}/images/goals_poission_distribution_table_12142023.png)



Next run chi squared test:

```python
# Get observed and expected frequencies to perform  the chi-square goodness-of-fit test
observed_freq, _ = np.histogram(goals_scored, bins = np.arange(0, max(goals_scored) + 1))
# Normalize the expected frequencies
expected_freq = poisson_dist.pmf(x_values) * len(goals_scored)
expected_freq = expected_freq * sum(observed_freq) / sum(expected_freq)

chi2_stat, p_value = chisquare(f_obs=observed_freq, f_exp=expected_freq)
print(f"Chi-square statistic: {chi2_stat}")
print(f"P-value: {p_value}")

if p_value < 0.05:
    print("The Poisson distribution does not fit the data well.")
else:
    print("The Poisson distribution fits the data well.")
```

Output:
```
Chi-square statistic: 305.91560826091995
P-value: 1.450815221371211e-60
The Poisson distribution does not fit the data well.
```

The lower p-value means that we reject the null hypothesis that the data follows a poisson distribution