# DATA_ANALYSIS

# 1. Price distribution of paid apps
**Thinking**

1. 每一分类下的每一种app对应一个价格，查看数值型（price）数据分布。分布意味着落在某一价格区间的数量多少，需要划分合理数值区间，查看落在区间中的个数。
    - x轴app名称，y轴价格，很明显不可取
    - 大体查看分布，找出异常值outliers，离群值，去除后根据有效信息画出分布图
2. 使用盒型图查看大体分布，以便找出outliers。
    - ```plt.boxplot()```, 有且仅有垂直方向
    - ```sns.boxplot()```,  默认水平与x轴，需要手动指定y画出垂直盒型图
3.  大部分情况下，需要找到数据划分区间后，使用```plt.hist()```, ```sns.distplot()```查看柱形图分布.
    - ```plt.hist(normal_df['price'], bins=10)```
    - ```sns.distplot(normal_df['price'], bins=10, kde=False, rug=True), kde密度函数， rug数值点小竖线```
4. 若各项取值重复性高，如app一般定价为0.99，1.99，2.99，9.99等，可使用
    - ```data_df['price'].value_counts().plot(kind='bar')```
    - ```sns.countplot(data_df['price'])```
5. 使用分位数查看数值分布
    - ```data_df.describe()```
    - ```data_df['price'].quantile([0,0.25,0.5,0.75,1]) # 默认只显示0.5中位数```
6. 如果有2列数值型数据查看分布，可以使用scatter。此处显然不合适。
     - ```plt.scatter(data_df['prime_genre'], data_df['user_rating'], s=data_df['price']**2) # s= 气泡大小```
