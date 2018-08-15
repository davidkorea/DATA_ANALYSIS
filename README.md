# DATA_ANALYSIS

# 1. Price Distribution of Paid Apps

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
6. 多变量分布可以使用scatter。此处单列数据显然不合适。
     - ```plt.scatter(normal_df['prime_genre'], normal_df['price'])```
     - ```plt.scatter(data_df['prime_genre'], data_df['user_rating'], s=data_df['price']**2) # s= 气泡大小```


# 2. Category VS Price

1. How many catrgoris
2. Each cate vs price distribution ?
    - boxplot
3. 挑选top_N的类别进行分析
    - 获得top10的分类名称，value_counts.index是此列数据的类别名称，非索引行号
    
    ```top_n_cate_index = data_df['cate'].value_counts()[:N].index.tolist()```
    - 根据上面的分类名称，匹配找到data_df['cate']列的内容，选出相应的rows，返回新Dataframe
    
    ```top_n_cate_df = data_df[data_df['cate'].isin(top_n_cate_index)]```
    - 整理筛选后的df，添加索引，删除原来索引号
    
    ```python
    top_n_cate_df = top_n_cate_df.set_index(np.array([i for i in range(top_n_cate_df.shape[0])]))
    top_n_cate_df.drop('Unnamed: 0', axis=1, inplace=True)
    ```
    - 根据筛选后的top_n df，画出各个Category的price分布
    
    ```sns.boxplot(x=top_n_cate_df['cate'], y=top_n_cate_df['price'])```
    
    **并不是只有一列全部为数值的项可以画出分布boxplot，在一个Dataframe中，每一row中包含有Category，price的数据也可以画出boxplot**
   
   
# 3. Free VS Paid Apps Quantity
1. 创建data_df副本
    ```data_df_new = data_df.copy()```
2. 将topn之外的Category全部命名为others
    - 获取非topn的行索引
    - 根据行索引找到对应类别进行重命名
    ```python
    other_cate_index = data_df_new[ ~data_df_new['prime_genre'].isin(top_n_cate_index) ].index
    data_new_df.loc[ other_cate_index, 'prime_genre' ] = 'others' #注意是方括号[]
    ```
    > Reference: [iloc VS loc in pandas](https://github.com/davidkorea/DATA_ANALYSIS/issues/1)
3. 添加一列用来标记是否收费, 画出sns.countplot
    ```python
    data_df_new['type'] = np.where(data_df_new['price']==0, 'free', 'paid')
    sns.countplot(data_df_new['type'])
    ```
4. 数据透视表
    ```python
    free_paid_count = data_df_new.pivot_table(index=['prime_genre'], 
                                                columns=['type'], 
                                                values=['id'], 
                                                aggfunc='count')
    # values 可以选择任意一列，aggfunc='count' 计数前面一列的个数
    free_paid_count.columns = free_paid_count.columns.droplevel()
    # 删除掉上面一行count的列名‘id’
    ```
5. 分组统计，转换为数据透视表
    - 分组统计
    ```python
    groupby_df = data_df_new.groupby( ['prime_genre', 'type'] ).size().to_frame()
    # size()用于计数，to_frame()转化为Dataframe
    grouby_df.columns = ['count'] # 设置列名为count
    ```
    - 转换为数据透视表
    ```python
    pivots = groupby_df.pivot_table(index=['prime-genre'], columns=['type'], values=['count'])
    # values 使用上面count列的值
    ```
6. 数据透视表添加free，paid百分比，画出堆叠柱状图
    - 数据透视表添加columns
    ```python
    free_paid_count['free_perc'] = free_paid_count['free'] / (free_paid_count['free']+free_paid_count['paid'])
    free_paid_count['paid_perc'] = free_paid_count['paid'] / (free_paid_count['free']+free_paid_count['paid'])
    ```
    - 堆叠柱状图
    ```python
    free_paid_count[['free_perc','paid_perc']].plot(kind='bar', stacked=True, figsize=(12,8))
    ```
# 4. Price VS Rating

1. 收费与否与评分关系，使用已经分类的data_df_new（top10 + others）
    - 散点图
    ```python
    plt.scatter(x=data_df_new['prime_genre'], y=data_df_new['user_rating'], s=10*data_df_new['price'])
    ```
    - 小提琴图
    ```python
    sns.violinplot(data=data_df_new, x='prime_genre', y='user_rating', hue='type', split=True)
    # hue 按照‘type’分开画图， split=True，不同type画在同一个小提琴，但左右分开
    # split：将split设置为true则绘制分拆的violinplot以比较被hue拆分后的两个量
    ```
    
# 5. Price VS Size

1. add a new column named 'size_mb'
    ```normal_df['size_mb'] = normal_df['size_bytes'] / 1000000```
2. Distribution of size_mb, plot as what #1 does
    - ```sns.boxplot(y=normal_df['size_mb'])```
    - ```normal_df['size_mb'].describe()```
    - ```sns.distplot(normal_df['size_mb'], bins=10, kde=False, rug=True)```
    - ```sns.scatterplot(x=normal_df['size_mb'], y=normal_df['price'])```
