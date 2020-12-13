## Mysql函数

### ifNull

```
IFNULL() 函数用于判断第一个表达式是否为 NULL，如果为 NULL 则返回第二个参数的值，如果不为 NULL 则返回第一个参数的值。
```

IFNULL() 函数语法格式为：

```
IFNULL(expression, alt_value)
```



### concat

使用方法：

```
CONCAT(str1,str2,…) 
```

返回结果为连接参数产生的字符串。如有任何一个参数为NULL ，则返回值为 NULL。