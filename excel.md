## 公式

### `FILTER`

条件筛选

- 语法

  ```markdown
  =FILTER(array, include, [if_empty])
  =FILTER(数组, 包括, [空值])
  ```

  `array`要返回的单列/多列，`include`，条件，是布尔值。

  ![](https://images.bizha.top/20240422163129.png)

### `LOOK UP`

#### 向量形式

  在一列或一行区域中查找值，并在第二个对应的一列或一行区域中返回相同位置的值。

- 语法

```markdown
=LOOKUP(lookup_value, lookup_vector,  [result_vector])
=LOOKUP(查找值,  查找向量,  [返回向量])
```

![](https://images.bizha.top/lookup.png)

- 其它方式`xlookup`

  ![](https://images.bizha.top/20240422085847.png)

#### 数组形式

从提供的数组的第一行或第一列查找指定的值，返回数组最后一行或最后一列中同一位置的值。

- 语法

  ```markdown
  =LOOKUP(lookup_value, array)
  =LOOKUP(查找值,  数组)
  ```

  

![](https://images.bizha.top/lookup2.png)

### `TEXT` 

将数值转换为文本，并以指定格式显示。

- 语法

  ```markdown
  =TEXT(value, format_text)
  =TEXT(数值, 数字格式)
  ```

数字格式规则及示例

- `0`

  数字占位符。如果单元格的内容大于占位符数量，则显示实际数字。如果单元格内容小于占位符数量，则**用0补足**。

例：`=TEXT(12,"0000")`返回的是`0012`，`=TEXT(12,"0.000")`返回`12.000`。

- `#`

  数字占位符。只显示有意义的0，不显示无意义的0。小数点后的数值如果大于`#`的数量，按`#`进行四舍五入。

- `.`在数字中显示小数点

- 逗号，千分符。

- `y`、`m`、`d` ，年月日。`yyyymmdd`

- `a`，星期几。`aaa`，`aaaa`

- `h`、`m`、`s`，时分秒。`hhmmss`

- am/pm，基于12小时制显示。

- 其它字符，`=TEXT(12345678/10000,”￥#.#万”)` 返回`¥1234.6万`

### `MATCH`

提供的单元格区域中查找指定值，并返回指定值在单元格区域中的相对位置。

- 语法

  ```markdown
  =MATCH(lookup_value, lookup_array, [match_type])
  =MATCH(查找值, 单元格区域, [匹配模式])
  ```

  注意
  
  如果匹配模式为精确匹配，查找值为文本格式时，查找值可以使用通配符：
  
  - `？`：匹配任意单个字符；
  - `*`匹配任意一串字符；
  - 如果查找实际的`?`、`*`号时，字符前增`~`。

### `OFFSET`

根据指定的行偏移量和列偏移量，从初始位置计算，返回指定偏移量的单元格或单元格区域。

- 语法

  ```markdown
  =OFFSET(reference, rows, cols, [height], [width])
  =OFFSET(初始位置, 行偏移量, 列偏移量, [返回行数], [返回列数])
  ```

例1：返回一个单元格

![](https://images.bizha.top/OFFSET.png)

例2：返回单元格区域
![](https://images.bizha.top/OFFSET2.png)



## `INDEX`

### 引用形式

返回指定的行与列交叉处的单元格引用

- 语法

```markdown
=INDEX(reference, row_num, [column_num], [area_num])
=INDEX(引用, 行号, [列号], [区域序号])
```

例1

![](https://images.bizha.top/20240422132305.png)

例2

![](https://images.bizha.top/20240422133246.png)

例3 行号/列号为空表示所有行/列。

![](https://images.bizha.top/20240422133853.png)

### 数组形式

根据指定的行号与列号返回表格或数组中的元素。

- 语法

```markdown
=INDEX(array, row_num, [column_num])
=INDEX(数组, 行号, [列号])
```



## `ADDRESS`

根据提供的行号和列号参数以文本格式返回其代表的单元格引用。

- 语法

```markdown
=ADDRESS(row_num, column_num, [abs_num], [a1],[sheet_text])
=ADDRESS(行号, 列标, [引用类型], [引用样式], [工作表])
```

例：

`=ADDRESS(1,1)`表示第一行第一列的单元格，默认是绝对引用，因此返回的是`$A$1`。



## INDIRECT

将文本格式引用文本转换为标准Excel引用。

- 语法

  ```markdown
  =INDIRECT(ref_text, [a1])
  =INDIRECT(文本格式引用,[a1])
  ```

  

![](https://images.bizha.top/INDIRECT.png)

## 实例

### 查找最后一个营业额大于0月份

![](https://images.bizha.top/20240422152640.png)



用到了xlookup的逆序查找，公式：`=XLOOKUP(TRUE(),B2:M2>0,$B$1:$M$1,,0,-1)`，#NA是未找到大于0的列。

![](https://images.bizha.top/20240422152929.png)

## PowerQuery
