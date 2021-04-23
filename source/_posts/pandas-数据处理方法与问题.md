---
title: pandas 数据处理方法与问题
date: 2021-03-14 21:35:34
tags: pandas, data preprocess
---

 **前言：**近期参与了一个大数据挖掘项目，我在项目中负责部分数据预处理的工作。主要使用的工具为pandas，而在使用pandas进行大数据预处理工作的时候，遇到了较多问题需要回头总结。因此在这篇博客中，记录一些我使用的pandas等数据预处理方法和一些问题的解决方法，以供我今后回忆。



[TOC]



### 1. Pandas 读取 处理 Big Data 的局限性

> There is a stark difference between large data and big data. With the hype around big data, it is easy for us to consider everything as “big data” and just go with the flow.
> The word large and big are in themselves ‘relative’ and in my humble opinion, *large data is data sets that are less than 100GB*.
> Pandas is very efficient with small data (**usually from 100MB up to 1GB**) and performance is rarely a concern.
>
> ——Why and How to Use Pandas with Large Data，But Not Big Data… ——Admond Lee

因此，对于使用pandas读取大文件数据（在这里是指csv）可以使用 Chunk size的方式，其步骤如下。

> 1. read in a chunk
> 2. process the chunk
> 3. save the results of the chunk
> 4. repeat steps 1 to 3 until we have all chunk results
> 5. combine the chunk result

样例代码如下。

```python
def chunk_preprocessing(chunk):
    return chunk

# read the large csv file with specified chunksize. set header name as NONE
dataChunk = pd.read_csv(data_filepath, chunksize=10000, header=None)

chunkList = [] # append each chunk df here

# each chunk is in df format
for chunk in dataChunk:
    # perform data filtering
    chunkFilter = chunk_preprocessing(chunk)
    
    # once the data filtering is over, append the chunk to list
    chunkList.append(chunkFilter)
    
# concat the list into df format
dataConcat = pd.concat(chunkList)

```

### 2. pandas 中读取 csv 格式文件的一些参数设置

这是一篇很好的详细讲述```to_csv()```方法的文章。https://www.cnblogs.com/traditional/p/12514914.html

一些我常使用的参数：

1. `nrows`：设置一次读入的行数，当只想知道大文件的格式时，可以使用该参数观察。

   ```python
   pd.read_csv('data.csv', nrows=1)
   ```

2. `dtype`：指定某一列的格式。

   ```python
   pd.read_csv('data.csv',dtype={"id": str})
   ```

3. `usecols`：指定使用的列。

   ```python
   pd.read_csv('data.csv', usecols=["name", "address"])
   ```

### 3. 更改列的数据类型

使用`astype()`来更改`datafram`数据结构的类型。

```python
df[['DATE','TIME']] = df[['DATE','TIME']].astype('int32', copy=False)
```

 使用`copy=False`来提高处理性能。

### 4. 判断列的数据类型

使用`dtype`来得到`datafram`中某一列的数据类型，进而与numpy中的数据类型进行判断

```python
df['time'].dtype == np.int64
```

### 5. `dataframe`去空和去重

```python
  df.dropna(how='any', inplace=True)
  df.drop_duplicates(keep = 'first', inplace = True) 
```

### 6. `dataframe` 数据过滤

```python
df_in = df_og[df_og.CODE == 21]
df_no_tf = df_od[df_od.transfer.isnull()]
df_has_tf = df_od[df_od.transfer.notnull()]
```

### 7. `dataframe`列的重命名

```python
df.rename(columns={'DATE':'date','TIME':'time','CODE':'ID'}, inplace = True)
```

### 8. `dataframe`去除列

```python
df.drop(['CODE'], axis=1, inplace = True)
```

### 9.  两个`dataframe`的合并（merge）

```python
df_od = df_out.merge(df_in, on=['ID','date'], how='left', copy=False) #左外连
```

**注意：**在进行`merge`前需要保证列表中的没有重复值，否则`merge`后数据会极度膨胀。

参考链接：（我不可能写得比这还好了）

> https://www.cnblogs.com/xk-bench/p/8379180.html
>
> https://zhuanlan.zhihu.com/p/102274476

### 10. `dataframe`和`series`的`apply`方法

参考链接：（我不可能写得比这还好了）

> https://zhuanlan.zhihu.com/p/100064394

### 11. `dataframe`的`groupby`方法

参考链接：（我不可能写得比这还好了）

> https://zhuanlan.zhihu.com/p/101284491

### 12. 对`series`按`' '`或`','`分割

```python
df['lines'] = df['lines'].str.split(' ')
```

### 13. 使用列表推导式而非`for`循环构建python列表

```python
list_a = [i for i in ser.data] # Good
for i in range(len(list_b) - 1):
	list_b.append(ser.data[i]) # Bad
```

### 14. 使用`enumerate`同时得到列表的`index`和`value`

```python
for idx, val in enumerate(range(a, b)):
	res[val] = (idx + 1) * t
```

### 15. 对`dataframe`使用`explode`方法，进行列转行

```python
df = df_od.explode('time_arr')
```

如果爆炸两列以上，可以使用自定义方法：

> ```python
> def unnesting(df, explode):
>     idx = df.index.repeat(df[explode[0]].str.len())
>     df1 = pd.concat([
>         pd.DataFrame({x: np.concatenate(df[x].values)}) for x in explode], axis=1)
>     df1.index = idx
>  
>     return df1.join(df.drop(explode, 1), how='left')
> 
> ```
>
> https://blog.csdn.net/m0_37773338/article/details/103754807

### 16. 对`dataframe`使用`iterrows`方法，来遍历行

```python
for index, row in df.iterrows():
    print(index)
    print(row)
```

### 17. 对`series`使用`unique`方法，来获取唯一值

```python
date_arr = df_one_month['DATE'].unique()
```

### 18. 使用`time.time()`方法来获取程序运行时间

```python
time0 = time.time()
print('time cost: {0:.2f}'.format(time.time()-time0))
```

### 19. 将时间戳变更为给定格式的时间

```python
int(time.strftime("%H%M%S", time.localtime(x+57600))) # windows
int(time.strftime("%H%M%S", time.localtime(x))) # linux
```

### 20. 将给定格式的时间变更为时间戳

```python
time.mktime(time.strptime('1970-01-03 00:00:00', "%Y-%m-%d %H:%M:%S"))
```

### 21. `time` 库

- `time.localtime([ sec ])`
    - 作用是格式化时间戳为本地的时间。 如果sec参数未输入，则以当前时间为转换标准。  
    - `sec` -- 转换为`time.struct_time`类型的对象的秒数。
    - 无返回值
- `time.strftime(format[, t])`  
    - 接收以时间元组，并返回以可读字符串表示的当地时间，格式由参数 `format` 决定。  
    - `format` -- 格式字符串。`t` -- 可选的参数t是一个`struct_time`对象。  
    - 返回以可读字符串表示的当地时间。
- `time.strptime(string[, format])`
    - 根据指定的格式把一个时间字符串解析为时间元组。
    - `string` -- 时间字符串。`format` -- 格式化字符串。
    - 返回`struct_time`对象。
- `time.mktime(t)`
    - 执行与`gmtime()` `，localtime()`相反的操作，它接收`struct_time`对象作为参数，返回用秒数来表示时间的浮点数。
    - `t` -- 结构化的时间或者完整的9位元组元素。
    - 返回用秒数来表示时间的浮点数。

### 22. `pandarallel` 库

```python
# 仅在linux环境下使用
from pandarallel import pandarallel
# 初始化
pandarallel.initialize()
# 将.apply()方法替换为.parallel_apply()方法
ser = df.parallel_apply(func, axis=1)
# 尽量返回series而非dataframe，此处bug存疑
```

链接：https://github.com/nalepae/pandarallel