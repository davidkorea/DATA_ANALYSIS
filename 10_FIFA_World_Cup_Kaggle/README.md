# FIFA_World_Cup_Kaggle

1. data process and analysis, easy to hard
2. advanced visualization

# Index
:book:
:notebook_with_decorative_cover:
:ledger:
:point_up:
:balloon:
:small_red_triangle:
:back:
:arrow_heading_up:  

0. [Clean data](#0-clean-data)

    0.1 [match_df](#01-match_df)
    
    0.2 [cup_df](#02-cup_df)
1. [Attendence of world cups by year](#1-attendence-of-world-cups-by-year)

    1.1 [Total attendence of world cups by year](#11-total-attendence-of-world-cups-by-year)
    
    1.2 [Average attendence by year](#12-average-attendence-by-year)
2. [Total goals scored](#2-total-goals-scored---per-country-per-cup-)

    2.1 [Total goals scored by year](#21-total-goals-scored-by-year)
    
    2.2 [Total goals scored by country](#22-total-goals-scored-by-country)

3. [Matches, Stadiums with Highest number of attendance](#3-matches-stadiums-with-highest-number-of-attendance)

    3.1 [Matches with highest number of attendance](#31-matches-with-highest-number-of-attendance)
    
    3.2 [Stadiums with highest average attendance](#32-stadiums-with-highest-average-attendance)
    
4. [Figures distribution by year/cup](#4-figures-distribution-by-yearcup)

5. [Which countries had won the cup most?](#5-which-countries-had-won-the-cup-most)

    5.1 [Which country got winner most?](#51-which-country-got-winner-most)
    
    5.2 [Which countries had won the cup most? 1st,2nd,3rd](#52-which-countries-had-won-the-cup-most-1st2nd3rd)

6. [Top5 teams scored the most goals per cup ?](#6-top5-teams-scored-the-most-goals-per-cup-)

7. [Interactions between teams](#7-interactions-between-teams)

# 0. Clean data
[:page_facing_up:](#index)
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

**Code** -> scatter?????? as above 2.1
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

# 3. Matches, Stadiums with Highest number of attendance

## 3.1 Matches with highest number of attendance

**Step**
1. match_df
2. match name:create team1 vs team2 as the match name, two ways
3. show text on bar: date and stadium of this match
4. sort values: sort_values(by='Attendance'), top10

**Code**
```python
top_attend_match_df = match_df.sort_values(by='Attendance',ascending=False)[:10]
top_attend_match_df['vs'] = top_attend_match_df['Home Team Name']+ ' .VS. ' +top_attend_match_df['Away Team Name']
# top_attend_match_df['Home Team Name'].str.cat(top_attend_match_df['Away Team Name'], sep=' .vs. ')
top_attend_match_df['date'] = top_attend_match_df['Datetime'].str.split('-').str[0]
```
```python
text = 'Stadium:'+top_attend_match_df['Stadium'].str.split('-').str[0]+ ', Date:' +top_attend_match_df['date']

plt.figure(figsize=(10,8))
ax = sns.barplot(data=top_attend_match_df, x='Attendance', y='vs',palette='gist_ncar', 
            linewidth=1, edgecolor='k')
plt.ylabel('Match Teams')
plt.xlabel('Attendance')
plt.title('Matches with highest number of attendace')
plt.grid(True)
for i,j in enumerate(text):
    ax.text(x=100,y=i,s=j, fontsize=12, color='w', weight='bold')
    # x - start location at x-axis, y - which bar has this test, s - text
```
## 3.2 Stadiums with highest average attendance

**Code**
```python
attend_stadium_df = match_df.groupby(['Stadium','City'])['Attendance'].mean().reset_index()
top_attend_stadium_df = attend_stadium_df.sort_values('Attendance', ascending=False)[:10]

plt.figure(figsize=(12,8))
ax = sns.barplot(data=top_attend_stadium_df, x='Attendance', y='Stadium_short', 
                linewidth=1, edgecolor='k', palette='cool')
for i,j in enumerate(' City: '+top_attend_stadium_df['City']):
    ax.text(100,i,j, fontsize=14, color='white', weight='bold')
plt.grid(True)
plt.title('Stadiums with highest average attendance')
```

# 4. Figures distribution by year/cup

**Step**
1. cup_df
2. make subplots for the specific figures

**Code**
```python
plot_list = cup_df.columns.tolist()[-4:]
# ['GoalsScored', 'QualifiedTeams', 'MatchesPlayed', 'Attendance']

plt.figure(figsize=(22,16))
for i,plot in enumerate(plot_list):
    plt.subplot('22{}'.format(i+1))
    ax = sns.barplot(data=cup_df,x='Year',y=plot, palette='Blues')
    ax.set_title('{} per cup'.format(plot),fontsize=16)
# can only show the last plot????? NO!!! data_df.plot() cannot be used, sns.barplot() ok!
plt.subplots_adjust(wspace = 0.2, hspace = 0.4,top = 0.9)
plt.show()
```
# 5. Which countries had won the cup most? 

## 5.1 Which country got winner most? 
**Step**
1. cup_df
2. counts: cup_df['winner'].value_counts()
3. winner at year: cup_df.groupby('winner')['year'].apply(' '.join), write on bar
4. merge

**Code**
```python
winner_year_count_df = cup_df['Winner'].value_counts().reset_index()
winner_year_count_df.columns = ['Winner','count']
winner_year_df = cup_df.groupby('Winner')['Year'].apply(', '.join).reset_index()
winner_year_merge_df = pd.merge(winner_year_df,winner_year_count_df, on='Winner', how='left')
winner_year_merge_df.sort_values(by='count', inplace=True, ascending=False)

plt.figure(figsize=(12,8))
ax = sns.barplot(data=winner_year_merge_df, x='count', y='Winner', linewidth=1, edgecolor='k', palette='jet_r')
for i,j in enumerate('Years: '+winner_year_merge_df['Year']):
    ax.text(0.05, i,j, fontsize=14, color='white', weight='bold')
plt.grid(True)
```

## 5.2 Which countries had won the cup most? 1st,2nd,3rd
**Step**
1. cup_df
2. winner: cup_df['winner'].value_counts()
3. runnerup: cup_df['runnerup'].value_counts()
3. third: cup_df['third'].value_counts()
4. merge

**Code**
```python
prize_list = ['Winner','Runners-Up','Third']
prize_df_list = []
for prize in prize_list:
    prize_count = cup_df[prize].value_counts().reset_index()
    prize_count.columns = ['country','{}_count'.format(prize)]
    prize_year = cup_df.groupby(prize)['Year'].apply(' '.join).reset_index()
    prize_year.columns = ['country', '{}_year_str'.format(prize)]
    prize_df = pd.merge(prize_year,prize_count,on='country')
    prize_df_list.append(prize_df)
# all_df = pd.merge(prize_df_list,on='country',how='outer')
# TypeError: merge() missing 1 required positional argument: 'right'

all_df = prize_df_list[0].merge(prize_df_list[1],on='country',how='outer').merge(prize_df_list[2],on='country',how='outer')
all_df = all_df.sort_values(by=['Winner_count','Runners-Up_count','Third_count'], ascending=False)
# can not write text on bar plot, year_str doesn't make sense
```
```python
# Genernal plot
all_df.plot(kind='bar',x='country',y=['Winner_count','Runners-Up_count','Third_count'],
            figsize=(18,6),color =['gold','silver','brown'],
           linewidth=0.7, edgecolor='w',fontsize=15,width=0.8, align='center')
# width: bar/bin width, color=['red','blue','#d88c03']
plt.xlabel('Countries')
plt.ylabel('Number of podium')
plt.title('Number of podium by country')
# can not write text on!!!!!
```

```python
# Advanced plot
import plotly.offline as py
py.init_notebook_mode(connected=True)
import plotly.graph_objs as go
import plotly.tools as tls

data= []
# each x:country has the ammount of y:prize divided by name/type:prize, easy to understand to see the data
for prize in prize_list:
    country = all_df['country']
    count = all_df['{}_count'.format(prize)]
    data.append(
        go.Bar(
            x=country,
            y=count,
            name = prize,
        )
    )

layout = go.Layout(
    barmode = "stack", 
    title = "Number of podium by country",
    showlegend = False
)

fig = go.Figure(data=data, layout=layout)
py.iplot(fig, filename='pyplot-fifa')
```
# 6. Top5 teams scored the most goals per cup ? 

**Step**
> 1. ~~cup_df~~
> 2. ~~groupby winner, runnerup, third, and sum year.~~  **DO NOT transfrm to Dataframe**
> 3. ~~concat above 3 pd.Series~~~~

1. match_df
2. x - year, y - stacked top5 country goals, name = country
3. group year&home_team, count home_goal, group year&away-team, count away_goal. **DO NOT transfrm to Dataframe**
4. ~~merge above two pd.Series~~ pd.Series cannot be merged, use pd.concat
5. Series -> Dataframe, reset_index(), set new columns name
6. df['total_goal'] = df['home_goal'] + df['away_goal']
7. sort values: sort_values(by=['year','total_goal'], ascending=[Trye, False])
8. ~~df.gropby('year')['total_goal'].head(5)~~  **df.gropby('year').head(5)**， **_Most Important_**

**Code**
```python
home_cup_goal = match_df.groupby(['Year','Home Team Name'])['Home Team Goals'].sum()
away_cup_goal = match_df.groupby(['Year', 'Away Team Name'])['Away Team Goals'].sum()
country_cup_goal_df = pd.concat([home_cup_goal, away_cup_goal],axis=1)
country_cup_goal_df = country_cup_goal_df.reset_index()
country_cup_goal_df.columns = ['year','country','home_goal','away_goal']
country_cup_goal_df['total_goal'] = country_cup_goal_df['home_goal'] + country_cup_goal_df['away_goal']

new_df = country_cup_goal_df.copy()
new_df = new_df.sort_values(by=['year','total_goal'], ascending=[True,False])
new_df = new_df.groupby('year').head(5)
```
```python
# Advanced plot
country_list = new_df['country'].value_counts().index.tolist()
data = []
for country in country_list:
    year = new_df[new_df['country']==country]['year']
    goal = new_df[new_df['country']==country]['total_goal']
    data.append(
        go.Bar(
            x=year,
            y=goal,
            name=country
        )
    )
layout = go.Layout(
    barmode = "stack", 
    title = "Top 5 teams which scored the most goals",
    showlegend = False
)    
fig = go.Figure(data=data, layout=layout)
py.iplot(fig, filename='pyplot-fifa')
```

# 7. Interactions between teams

```python
import networkx as nx 

def interactions(year,color):    
    df  =  matches[matches["Year"] == year][["Home Team Name","Away Team Name"]]
    G   = nx.from_pandas_dataframe(df,"Home Team Name","Away Team Name")    
    plt.figure(figsize=(10,9))    
    nx.draw_kamada_kawai(G,with_labels = True,
                         node_size  = 2500,
                         node_color = color,
                         node_shape = "h", # node_shape = h-6 edge, s-4 edge
                         edgecolor  = "k",
                         linewidths  = 5 ,
                         font_size  = 13 ,
                         alpha=.8)                        
    plt.title("Interaction between teams :" + str(year) , fontsize =13 , color = "b")
    
interactions(2014,"r")
interactions(1994,"royalblue")
```

