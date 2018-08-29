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
match_df = match_df.dropna()
match_df['Year'] = match_df['Year'].astype(int)
match_df['date'] = match_df['Datetime'].str.split('-').str[0]
match_df['Stadium'] = match_df['Stadium'].str.split().str[0]
match_df = match_df.replace('Germany FR','Germany')
match_df = match_df.drop_duplicates(subset='MatchID', keep='first')
```
> **Do not use ```inplace=True```, make the code more clearly by data_df = data_df.operations**
> **```data_df['col_1'].str.split('-').str[0]``` better than ```.apply(lambda x:x.split('-')[0]) ```**


## 0.2 cup_df
- Germany FR -> Germany
- attendance: str.replace('.', '')
- year; astype(str) #TypeError: sequence item 0: expected str instance, int found





# 1. Total attendence of world cups by year 历届世界杯观众人数


