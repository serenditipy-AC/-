
# mysql 

## 通用语法及分类

- DDL: 数据定义语言，用来定义数据库对象（数据库、表、字段）
- DML: 数据操作语言，用来对数据库表中的数据进行增删改
- DQL: 数据查询语言，用来查询数据库中表的记录
- DCL: 数据控制语言，用来创建数据库用户、控制数据库的控制权限

### DDL（数据定义语言）

数据定义语言

#### 数据库操作

查询所有数据库：
`SHOW DATABASES;`
查询当前数据库：
`SELECT DATABASE();`
创建数据库：
`CREATE DATABASE [ IF NOT EXISTS ] 数据库名 [ DEFAULT CHARSET 字符集] [COLLATE 排序规则 ];`
删除数据库：
`DROP DATABASE [ IF EXISTS ] 数据库名;`
使用数据库：
`USE 数据库名;`

##### 注意事项

- UTF8字符集长度为3字节，有些符号占4字节，所以推荐用utf8mb4字符集

#### 表操作

查询当前数据库所有表：
`SHOW TABLES;`
查询表结构：
`DESC 表名;`
查询指定表的建表语句：
`SHOW CREATE TABLE 表名;`

创建表：

```mysql
CREATE TABLE 表名(
	字段1 字段1类型 [COMMENT 字段1注释],
	字段2 字段2类型 [COMMENT 字段2注释],
	字段3 字段3类型 [COMMENT 字段3注释],
	...
	字段n 字段n类型 [COMMENT 字段n注释]
)[ COMMENT 表注释 ];
```

**最后一个字段后面没有逗号**

添加字段：
`ALTER TABLE 表名 ADD 字段名 类型(长度) [COMMENT 注释] [约束];`
例：`ALTER TABLE emp ADD nickname varchar(20) COMMENT '昵称';`

修改数据类型：
`ALTER TABLE 表名 MODIFY 字段名 新数据类型(长度);`
修改字段名和字段类型：
`ALTER TABLE 表名 CHANGE 旧字段名 新字段名 类型(长度) [COMMENT 注释] [约束];`
例：将emp表的nickname字段修改为username，类型为varchar(30)
`ALTER TABLE emp CHANGE nickname username varchar(30) COMMENT '昵称';`

删除字段：
`ALTER TABLE 表名 DROP 字段名;`

修改表名：
`ALTER TABLE 表名 RENAME TO 新表名`

删除表：
`DROP TABLE [IF EXISTS] 表名;`
删除表，并重新创建该表：
`TRUNCATE TABLE 表名;`

### DML（数据操作语言）

#### 添加数据

指定字段：
`INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...);`
全部字段：
`INSERT INTO 表名 VALUES (值1, 值2, ...);`

批量添加数据：
`INSERT INTO 表名 (字段名1, 字段名2, ...) VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);`
`INSERT INTO 表名 VALUES (值1, 值2, ...), (值1, 值2, ...), (值1, 值2, ...);`

##### 注意事项

- 字符串和日期类型数据应该包含在引号中
- 插入的数据大小应该在字段的规定范围内

#### 更新和删除数据

修改数据：
`UPDATE 表名 SET 字段名1 = 值1, 字段名2 = 值2, ... [ WHERE 条件 ];`
例：
`UPDATE emp SET name = 'Jack' WHERE id = 1;`

删除数据：
`DELETE FROM 表名 [ WHERE 条件 ];`

### DQL（数据查询语言）

语法：

```mysql
SELECT
	字段列表                                                                                （5）
FROM
	表名字段                                                                                （1）
WHERE
	条件列表                                                                                （2）
GROUP BY
	分组字段列表                                                                             （3）
HAVING
	分组后的条件列表                                                                          （4）
ORDER BY
	排序字段列表                                                                             （6）
LIMIT
	分页参数                                                                                 （7）
```

#### 基础查询

查询多个字段：
`SELECT 字段1, 字段2, 字段3, ... FROM 表名;`
`SELECT * FROM 表名;`

设置别名：
`SELECT 字段1 [ AS 别名1 ], 字段2 [ AS 别名2 ], 字段3 [ AS 别名3 ], ... FROM 表名;`
`SELECT 字段1 [ 别名1 ], 字段2 [ 别名2 ], 字段3 [ 别名3 ], ... FROM 表名;`

去除重复记录：
`SELECT DISTINCT 字段列表 FROM 表名;`

转义：
`SELECT * FROM 表名 WHERE name LIKE '/_张三' ESCAPE '/'`
/ 之后的\_不作为通配符

#### 条件查询

语法：
`SELECT 字段列表 FROM 表名 WHERE 条件列表;`

条件：	

| 比较运算符          | 功能                                        |
| ------------------- | ------------------------------------------- |
| >                   | 大于                                        |
| >=                  | 大于等于                                    |
| <                   | 小于                                        |
| <=                  | 小于等于                                    |
| =                   | 等于                                        |
| <> 或 !=            | 不等于                                      |
| BETWEEN ... AND ... | 在某个范围内（含最小、最大值）              |
| IN(...)             | 在in之后的列表中的值，多选一                |
| LIKE 占位符         | 模糊匹配（\_匹配单个字符，%匹配任意个字符） |
| IS NULL             | 是NULL                                      |

| 逻辑运算符         | 功能                         |
| ------------------ | ---------------------------- |
| AND 或 &&          | 并且（多个条件同时成立）     |
| OR 或 &#124;&#124; | 或者（多个条件任意一个成立） |
| NOT 或 !           | 非，不是                     |

例子：

```mysql
-- 年龄等于30
select * from employee where age = 30;
-- 年龄小于30
select * from employee where age < 30;
-- 小于等于
select * from employee where age <= 30;
-- 没有身份证
select * from employee where idcard is null or idcard = '';
-- 有身份证
select * from employee where idcard;
select * from employee where idcard is not null;
-- 不等于
select * from employee where age != 30;
-- 年龄在20到30之间
select * from employee where age between 20 and 30;
select * from employee where age >= 20 and age <= 30;
-- 下面语句不报错，但查不到任何信息
select * from employee where age between 30 and 20;
-- 性别为女且年龄小于30
select * from employee where age < 30 and gender = '女';
-- 年龄等于25或30或35
select * from employee where age = 25 or age = 30 or age = 35;
select * from employee where age in (25, 30, 35);
-- 姓名为两个字
select * from employee where name like '__';
-- 身份证最后为X
select * from employee where idcard like '%X';
```

#### 聚合查询（聚合函数）

常见聚合函数：

| 函数  | 功能     |
| ----- | -------- |
| count | 统计数量 |
| max   | 最大值   |
| min   | 最小值   |
| avg   | 平均值   |
| sum   | 求和     |

语法：
`SELECT 聚合函数(字段列表) FROM 表名;`
例：
`SELECT count(id) from employee where workaddress = "广东省";`

#### 分组查询

语法：
`SELECT 字段列表 FROM 表名 [ WHERE 条件 ] GROUP BY 分组字段名 [ HAVING 分组后的过滤条件 ];`

where 和 having 的区别：

- 执行时机不同：where是分组之前进行过滤，不满足where条件不参与分组；having是分组后对结果进行过滤。
- 判断条件不同：where不能对聚合函数进行判断，而having可以。

例子：

```mysql
-- 根据性别分组，统计男性和女性数量（只显示分组数量，不显示哪个是男哪个是女）
select count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性数量
select gender, count(*) from employee group by gender;
-- 根据性别分组，统计男性和女性的平均年龄
select gender, avg(age) from employee group by gender;
-- 年龄小于45，并根据工作地址分组
select workaddress, count(*) from employee where age < 45 group by workaddress;
-- 年龄小于45，并根据工作地址分组，获取员工数量大于等于3的工作地址
select workaddress, count(*) address_count from employee where age < 45 group by workaddress having address_count >= 3;
```

##### 注意事项

- 执行顺序：where > 聚合函数 > having
- 分组之后，查询的字段一般为聚合函数和分组字段，查询其他字段无任何意义

#### 排序查询

语法：
`SELECT 字段列表 FROM 表名 ORDER BY 字段1 排序方式1, 字段2 排序方式2;`

排序方式：

- ASC: 升序（默认）
- DESC: 降序

例子：

```mysql
-- 根据年龄升序排序
SELECT * FROM employee ORDER BY age ASC;
SELECT * FROM employee ORDER BY age;
-- 两字段排序，根据年龄升序排序，入职时间降序排序
SELECT * FROM employee ORDER BY age ASC, entrydate DESC;
```

##### 注意事项

如果是多字段排序，当第一个字段值相同时，才会根据第二个字段进行排序

#### 分页查询

语法：
`SELECT 字段列表 FROM 表名 LIMIT 起始索引, 查询记录数;`

例子：

```mysql
-- 查询第一页数据，展示10条
SELECT * FROM employee LIMIT 0, 10;
-- 查询第二页
SELECT * FROM employee LIMIT 10, 10;
```

##### 注意事项

- 起始索引从0开始，起始索引 = （查询页码 - 1） * 每页显示记录数
- 分页查询是数据库的方言，不同数据库有不同实现，MySQL是LIMIT
- 如果查询的是第一页数据，起始索引可以省略，直接简写 LIMIT 10

#### DQL执行顺序

FROM -> WHERE -> GROUP BY -> SELECT -> ORDER BY -> LIMIT

### DCL

#### 管理用户

查询用户：

```mysql
USE mysql;
SELECT * FROM user;
```

创建用户:
`CREATE USER '用户名'@'主机名' IDENTIFIED BY '密码';`

修改用户密码：
`ALTER USER '用户名'@'主机名' IDENTIFIED WITH mysql_native_password BY '新密码';`

删除用户：
`DROP USER '用户名'@'主机名';`

例子：

```mysql
-- 创建用户test，只能在当前主机localhost访问
create user 'test'@'localhost' identified by '123456';
-- 创建用户test，能在任意主机访问
create user 'test'@'%' identified by '123456';
create user 'test' identified by '123456';
-- 修改密码
alter user 'test'@'localhost' identified with mysql_native_password by '1234';
-- 删除用户
drop user 'test'@'localhost';
```

##### 注意事项

- 主机名可以使用 % 通配

#### 权限控制

常用权限：

| 权限                | 说明               |
| ------------------- | ------------------ |
| ALL, ALL PRIVILEGES | 所有权限           |
| SELECT              | 查询数据           |
| INSERT              | 插入数据           |
| UPDATE              | 修改数据           |
| DELETE              | 删除数据           |
| ALTER               | 修改表             |
| DROP                | 删除数据库/表/视图 |
| CREATE              | 创建数据库/表      |

查询权限：
`SHOW GRANTS FOR '用户名'@'主机名';`

授予权限：
`GRANT 权限列表 ON 数据库名.表名 TO '用户名'@'主机名';`

撤销权限：
`REVOKE 权限列表 ON 数据库名.表名 FROM '用户名'@'主机名';`

##### 注意事项

- 多个权限用逗号分隔
- 授权时，数据库名和表名可以用 * 进行通配，代表所有

# css 

### css简介

​    CSS 是也是一种标记语言
​    CSS 主要用于设置 HTML 页面中的文本内容（字体、大小、对齐方式等）、图片的外形（宽高、边框样式、边距等）以及版面的布局和外观显示样式。
​    CSS 最大价值: 由 HTML 专注去做结构呈现，样式交给 CSS，即 结构 ( HTML ) 与样式( CSS ) 相分离

### css语法规范


​    CSS 规则由两个主要的部分构成：选择器以及一条或多条声明。

![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/css属性规则.png)

 1.选择器是用于指定 CSS 样式的 HTML 标签，花括号内是对该对象设置的具体样式
 2.属性和属性值以“键值对”的形式出现
 3.属性是对指定的对象设置的样式属性，例如字体大小、文本颜色等
 4.属性和属性值之间用英文“:”分开
 5.多个“键值对”之间用英文“;”进行区分
    所有的样式，都包含在 <style> 标签内，表示是样式表。<style> 一般写到 </head> 上方

    1. 样式格式书写
        1.紧凑格式   
            h3 { color: deeppink;font-size: 20px;}
        2.展开格式   
         h3 {
            color: pink;
            font-size: 20px;    
         }
        强烈推荐第二种格式， 因为更直观。

### css选择器的作用

​        答：选择器(选择符)就是根据不同需求把不同的标签选出来这就是选择器的作用。  简单来说，就是选择标签用的。
​    ![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/css属性规则.png)
​        找到所有的 h1 标签。  选择器（选对人）
​        设置这些标签的样式，比如颜色为红色（做对事）。

## css基础选择器

基础选择器又包括：标签选择器、类选择器、id 选择器和通配符选择器

### 标签选择器：

​    标签选择器（元素选择器）是指用 HTML 标签名称作为选择器，按标签名称分类，为页面中某一类标签指定统一的 CSS 样式。
语法：
​    标签选择器{
​        属性：属性值
​        ...
​    }
作用：
​    标签选择器（元素选择器）是指用 HTML 标签名称作为选择器，按标签名称分类，为页面中某一类标签指定统一的 CSS 样式。

### 类选择器

​    如果想要差异化选择不同的标签，单独选一个或者某几个标签，可以使用类选择器.
语法：
​    .类名 {
​        属性1: 属性值1;  
​        ...
​    } 
​    结构需要用class属性来调用  class  类的意思

     <div class="类名"> 变红色 </div>

​    1.如果想要差异化选择不同的标签，单独选一个或者某几个标签，可以使用类选择器。
​    2.类选择器在 HTML 中以 class 属性表示，在 CSS 中，类选择器以一个点“.”号显示。
​    3.类选择器使用“.”（英文点号）进行标识，后面紧跟类名（自定义，我们自己命名的）。
​    4.可以理解为给这个标签起了一个名字，来表示。
​    5.长名称或词组可以使用中横线来为选择器命名。
​    6.不要使用纯数字、中文等命名，尽量使用英文字母来表示。
​    7.命名要有意义，尽量使别人一眼就知道这个类名的目的。
​    8.命名规范：见附件（ Web 前端开发规范手册.doc）

### 多类名选择器

​    我们可以给一个标签指定多个类名，从而达到更多的选择目的。 这些类名都可以选出这个标签.
​    简单理解就是一个标签有多个名字. 

多类名的具体使用：
        1. <div class="red font20">CSS</div>
                注意：
        ​        ​        1.在标签class 属性中写 多个类名
        ​        ​        2.多个类名中间必须用空格分开
        ​        ​        3.这个标签就可以分别具有这些类名的样式

### id选择器：

​    id 选择器可以为标有特定 id 的 HTML 元素指定特定的样式。
​    HTML 元素以 id 属性来设置 id 选择器，CSS 中 id 选择器以“#" 来定义。
​    语法：
​         #id名 {
​            属性1: 属性值1;  
​            ...
​        } 
​        注意：id 属性只能在每个 HTML 文档中出现一次

### id选择器和类选择器的区别：

id选择器只能使用一次，类选择器可以选择多次

### 通配符选择器：

​    语法：
​        * {
​            属性1: 属性值1;  
​            ...
​                }
​                通配符选择器不需要调用， 自动就给所有的元素使用样式
​                特殊情况才使用，后面讲解使用场景(以下是清除所有的元素标签的内外边距,后期讲)
​          * {
​            margin: 0;
​            padding: 0;
​                } 

### 选择器总结

![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/基础选择器总结.png)

## css字体属性:

### 字体大小：

​    CSS 使用 font-size 属性定义字体大小。 
语法：
​     p {  
​        font-size: 20px; 
​    }
​    1.px（像素）大小是我们网页的最常用的单位
​    2.谷歌浏览器默认的文字大小为16px
​    3.不同浏览器可能默认显示的字号大小不一致，我们尽量给一个明确值大小，不要默认大小
​    4.可以给 body 指定整个页面文字的大小

### 字体粗细：

​    CSS 使用 font-weight 属性设置文本字体的粗细。
语法：
​    p {  
​        font-weight: bold; 
​    }

![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/字体粗细.png)

1.学会让加粗标签（比如 h 和 strong 等) 不加粗，或者其他标签加粗
2.实际开发时，我们更喜欢用数字表示粗细

### 字体样式：

​    CSS 使用 font-style 属性设置文本的风格。
语法：
​    p {  
​        font-style: normal;
​    }

![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/文字倾斜.png)

### 字体的综合写法

字体属性可以把以上文字样式综合来写, 这样可以更节约代码:

 body {   font: font-style  font-weight  font-size/line-height  font-family;}

使用 font 属性时，必须按上面语法格式中的顺序书写，不能更换顺序，并且各个属性间以空格隔开 不需要设置的属性可以省略（取默认值），但必须保留 font-size 和 font-family 属性，否则 font 属性将不起作用

### 字体总结：

![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/字体总结.png)

## css文本属性：

### 文本颜色：

    color 属性用于定义文本的颜色。

语法：
    div { 
        color: red;
    }

![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/颜色值.png)

开发中最常用的是十六进制

### 文本对齐：

text-align 属性用于设置元素内文本内容的水平对齐方式。
语法：
    div { 
        text-align: center;
    }

![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/对齐文本.png)

### 修饰文本：

​    语法：
​     div { 
​        text-decoration：underline；
​     }

![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/修饰文本.png)

重点记住如何添加下划线 ? 如何删除下划线 ? 其余了解即可.

### 文本缩进

语法：
    div { 
        text-indent：20px；
    }
    div { 
        text-indent：2em；
    }

![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/文本缩进.png)

    em 是一个相对单位，就是当前元素（font-size) 1 个文字的大小, 如果当前元素没有设置大小，则会按照父元素的 1 个文字大小。

### 行间距：

​    line-height 属性用于设置行间的距离（行高）。可以控制文字行与行之间的距离
语法：
​    p { 
​        line-height: 26px;
​    }
​    行高的文本分为 上间距  文本高度 下间距 = 行间距

## css样式表：

​    按照 CSS 样式书写的位置（或者引入的方式），CSS 样式表可以分为三大类： 

### 行内样式表（行内式）

​        行内样式表（内联样式表）是在元素标签内部的 style 属性中设定 CSS 样式。适合于修改简单样式.
​        语法：

            <div style="color: red; font-size: 12px;">青春不常在，抓紧谈恋爱</div>

​        1.style 其实就是标签的属性
​        在双引号中间，写法要符合 CSS 规范
​        2.可以控制当前的标签设置样式
​        3.由于书写繁琐，并且没有体现出结构与样式相分离的思想，所以不推荐大量使用，只有对当前元素添加简单样式的时候，可以考虑使用
​        4.使用行内样式表设定 CSS，通常也被称为行内式引入

### 内部样式表（嵌入式）

​        内部样式表（内嵌样式表）是写到html页面内部. 是将所有的 CSS 代码抽取出来，单独放到一个 <style> 标签中
​        语法：
​            <style>
​                div {
​                color: red;
​                font-size: 12px;
​                }
​            </style>
​        1.<style> 标签理论上可以放在 HTML 文档的任何地方，但一般会放在文档的<head>标签中
​        2.通过此种方式，可以方便控制当前整个页面中的元素样式设置
​        3.代码结构清晰，但是并没有实现结构与样式完全分离
​        4.使用内部样式表设定 CSS，通常也被称为嵌入式引入，这种方式是我们练习时常用的方式

### 外部样式表（链接式）

​    实际开发都是外部样式表. 适合于样式比较多的情况. 核心是:样式单独写到CSS 文件中，之后把CSS文件引入到 HTML 页面中使用.
​    引入外部样式表分为两步：

    1. 新建一个后缀名为 .css 的样式文件，把所有 CSS 代码都放入此文件中。
        2. 在 HTML 页面中，使用<link> 标签引入这个文件。
        语法：
        <link rel="stylesheet"  href="css文件路径">

![](D:/学习资料/前端资料/pink前端基础带资料/基础部分/03-前端基础CSS第一天/笔记/images/css引入方式总结.png)

## emmet语法

### 1、简介

​		Emmet语法的前身是Zen coding,它使用缩写,来提高html/css的编写速度, Vscode内部已经集成该语法。

​		快速生成HTML结构语法

​		快速生成CSS样式语法

### 2、快速生成HTML结构语法

- 生成标签 直接输入标签名 按tab键即可   比如  div   然后tab 键， 就可以生成 <div></div>
- 如果想要生成多个相同标签  加上 * 就可以了 比如   div*3  就可以快速生成3个div
- 如果有父子级关系的标签，可以用 >  比如   ul > li就可以了
- 如果有兄弟关系的标签，用  +  就可以了 比如 div+p  
- 如果生成带有类名或者id名字的，  直接写  .demo  或者  #two   tab 键就可以了
- 如果生成的div 类名是有顺序的， 可以用 自增符号  $ 
- 如果想要在生成的标签内部写内容可以用  { }  表示

### 3、快速生成CSS样式语法

CSS 基本采取简写形式即可

​		比如 w200   按tab  可以 生成  width: 200px;

​		比如 lh26px   按tab  可以生成  line-height: 26px;

### 4、快速格式化代码

Vscode  快速格式化代码:   shift+alt+f

也可以设置 当我们 保存页面的时候自动格式化代码:

1）文件 ------.>【首选项】---------->【设置】；

2）搜索emmet.include;

3）在settings.json下的【工作区设置】中添加以下语句：

​		"editor.formatOnType": true,

​		"editor.formatOnSave": true

## css的复合选择器

### 1、什么是复合选择器？

​		在 CSS 中，可以根据选择器的类型把选择器分为***基础选择器***和***复合选择器***，复合选择器是建立在基础选择器之上，对基本选择器进行组合形成的。 
​		复合选择器是由两个或多个基础选择器，通过不同的方式组合而成的，可以更准确、更高效的选择目标元素（标签）
​		常用的复合选择器包括：**后代选择器、子选择器、并集选择器、伪类选择器**等等

### 2、后代选择器 (重要）

**定义：**

​		后代选择器又称为包含选择器，可以选择父元素里面子元素。其写法就是把外层标签写在前面，内层标签写在后面，中间用空格分隔。当标签发生嵌套时，内层标签就成为外层标签的后代。

**语法：**

![1570867555401](D:/学习资料/前端资料/pink前端基础带资料/基础部分/04-前端基础CSS第二天/笔记/images/01.png)

​		上述语法表示选择元素 1 里面的所有元素 2 (后代元素)。

**语法说明**：

- 元素1 和 元素2 中间用空格隔开
- 元素1 是父级，元素2 是子级，最终选择的是元素2
- 元素2 可以是儿子，也可以是孙子等，只要是元素1 的后代即可
- 元素1 和 元素2 可以是任意基础选择器

**例子：**

![1570867652832](D:/学习资料/前端资料/pink前端基础带资料/基础部分/04-前端基础CSS第二天/笔记/images/02.png)

### 3、子选择器 (重要）

**定义：**

​		子元素选择器（子选择器）只能选择作为某元素的最近一级子元素。

​		（简单理解就是选亲儿子元素）

**语法：**

<img src="D:/学习资料/前端资料/pink前端基础带资料/基础部分/04-前端基础CSS第二天/笔记/images/03.png"/>

​		上述语法表示选择元素1 里面的所有直接后代(子元素) 元素2。

**语法说明**：

- 元素1 和 元素2 中间用 大于号 隔开
- 元素1 是父级，元素2 是子级，最终选择的是元素2
- 元素2 必须是亲儿子，其孙子、重孙之类都不归他管. 你也可以叫他 亲儿子选择器

**例子：**

![1570867652832](D:/学习资料/前端资料/pink前端基础带资料/基础部分/04-前端基础CSS第二天/笔记/images/04.png)

### 4、并集选择器 (重要）

**定义：**

​		并集选择器可以选择多组标签, 同时为他们定义相同的样式，通常用于集体声明。并集选择器是各选择器通过英文逗号（,）连接而成，任何形式的选择器都可以作为并集选择器的一部分。

**语法：**

<img src="D:/学习资料/前端资料/pink前端基础带资料/基础部分/04-前端基础CSS第二天/笔记/images/05.png"/>

​		上述语法表示选择元素1 和 元素2。

**语法说明**：

- 元素1 和 元素2 中间用逗号隔开
- 逗号可以理解为和的意思
- 并集选择器通常用于集体声明

**例子：**

![1570867652832](D:/学习资料/前端资料/pink前端基础带资料/基础部分/04-前端基础CSS第二天/笔记/images/06.png)

### 5、伪类选择器

**定义：**

​		伪类选择器用于向某些选择器添加特殊的效果，比如给链接添加特殊效果，或选择第1个，第n个元素。

**语法：**

​		伪类选择器书写最大的特点是用冒号（:）表示，比如 :hover 、 :first-child 。



### 6、链接伪类选择器

**定义：**

​		伪类选择器用于向某些选择器添加特殊的效果，比如给链接添加特殊效果，或选择第1个，第n个元素。

**语法：**

​		伪类选择器书写最大的特点是用冒号（:）表示，比如 :hover 、 :first-child 。

​		a:link	没有点击过的(访问过的)链接
​		a:visited	点击过的(访问过的)链接
​		a:hover	鼠标经过的那个链接
​		a:active	鼠标正在按下还没有弹起鼠标的那个链接

**链接伪类选择器注意事项**

​		为了确保生效，请按照 LVHA 的循顺序声明 :link－:visited－:hover－:active。

​		记忆法：love hate 或者 lv 包包 hao 。

​		因为 a 链接在浏览器中具有默认样式，所以我们实际工作中都需要给链接单独指定样式。

**链接伪类选择器实际工作开发中的写法**：

![1570868845786](D:/学习资料/前端资料/pink前端基础带资料/基础部分/04-前端基础CSS第二天/笔记/images/1570868845786.png)



### focus 伪类选择器

**定义：**

​		:focus 伪类选择器用于选取获得焦点的表单元素。

​		焦点就是光标，一般情况 <input> 类表单元素才能获取

例子：

![1570868891669](D:/学习资料/前端资料/pink前端基础带资料/基础部分/04-前端基础CSS第二天/笔记/images/1570868891669.png)



### 复合选择器总结

![1570868930472](D:/学习资料/前端资料/pink前端基础带资料/基础部分/04-前端基础CSS第二天/笔记/images/1570868930472.png)