# Google Job Skills Kaggle

- tasks
  - work experience of year 
  - required minimum degree 
  - required programming lamguage
  - each position's responsibilities, minimum qualifications, preferred qualifications
- code
  - ```data_df[''].apply(lambda x: ...)```
  - ```content_list = data_df[ data_df['col_1'].str.contains('a') ]['col_2'].tolist()```
  - ```long_text = ' '.join(content_list)```
  - ```Wordcloud().generate(long_text)```
  
**1. Replace in one column**

```python
new_df['country'].replace('Taiwan', 'China', inplace=True)
```
```python
data_df['country'].apply(lambda x:'China' if x = 'Taiwan' else x) # must be a full if...else... pattern
```
```python
data_df.loc[ data_df['country']=='Taiwan', 'country' ] = 'China'
```
**2. dict can't tranform to df**

dict -> list -> df

```python
list = [ (i[0], i[1]) for i in dict.items()]
```

**3. sort dict and to list**

```python
sorted_dcit_list = sorted( dict.items(), key=lambda item : item[1])
```
