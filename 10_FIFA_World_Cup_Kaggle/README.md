# FIFA_World_Cup_Kaggle

1. data process and analysis, easy to hard
2. advanced visualization

# 목차
0. Clean data
1. Total attendence of world cups by year


# 0. Clean data
## 0.1 match_df
Steps
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
> 2. **```data_df['col_1'].str.split('-').str[0]``` better than ```data_df['col_1'].apply(lambda x:x.split('-')[0]) ```**
> 3. **data_df.drop_duplicates(subset='col_1', keep='first')**


## 0.2 cup_df
Steps
1. Germany FR -> Germany
2. attendance: str.replace('.', '')
3. year: astype(str)

Code
```python
cup_df = cup_df.replace('Germany FR','Germany')
cup_df["Attendance"] = cup_df['Attendance'].str.replace('.','').astype(int)
cup_df['Year'] = cup_df['Year'].astype(str)
```

# 1. Attendence of world cups by year 
## 1.1 Total attendence of world cups by year
Steps
0. use cups_df['Attendance'] directly
1. use match_df to get the attendance data
2. groupby year and attance.sum()
3. sns.barplot(data=year_attend_df, x='Year', y='Attendance', linewidth=1, edgecolor='K')
4. sns.pointplot()
Code
```python
sns.set_style('darkgrid')
year_attend_df = match_df.groupby('Year')['Attendance'].sum().reset_index()

plt.figure(figsize=(12,4))
sns.barplot(data=year_attend_df, x='Year', y='Attendance', linewidth=1, edgecolor='K')
plt.grid(True)
plt.title('Attendence by year', color='b', fontsize=18)
plt.show()
```
## 1.2 Average attendence by year
```python
year_attend_mean_df = match_df.groupby('Year')['Attendance'].mean().reset_index()

plt.figure(figsize=(12,4))
ax = sns.pointplot(year_attend_mean_df['Year'], year_attend_mean_df['Attendance'], color='w')
ax.set_facecolor('k')
plt.grid(True, color='grey', alpha=0.3)
plt.title('Average attendence by year')
plt.show()
```
