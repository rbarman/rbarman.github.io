Title: Quick league competitiveness test
Date: 2023-12-17
Tags: soccer, statistics

This is a quick statistical test to compare the competitiveness between soccer leagues. I am using the free open statsbomb datasets, so I have very limited data to work with. For this initial analysis I am working with 1 season of Ligue1 and 1 season of Bundesliga1. I use the absolute value of goal difference as a proxy of competitiveness. Low goal differences can mean that teams are evenly matched and more competitive while high goal differences mean that few teams dominate and there is not a lot of overal competition.

Download the data: 

```bash
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/7/108.json -O ligue_data.json
!wget https://raw.githubusercontent.com/statsbomb/open-data/master/data/matches/9/27.json -O bundesliga_data.json
```

Compute goal differences:

```python
def get_goal_differences(data_path):
    goal_differences = []
    with open(data_path) as f:
        matches = json.load(f)
        for _match in matches:
            diff = abs(_match['home_score'] - _match['away_score'])
            goal_differences.append(diff)
    return goal_differences

bundesliga_goal_differences = get_goal_differences(bundesliga_data_path)
ligue_goal_differences = get_goal_differences(ligue_data_path)
```

The mean of bundesliga_goal_differences is 1.46 while mean of ligue_goal_differences is 1.85. Just from the mean values it seems that the bundesliga is more competitive.

I use a statistical test to see if there is a significant difference between the data from both league. Scipy library has many useful functions to perform these tests. 

```python
t_statistic, p_value = ttest_ind(bundesliga_goal_differences, ligue_goal_differences)

print(f'T-statistic: {t_statistic}')
print(f'P-value: {p_value}')

if p_value < 0.05:
    print('Reject the null hypothesis: There is a significant difference.')
else:
    print('Fail to reject the null hypothesis: There is no significant difference.')
```

The null hypothesis is that there is no difference between means of goal difference distribtions across both leagues.
The returned p-value of .13 so I fail to reject the null hypothesis. I don't have evidence that one league is more competitive than the other.