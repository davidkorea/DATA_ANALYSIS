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
  - visualization
  ```python
  data_df['country'] = data_df['location'].apply(lambda x: x.split(',')[-1])
  data_df['country'] = data_df['country'].apply(lambda y: y.replace(' ', ''))
  # data_df['country'] = data_df['country'].replace(' ', '') # doesn't work
  # because this is not a full replace, space and string are mixed up together
  # but when you put it into a apply(lambda x:) it works!!!
  data_df['country'].value_counts()[:10].plot(kind='bar', rot=45, figsize=(12,8))
  ```    

2. **Replace in one df column** [_Issue_](https://github.com/davidkorea/DATA_ANALYSIS/issues/2)

  - Only full replace / completely replace, remove irrelevant elements first and leave the char we want to replace only
    - ```new_df['country'].replace('Taiwan', 'China', inplace=True)```
    - ```data_df.loc[ data_df['country']=='Taiwan', 'country' ] = 'China'```      
    - ```data_df['country'].apply(lambda x:'China' if x = 'Taiwan' else x)```, apply(lambda x:) must be a full if-else pattern.

  - Replace part of elements in df column
    - ```data_df.loc[ data_df['country'].str.contains('Taiwan'), 'country' ] = 'China'```, replace all contents if the cell contains the char we set.
    - ```data_df['country'] = data_df['country'].apply(lambda x: x.replace('Taiwan', 'China'))```, only replace the chars we set and leave the others as what they are.

# 2. Years of work experience

1. get minimum year 
  
  - get numbers through regex in a text
  - clean numbers
  - visualization
  ```python
  data_df['min_year_exp'] = data_df['minimum_qulifications'].apply(lambda x: re.findall('([0-9]+) year', x))
  data_df['min_year_exp'] = data_df['min_year_exp'].apply(lambda y: [0] if len(y)==0 else y)
  data_df['min_year_exp'] = data_df['min_year_exp'].apply(lambda z: max(z))
  
  data_df['min_year_exp'].value_counts()[:10].plot(kind='bar') # no axis, sorted
  sns.countplot(data_df['min_year_exp']) # auto axis, no sort
  sns.distplot(data_df['min_year_exp'].astype('int'), bins=15, kde=False) # auto axis, sorted
  ```
 > - ```sns.countplot()``` is better than ```value_counts().plot()```, beacuse sns shows axis without setting. 
 > - but sns doesn't sort data and will run as the order first in the column
 > - sns.distplot will fixed the above problem, axis go in sorted list







**2. dict can't tranform to df**

dict -> list -> df

```python
list = [ (i[0], i[1]) for i in dict.items()]
```

**3. sort dict and to list**

```python
sorted_dcit_list = sorted( dict.items(), key=lambda item : item[1])
```
