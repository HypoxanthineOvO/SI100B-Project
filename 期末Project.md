# 期末Project

[TOC]



## Week 2

### Introduction

#### 文件介绍

In the matrix, the values (counts) represent the number for each feature (i.e. gene; row) that are detected in each cell (column).

在这个矩阵中，值（Count）代表每个特征（即基因;行），在每个单元格（Column）中检测到。

- `genes.tsv`: a tabular file with information about the 32,738 genes in 2 columns (Ensembl gene id and the gene symbol)

> 一个包含32,738个基因信息的表格文件，分两列(Ensembl基因id和基因符号)
>

- `barcodes.tsv`: a tabular file with the barcode for each of the 2,700 cells

> 包含2700个单元格中每个单元格的条形码的表格文件
>

- `matrix.mtx`: a condesed version of the count matrix.

> 计数矩阵的简化版本。

### Section 1: Parse dataset with scipy and pandas

#### 题目概况

我们把数据存储在Pandas的Dataframe中。

If you write the code correctly, you will get a dataframe with 2700 rows and 32738 columns; where each row, each barcode, represents a cell; each column, represents a gene; and the value of the data represents the count number of the corresponding gene fragment in the corresponding cell.

> 如果你写的代码正确，你会得到一个2700行32738列的Dataframe；
>
> · 每一行，每一个条形码，代表一个单元格
>
> · 每一列代表一个基因
>
> 数据的值表示相应细胞中相应基因片段的计数个数。

#### 函数一览

```python
mmread(文件名)
```

阅读 mtx 文件，得到COO格式的稀疏矩阵

```python
pd.read_csv(文件名，sep='	',header=None) 
```

阅读 csv 文件，sep是分割的参数，header =  None 是指数据里没有指定行列索引

```python
mwant = mp.transpose()
```

COO数据的转置

```python
ndm = mwant.toarray()
ndm = ndm.astype(int)
```

转换成ndarray数据，然后把所有浮点数转化为整数

```python
df = pd.DataFrame(data = ndm , columns = DFColumnname , index = DFRowname) 
df.columns.name = 'genes'
df.index.name = 'barcodes'
```

生成目标矩阵，行列名称等设置



### Section 2: Preprocessing

#### Section 2.1: n_genes_by_counts

The first section is to calculate the number of genes with at least 1 count in each cell.

> 第一部分是计算每个细胞中至少有1个基因的数量。

```python
ret = (data != 0).sum(axis = 1)
```

这是Dataframe计数的一种方式，就是把data中每一列里不等于0的项在每一行求和。



#### Section 2.2: total_counts

The second section is to calculate the total number of counts in each cell.

> 第二部分是计算每个单元中的计数总数。

```python
ret = data.sum(axis = 1)
```

我太聪明了！！！直接把之前那个判断（data！= 0）去掉，之前是计算列数而现在是直接求和

没有陷入上次的误区（手写函数）

棒！



#### Section 2.3: highest_expr_genes

The third section is to calculate the total number of counts in each cell.

> 第三部分是计算每个单元中的计数总数。

```python
sortdata = data.sum(axis = 0)
sortdata.sort_values(ascending= False,inplace= True)
listdata = list(sortdata.index)
ret = listdata[:ntop]
```

按行求和并排序，获取名称，切片。



#### Section 2.4: filter_cells

First, we want to filter cells.

> 首先，我们要过滤单元格。

```python
mic = min_counts
mac = max_counts
celldata = data.sum(axis=1)
if mic != None:
    ftddata = data[mic < celldata]
if mac != None:
    ftddata = data[celldata < mac]
ret = ftddata
```

重点是其中“筛选符合条件的项”的语法



#### Section 2.5: filter_genes

出了一点小问题。

思路上，把2.4的图表横纵坐标交换一下，操作就完全一致了。

最后再交换一次即可。

问题是数据有一个微小的偏差，我感觉是题目给的数据有问题。