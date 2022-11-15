# python数据分析入门——pandas
*本篇笔记是我在阅读《利用python进行数据分析》pandas部分后的凭借自己理解复述的内容，如有纰漏还恳请指正。由于本人水平所限，表述未必严谨，系统学习请参照《利用python进行数据分析》。*

## 什么是pandas
pandas命名有多种说法，一种比较常见的是*panel data analysis*，即面板数据分析，这表明pandas生来就是数据分析的工具。我的理解中，pandas的数据结构在接口方面像是python的字典，而底层的数据结构则是基于numpy的ndarray，因此对于pandas提供的数据结构进行操作时，可以参考字典和ndarray的操作。

## 数据结构
pandas提供两种主要的数据结构*Series* 和 *DataFrame*。

Series可以由Python的列表或Numpy的1darray直接构造而来，是一种一维的数据结构。另外可以指定每个元素的索引值（可以是任何python对象），若采用这种方式进行构造则需要传入一组键值对组成的字典。反过来，可以通过Series的index属性得到索引值列表，value属性得到1darray。

DataFrame可以由Python列表的列表或Numpy的2darray直接构造出，是一种二维的数据结构。也可以传入colunms属性和index属性，作为二维元素的索引，同样也可以指定这两个属性，返回相应的列表。除此之外，DataFrame也可以由多个Series（或python列表）加上列名（name）以字典的形式传入构造。

DataFrame十分值得探讨的就是索引形式。如前所述，DataFrame接口类似于字典，可以指定列名获取整**列**（Series）；底层基于numpy的ndarray，可以通过切片形式得到多**行**视图。但是对于列名或行索引存在*数值*的情况，这两种索引会产生歧义，所以推荐使用pandas专有的loc或iloc属性进行索引。

如果使用iloc属性索引，可以完全将索引过程看作是Numpy的ndarray索引（*可以参考我的第一篇笔记*）。如果使用loc属性进行所以，就是通过列标签或行索引进行索引，其他方面与iloc也很类似。一个值得注意的与iloc的区别就是，loc切片**包含**最后一项，这也是用标签索引的特点

## 算法
由于pandas底层的数据结构是numpy的ndarray，因此pandas的算术运算行为非常类似numpy的ndarray。一个值得注意的点就是pandas处理的数据来自实际生活，因此需要特别需要考虑NA值的处理。处理方式也很简单，比如在add函数中可以指定fill_value参数来填充空值。

另外还有一些重要的算法：
1. reindex重新索引；
2. sort_index/sort_value排序（*对于DataFrame通过by指定列*）；
... ...

## 读数据

