# 一、常见函数

使用方法：

`select 函数名(实参列表) 【from 表】 ` 

1. 单行函数
2. 分组函数



## 单行函数

### 1.字符函数

1.1 length 获取参数值的字节数

```sql
select length('john');
```

1.2 concat 拼接字符串

```sql
select contcat(last_name, '_', first_name) from user;
```

1.3 upper、lower  改变大小写

```mysql
select concat(upper(last_name), '_', lower(first_name)) from user;
```

1.4 substr、substring 截取字符(索引都是从1开始)

```sql
select substr(str, pos);#截取从pos开始之后的所有字符

select substr(str, pos, len);#截取从指定索引处，指定长度的自读 
```

1.5 instr 返回字串出现的第一次索引，找不到就返回0 

```sql
select instr(str1, str2);
```

1.6 trim 去前后指定字符

```slq
select trim(str);#默认的就是去前后空格
select trim(str from str1); #去str1中前后的str
```

1.7 lpad 用指定的字符str1，实现左填充指定长度length（length指的是最终实现的字符串的长度）

```sql
select lpad(str, length, str1);
```

1.8 rpad 右填充

1.9 replace 替换

```sql
select replace(str, str1, str2);#将str中的str1替换成str2
```



### 2. 数学函数

2.1 round 四舍五入

```sql
select round(1.65);
```

2.2 ceil 向上取整，返回>=该参数的最小整数

```sql
select ceil(1.03); #结果为2
```

2.3 floor 向下取整，返回<=该参数的最大整数

```sql
select floor(-9.0); #结果为10
```

2.4 truncate  截断

```slq
select truncate(1.44443, 1);#截断为小数点后一位
```

2.5 mod 取余

```sql 
mod(a, b)
```



### 3. 日期函数

3.1 now 返回当前系统日期+时间

```slq
select now();
```

3.2 curdate 返回当前日期，不包含时间

3.3 curtime 返回当前时间，不包含日期

3.4 获取指定的部分，年、月、日、小时、分钟、秒

```sql
select year(now());
select year('1999-3-3');
select month(now());
...
```

3.5 str_to_date 将日期格式的字符装换成指定的日期

```slq
select str_to_date('1993-3-4', '%Y-%c-%d');
```

3.6 date_format 将日期转换成字符

```sql
select date_format(now(), "%y-%m-%d");
```



### 4.流程控制函数

4.1 if函数：实现if else效果

```sql
select if(expr1, expr2, expr3);
# expr1代表判断条件，expr2代表判断成立的结果，expr3代表判断失败的结果
```

4.2 case函数

* switch case效果

  ```sql
  case 要判断的字段或者表达式
  when 常量1 then 要显示的值1(或语句1;)
  when 常量2 then 要显示的值2(或语句2;)
  ...
  else 要显示的值n或者(语句n;)
  end;
  
  select salary 原始工资, department_id,
  case department_id
  when 30 then salary*1.1
  when 40 then salary*1.2
  when 50 then salary*1.3
  else salary
  end as 新工资
  from employees;
  ```

* 多重if

  ```sql
  case
  when 条件1 then 要显示的值1(或语句1;)
  when 条件2 then 要显示的值2(或语句2;)
  ...
  else 要显示的值n
  end
  
  select salary,
  case 
  when salary > 20000 then 'A'
  when salary > 15000 then 'B'
  when salary > 10000 then 'C'
  else 'D'
  end as 等级
  from employees;
  ```

  

## 分组函数

功能：用作统计使用，又称为聚合函数或统计函数或组函数

分类：sum 求和、avg 平均值、max 最大值、min 最小值、 count 计算个数

特点：

* sum、avg一般用于处理数值型。max、min、count、可以处理任何类型

* 是否忽略null值：sum、avg、max、min、count都忽略null值

* 可以和distinct搭配

  ```sql
  select sum(distinct salary), sum(salary) from employees;
  ```



# 二、分组查询

group by：

```sql
select 分组函数，列（要求出现在group by的后面）
from 表
[where 筛选条件]
group by 分组的列表
[order by 条件]
```

注意：查询列表比较特殊，要求是分组函数和group by后出现的字段

```sql
select max(salary), department_id
from table1
group by department_id;
```



## 分组查询筛选

* 分组前的筛选：一个是根据email查询出后分组

  ```sql
  select count(*), department_id
  from table1
  where email like '%a%'
  group by department_id;
  ```

* 分组后的筛选：另一个是分组后，再根据分组后的个数进行筛选

  ```sql
  select count(*), department_id
  from table1
  group by department_id
  having count(*) > 2;
  ```

区分分组前还是分组后的筛选，要先分组后再查看，如果条件是在表中的字段，就是where，如果不是表中的字段，或者是一个分组，就要用到having

```sql
select min(salary), lingdao_id
from table1
where lingdao_id > 102
group by lingdao_id
having min(salary) > 5000
```



## 总结

1. 分组查询中的筛选条件分为两类

|            | 数据源         | 位置             | 关键字 |
| ---------- | -------------- | ---------------- | ------ |
| 分组前筛选 | 原始表         | group by字句前面 | where  |
| 分组后筛选 | 分组后的结果集 | group by子句后面 | having |

* 分组函数做条件肯定是放在having子句中
* 能用分组前筛选的，就优先考虑使用分组前筛选

2. group by 子句支持单个字段分组，多个字段分组（多个字段之间用逗号隔开没有顺序要求），表达式函数分组（不常用）
3. 也可以添加排序（排序放在整个分组查询的最后）





# 三、连接查询

**含义：**又称之为多表查询，当查询的字段来自于多个表时，就会用到连接查询

**笛卡尔乘积现象：**表1有m行， 表2有n行，结果=m*n行

* 发生原因：没有有效的连接条件

* 如何避免：添加有效的连接条件

## 1.sql92标准

### 1.1 等值连接

```sql
select name, boyName
from boys, beauty
where beauty.boyfriend_id = boys.id;
```

### 1.2 为表起别名

* 提高语句简洁度
* 区分多个重名的字段

注意：如果为表起了别名，则查询的字段就不能使用原来的表明去限定

```sql
select last_name, e.job, job_title
from employees e, jobs j
where e.job_id = j.job_id;
```

### 1.3 两个表的顺序可以调换

### 1.4 可以加筛选

用到and连接

### 1.5 可以加分组

```sql
select count(*) , city
from department d, location l
where d.location_id = l.location_id
group by city;
```

### 1.6 可以加排序

```sql
select job_title, count(*)
from employees e, jobs j
where e.job_id = j.job_id
group by job_title
order by count(*) desc;
```

### 1.7 可以实现三表连接

```sql
select last_name, department_name, city
from employees e, department d, location l
where e.job_id = d.job_id
and d.location_id = j.location_id
and city like '%s'
order by department_name;
```

### 总结：

1. 多表等值连接的结果为多表交集部分
2. n表连接，至少需要n-1个连接条件
3. 多表的顺序没有要求
4. 一般需要为表起别名
5. 可以搭配前面介绍的所有子句使用，比如排序、分组、筛选

