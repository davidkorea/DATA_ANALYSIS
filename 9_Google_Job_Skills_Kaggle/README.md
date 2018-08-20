# Google Job Skills Kaggle


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
