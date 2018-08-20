
**1. Replace in one column**

```python
new_df['country'].replace('Taiwan', 'China', inplace=True)
```
```python
data_df[''].apply(lambda x:'China' if x = 'Taiwan' else x) # must be a full if...else... pattern
```
```python
data_df.loc[ data_df['country']=='Taiwan', 'country' ] = 'China'
```
