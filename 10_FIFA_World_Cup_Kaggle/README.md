# FIFA_World_Cup_Kaggle

1. data process and analysis, easy to hard
2. advanced visualization

# 목차
0. Clean data
1. Total attendence of world cups by year


# 0. Clean data
## 0.1 match_df
1. dropna
2. year: astype(int)
3. split date: str.split('-').str[0] 
4. stadium: split and use only first word
5. Germany FR -> Germany
6. MatchID: drop_duplicates

확인
1. ```match_df[match_df['Home Team Name'].str.contains('Germany')]```
2. ```match_df[match_df['MatchID']==300186461.0]```

Code
```python

```
## 0.2 cup_df
- Germany FR -> Germany
- attendance: str.replace('.', '')
- year; astype(str) #TypeError: sequence item 0: expected str instance, int found





# 1. Total attendence of world cups by year 历届世界杯观众人数


