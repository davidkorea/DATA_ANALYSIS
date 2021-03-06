# Google Job Skills Kaggle

- tasks
  - country of recruitment
  - work experience of year 
  - required minimum degree 
  - required programming lamguage
  - each position's responsibilities, minimum qualifications, preferred qualifications
- code
  - ```data_df[''].apply(lambda x: ...)```, only if the vector satisfied the criterion will apply on x, or will be left
  - ```content_list = data_df[ data_df['col_1'].str.contains('a') ]['col_2'].tolist()```
  - ```long_text = ' '.join(content_list)```
  - ```Wordcloud().generate(long_text)```

# 1. Country of recruitment
## 1.1 Code
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
## 1.2 Insights
1. Replace in one df column [Issue](https://github.com/davidkorea/DATA_ANALYSIS/issues/2)

  - Only full replace / completely replace, remove irrelevant elements first and leave the char we want to replace only
    - ```new_df['country'].replace('Taiwan', 'China', inplace=True)```
    - ```data_df.loc[ data_df['country']=='Taiwan', 'country' ] = 'China'```      
    - ```data_df['country'].apply(lambda x:'China' if x = 'Taiwan' else x)```, apply(lambda x:) must be a full if-else pattern.

  - Replace part of elements in df column
    - ```data_df.loc[ data_df['country'].str.contains('Taiwan'), 'country' ] = 'China'```, replace all contents if the cell contains the char we set.
    - ```data_df['country'] = data_df['country'].apply(lambda x: x.replace('Taiwan', 'China'))```, only replace the chars we set and leave the others as what they are.
    - ```data_df['col_1'] = data_df['col_1'].str.replace('.', '')```, # 360.000.000 -> 360000000
    
> **_Best_**: ```data_df['col_1'] = data_df['col_1'].str.replace('.', '')```    
>  > **_Best_**: ```data_df['col_1'].apply(lambda x: x.replace('char1', 'char2'))```.
>  > Only replace the specific char you want to replace in a text and leave others text there.
>  > Do whatever you want to do

# 2. Years of work experience

## 2.1 Code
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
  sns.distplot(data_df['min_year_exp'].astype('int'), bins=15, kde=False) # auto axis, sorted 【O】
  sns.boxplot(data=new_df['min_year_exp'].astype('int'))
  ```
## 2.2 Insights
1. Compare 3 count plot
  - ```sns.countplot()``` is better than ```value_counts().plot()```, beacuse sns shows axis without setting. 
  - but sns doesn't sort data and will run as the order first in the column
  - ```sns.distplot()``` will fixed the above problem, axis go in sorted list
2. Regex - regulation expression
  - ```re.findall('([0-9]+) year', x)```, find many numbers from[0,9] in front of 'year', only inside( ) will be returned
  - ```re.findall('[0-9]\d{0,2}')```, could have 0~2 more numbers behind the first number
  - ```re.findall('[0-9]\d{2}')```, could only have 2 more numbers behind the first number
  - ^ : begin
  - $ : end

# 3. Required minimum degree

Get a specific word this a text, count the frequency by a dict, transform the dict to a sorted list and make a Dataframe for visualization.

> get error code ?
## 3.1 Code
1. Get degree requirement from minimum qualifications
  - set a list of degree
  - init d dict for count
  - iterate the df column to serach the degree and update the count dict
  ```python
  degree_list = ['BA', 'BS', 'Bachelor', 'MBA', 'Master', 'PhD']
  degree_dict = dict( (x,0) for x in degree_list )
  for degree in degree_list:
      count = data_df['Minimum Qualicafitions'].str.contains(degree).sum() # sum up a boolean array
      degree_dict[degree] = count
  ```
  - transform dict to a sorted list 
  - transform sorted list to Dataframe
  - visualization
  ```python
  degree_requirement = sorted(degree_dict.items(), key=item:item[1], reverse=True)
  degree_df = pd.Dataframe(degree_requirement, columns=['degree', 'count'])
  
  sns.barplot(x=degree_df.degree, y=degree_df['count']) 
  # sns must set x and y, no need to set_index
  # must be degree_df['count'], or else TypeError: float() argument must be a string or a number, not 'method'
  
  degree_df.set_index('degree', inplace=True) # if no inplace, it doesn't work
  degree_df.plot(kind='bar', rot=0)
  ```
## 3.2 Insights
1. dict -> list -> df
  - dict can't be tranformed to df
  ```python
  list = [ (i[0], i[1]) for i in dict.items()]
  list = [ (key, value) for key,value in dict.items() ]
  ```
  - sort dict and to list**
  ```python
  sorted_dcit_list = sorted( dict.items(), key=lambda x : x[1], reverse=True)
  ```
**we need to transform a dict to a list anyway for make a df, so just sort a dict and it will return a list**

# 4. Required programming language

Same as required minimum degree

```python
language_list = language_list = ['Python', 'Java ', 'C#', 'PHP', 'Javascript', 'Ruby', 'Perl', 'SQL', 'Go ']
language_dict = dict( (x,0) for x in language_list )
for language in language_list:
    count = data_df['Minimum Qualifications'].str.contains(language).sum()
    language_dict[language] = count

language_requirement = sorted(language_dict.items(), key=x:x[1], reverse=True)
language_df = pd.dateframe(language_requirement, columns=['language', 'count'])

sns.barplot(x=language_df['language'] , y=language_df['count'])
```

# 5. Word cloud

Generate the word cloud of [responsibilities, minimum qualifications, preferred qualifications] for the specific word in job titles.

## 5.1 Code 
  - search specific word in the Title column
  - filter the data_df with the criterion above
  - get the relevant text of the filtered data_df and put them into a list
  - make a long_text, a very long string by the list above
  - generate wordcloud
  ```python
  wordcould_types = ['Responsibilities', 'Minimum Qualifications', 'Preferred Qualifications']
  
  def plot_wordcloud(title, wordcould_types):
      for wordcould_type in wordcould_types:
          filtered_list = data_df[data_df['Title'].str.contains(title)][wordcould_type].tolist()
          long_text = ' '.join(filtered_list)
          word_cloud = Wordcloud().generate(long_text)
          plt.imshow(word_cloud)
          plt.title('{} - {}'.format(title, wordcould_type))
          plt.axis('off')
          plt.show()
  
  plot_wordcloud('Analyst', wordcould_types)
  plot_wordcloud('Solution', wordcould_types)
  plot_wordcloud('Sales', wordcould_types)
  ```
