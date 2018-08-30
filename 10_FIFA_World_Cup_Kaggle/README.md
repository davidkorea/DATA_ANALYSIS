# FIFA_World_Cup_Kaggle

1. data process and analysis, easy to hard
2. advanced visualization

# 목차
0. [Clean data](#0-clean-data)
1. Total attendence of world cups by year


# 0. Clean data
## 0.1 match_df
**Steps**
1. dropna
2. year: astype(int)
3. split date: str.split('-').str[0] 
4. stadium: split and use only first word
5. Germany FR -> Germany
6. MatchID: drop_duplicates

**확인**
1. ```match_df[match_df['Home Team Name'].str.contains('Germany')]```
2. ```match_df[match_df['MatchID']==300186461.0]```

**Code**
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
**Steps**
1. Germany FR -> Germany
2. attendance: str.replace('.', '')
3. year: astype(str)

**Code**
```python
cup_df = cup_df.replace('Germany FR','Germany')
cup_df["Attendance"] = cup_df['Attendance'].str.replace('.','').astype(int)
cup_df['Year'] = cup_df['Year'].astype(str)
```

# 1. Attendence of world cups by year 
## 1.1 Total attendence of world cups by year

**Steps**

0. use cups_df['Attendance'] directly
1. use match_df to get the attendance data
2. groupby year and attance.sum()
3. sns.barplot(data=year_attend_df, x='Year', y='Attendance', linewidth=1, edgecolor='K')
4. sns.pointplot()

**Code**

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
> **```.to_frame()``` vs ```.reset_index()```**, 둘 다 return a Dataframe, 하지만 뒷것이 나을듯

# 2. Total goals scored -> per country per cup [☆☆☆☆☆]

## 2.1 Total goals scored by year
**Steps**

1. use cup_df
2. sns.scatter(x='Year', y='GoalsScored', s=cup_df['Attendance']*0.0001, c='GoalsScored', cmap='Blues')
    - s: scatter size
    - c, cmap: c의 값에 따라, cmap섹상이 변하다 
  
**Code**
```python
plt.figure(figsize=(14,6))
plt.scatter(data=cup_df, x='Year', y='GoalsScored', s=cup_df['Attendance']*0.0001,
            c='GoalsScored', cmap='Blues', linewidth=1 ,edgecolor='blue')
plt.colorbar()
plt.xticks(cup_df['Year'].unique())
# with no plt.xticks, will generate only 4 ticks [1940,1960,1980,2000] not each year
plt.yticks(np.arange(60,200,20))
plt.title('Total goals scored by year',color='b')
plt.xlabel("year")
plt.ylabel("total goals scored")
plt.show()
```
## 2.2 Total goals scored by country

**Steps**

1. match_df
2. home_country: match_df.groupby('home name')['home goal'].sum().reset_index(), rename columns=['country','home_goal']
2. away_country: match_df.groupby('away name')['away goal'].sum().reset_index(), rename columns=['country','away_goal']
3. merge all: all_country = pd.merge(home_country,away_country,oh='country',how='outer')
4. add total: all_country['total_goal'] = all_country['home_goal'] + all_country['away_goal']
5. sort values: all_country.sort_values(by='total_goal')
6. plot: all_country.plot(kind='bar', x='country', y='total_goal'), sns.barplot()

**Code**
```python
home_df = match_df.groupby('Home Team Name')['Home Team Goals'].sum().reset_index()
home_df.columns = ['country', 'home_goals']
away_df = match_df.groupby('Away Team Name')['Away Team Goals'].sum().reset_index()
away_df.columns = ['country', 'away_goals']
country_goal_df = pd.merge(home_df, away_df, on='country', how='inner')
country_goal_df['total_goals'] = country_goal_df['home_goals']+country_goal_df['away_goals']
top_country_goal_df = country_goal_df.sort_values(by='total_goals',ascending=False)[:10]
```
```python
# plot hone,away goal stacked bar
top_country_goal_df.head(10).plot(kind='bar', x='country', y=['home_goals','away_goals'], stacked=True)
# select sepcific columns to plot, multi-y
# ONLY this format(data_df.plot(kind='bar', ... )) can use multi-y 
```
```python
# plot total_goal
top_country_goal_df.head(10).plot(kind='bar', x='country', y='total_goals')
sns.barplot(x='country', y='total_goals', data=top_country_goal_df)
```
