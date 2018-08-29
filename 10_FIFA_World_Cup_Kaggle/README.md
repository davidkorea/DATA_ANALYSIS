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
> 1. **Do not use ```inplace=True```, make the code more clearly and readable by data_df = data_df.operations**
> 2. **```data_df['col_1'].str.split('-').str[0]``` better than ```.apply(lambda x:x.split('-')[0]) ```**
> 3. **data_df.drop_duplicates(subset='col_1', keep='first')**


## 0.2 cup_df
1. Germany FR -> Germany
2. attendance: str.replace('.', '')
3. year: astype(str)

Code
```python
cup_df = cup_df.replace('Germany FR','Germany')
cup_df["Attendance"] = cup_df['Attendance'].str.replace('.','').astype(int)
cup_df['Year'] = cup_df['Year'].astype(str)
```

# 1. Total attendence of world cups by year 
  历届世界杯观众人数


