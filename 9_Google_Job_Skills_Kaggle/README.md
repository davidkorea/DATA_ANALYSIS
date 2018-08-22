# Google Job Skills Kaggle

- tasks
  - country of recruiment
  - work experience of year 
  - required minimum degree 
  - required programming lamguage
  - each position's responsibilities, minimum qualifications, preferred qualifications
- code
  - ```data_df[''].apply(lambda x: ...)```, only if the vector satisfied the criterion will apply on x, or will be left
  - ```content_list = data_df[ data_df['col_1'].str.contains('a') ]['col_2'].tolist()```
  - ```long_text = ' '.join(content_list)```
  - ```Wordcloud().generate(long_text)```

# 1. Country of recuiment

  1. get country from location column
    - vector operations   
  ```python
  data_df['country'] = data_df['location'].apply(lambda x: x.split(',')[-1])
  data_df['country'] = data_df['country'].apply(lambda y: y.replace(' ', ''))
  # data_df['country'] = data_df['country'].replace(' ', '') # doesn't work, 
  ```






# 2. Years of work experience

1. get minimum year 
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
