## MySQL数据类型

### 整数类型

MySQL主要提供的整数类型有TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT，其属性字段可以添加AUTO_INCREMENT自增约束条件。

| 类型名称     | 说明           | 存储需求 |
| ------------ | -------------- | -------- |
| TINYINT      | 很小的整数     | 1个字节  |
| SMALLINT     | 小的整数       | 2个宇节  |
| MEDIUMINT    | 中等大小的整数 | 3个字节  |
| INT(INTEGHR) | 普通大小的整数 | 4个字节  |
| BIGINT       | 大整数         | 8个字节  |

根据占用字节数可以求出每一种数据类型的取值范围。TINYINT需要1个字节(8bit)来存储，那么TINYINT无符号数的最大值为2^8^-1，即255；TINYINT有符号数的最大值为2^7^-1，即127。

| 类型名称     | 说明           | 存储需求 |
| ------------ | -------------- | -------- |
| TINYINT      | -2^7^~2^7^-1   | 0~2^8^   |
| SMALLINT     | -2^15^~2^15^-1 | 0~2^16^  |
| MEDIUMINT    | -2^23^~2^23^-1 | 0~2^24^  |
| INT(INTEGER) | -2^31^~2^31^-1 | 0~2^32^  |
| BIGINT       | -2^63^~2^63^-1 | 0~2^64^  |

不同的整数类型有不同的取值范围，并且需要不同的存储空间，因此应根据实际需要选择最合适的类型，这样有利于提高查询的效率和节省存储空间。

显示宽度和数据类型的取值范围是无关的。显示宽度只是指明MySQL最大可能显示的数字个数，数值的位数小于指定的宽度时会由空格填充。如果插入了大于显示宽度的值，只要该值不超过该类型整数的取值范围，数值依然可以插入，而且能够显示出来。TINYINT(1)和TINYINT(4)都可以存储127。

### 浮点类型

浮点类型有两种，分别是单精度浮点数(FLOAT)和双精度浮点数(DOUBLE)；定点类型只有一种DECIMAL。浮点类型和定点类型都可以用(M,D)来表示，其中M称为精度，表示总共的位数；D称为标度，表示小数的位数。

浮点数类型的取值范围为M(1\~255)和D(1\~30，且不能大于M-2)，分别表示显示宽度和小数位数。M和D在FLOAT和DOUBLE中是可选的，FLOAT和DOUBLE类型将被保存为硬件所支持的最大精度。FLOAT和DOUBLE在不指定精度时，默认会按照实际的精度(由计算机硬件和操作系统决定)。DECIMAL的默认D值为0、M值为10。

DECIMAL类型不同于FLOAT和DOUBLE。DOUBLE实际上是以字符串的形式存放的，DECIMAL可能的最大取值范围与DOUBLE相同，但是有效的取值范围由M和D决定。如果改变M而固定D，则取值范围将随M的变大而变大。

| 类型名称          | 说明             | 存储需求  |
| ----------------- | ---------------- | --------- |
| FLOAT             | 单精度浮点数     | 4个字节   |
| DOUBLE            | 双精度浮点数     | 8个字节   |
| DECIMAL(M,D)，DEC | 压缩的严格定点数 | M+2个字节 |

DECIMAL的存储空间并不是固定的，而由精度值M决定，占用M+2个字节。

FLOAT类型的取值范围：

- 有符号：-3.402823466E+38~1.175494351E-38。
- 无符号：0和-1.175494351E-38~3.402823466E+38。

DOUBLE类型的取值范围：

- 有符号：-1.7976931348623157E+308~2.2250738585072014E-308。
- 无符号：0和-2.2250738585072014E-308~1.7976931348623157E+308。

不论是定点还是浮点类型，如果用户指定的精度超出精度范围，则会四舍五入进行处理。浮点数相对于定点数的优点是在长度一定的情况下，浮点数能够表示更大的范围；缺点是会引起精度问题。

在MySQL中，定点数以字符串形式存储，在对精度要求比较高的时候(如货币、科学数据)，使用DECIMAL的类型比较好，另外两个浮点数进行减法和比较运算时也容易出问题，所以在使用浮点数时需要注意，并尽量避免做浮点数比较。

### 日期和时间类型

MySQL中有多处表示日期的数据类型：YEAR、TIME、DATE、DTAETIME、TIMESTAMP。每一个类型都有合法的取值范围，当指定确定不合法的值时，系统将零值插入数据库中。

| 类型名称  | 日期格式           | 日期范围                                        | 存储需求 |
| --------- | ------------------ | ----------------------------------------------- | -------- |
| YEAR      | YYYY               | 1901~2155                                       | 1个字节  |
| TIME      | HH:MM:SS           | -838:59:59~838:59:59                            | 3个字节  |
| DATE      | YYYY-MM-DD         | 1000-01-01~9999-12-3                            | 3个字节  |
| DATETIME  | YYYY-MM-DDHH:MM:SS | 1000-01-01 00:00:00~9999-12-31 23:59:59         | 8个字节  |
| TIMESTAMP | YYYY-MM-DDHH:MM:SS | 1980-01-01 00:00:01 UTC~2040-01-19 03:14:07 UTC | 4个字节  |

##### YEAR类型

- 以4位字符串或者4位数字格式表示的YEAR，范围为'1901'~'2155'。
- 以2位字符串格式表示的YEAR，范围为'00'到'99'。'00'\~'69'和'70'\~'99'范围的值分别被转换为2000\~2069和1970\~1999范围的YEAR值。'0'与'00'的作用相同。插入超过取值范围的值将被转换为2000。
- 以2位数字表示的YEAR，范围为1\~99。1\~99和70\~99范围的值分别被转换为2001\~2069和1970\~1999范围的YEAR值。注意，在这里0值将被转换为0000，而不是2000。
- 非法YEAR值将被转换为0000。

##### TIME类型

- 如果没有冒号，假定最右边的两位表示秒，1112表示00:11:12。
- 如果使用冒号，则被看作当天的时间，11:12表示11:12:00。

##### DATE类型

- 以'YYYY-MM-DD'或者'YYYYMMDD'字符中格式表示的日期，取值范围为'1000-01-01'\~'9999-12-3'。
- 以'YY-MM-DD'或者'YYMMDD'字符串格式表示日期，在这里YY表示两位的年值。MySQL解释两位年值的规则：'00\~69'范围的年值转换为'2000\~2069'，'70\~99'范围的年值转换为'1970\~1999'。
- 以YYMMDD数字格式表示的日期，与前面相似，00\~69范围的年值转换为2000\~2069，80\~99范围的年值转换为1980\~1999。
- 使用CURRENT_DATE或者NOW()，插入当前系统日期。
- MySQL允许不严格语法：任何标点符号都可以用作日期部分之间的间隔符。例如，'98-11-31'、'98.11.31'、'98/11/31'和'98@11@31'是等价的，这些值也可以正确地插入数据库。

##### DATETIME类型

- 以'YYYY-MM-DDHH:MM:SS'或者'YYYYMMDDHHMMSS'字符串格式表示的日期，取值范围为'1000-01-01 00:00:00'-'9999-12-3 23:59:59'。
- 以'YY-MM-DD HH:MM:SS'或者'YYMMDDHHMMSS'字符串格式表示的日期，在这里YY表示两位的年值。与前面相同，'00\~79'范围的年值转换为'2000\~2079'，'80\~99'范围的年值转换为'1980\~1999'。
- 以YYYYMMDDHHMMSS或者YYMMDDHHMMSS数字格式表示的日期和时间。
- MySQL允许不严格语法:任何标点符号都可用作日期部分或时间部分之间的间隔符。例如，'98-12-3111:30:45'、'98.12.3111+30+35'、'98/12/3111\*30\*45'和'98@12@3111\^30\^45'是等价的，这些值都可以正确地插入数据库。

##### TIMESTAMP类型

TIMESTAMP的显示格式与DATETIME相同，显示宽度固定在19个字符

> 协调世界时(英：Coordinated Universal Time，法：Temps Universel Coordonné)又称为世界统一时间、世界标准时间、国际协调时间。英文(CUT)和法文(TUC)的缩写不同，作为妥协，简称UTC。

DATETIME在存储日期数据时，按实际输入的格式存储，即输入什么就存储什么，与时区无关；而TIMESTAMP值的存储是以UTC(世界标准时间)格式保存的，存储时对当前时区进行转换，检索时再转换回当前时区。即查询时，根据当前时区的不同，显示的时间值是不同的。

### 字符串类型

MySQL中的字符串类型有CHAR、VARCHAR、TINYTEXT、TEXT、MEDIUMTEXT、LONGTEXT、ENUM、SET等。

字符串类型用来存储字符串数据，还可以存储图片和声音的二进制数据。字符串可以区分或者不区分大小写的串比较，还可以进行正则表达式的匹配查找。

| 类型名称   | 说明                                        | 存储需求                                                |
| ---------- | ------------------------------------------- | ------------------------------------------------------- |
| CHAR(M)    | 固定长度非二进制字符串                      | M字节，1<=M<=255                                        |
| VARCHAR(M) | 变长非二进制字符串                          | L+1字节，在此，L<=M和1<=M<=255                          |
| TINYTEXT   | 非常小的非二进制字符串                      | L+1字节，在此，L<2^8^                                   |
| TEXT       | 小的非二进制字符串                          | L+2字节，在此，L<2^16^                                  |
| MEDIUMTEXT | 中等大小的非二进制字符串                    | L+3字节，在此，L<2^24^                                  |
| LONGTEXT   | 大的非二进制字符串                          | L+4字节，在此，L<2^32^                                  |
| ENUM       | 枚举类型，只能有一个枚举字符串值            | 1或2个字节，取决于枚举值的数目(最大值为65535)           |
| SET        | 一个设置，字符串对象可以有零个或多个SET成员 | 1、2、3、4或8个字节，取决于集合成员的数量(最多64个成员) |

括号中的M表示可以为其指定长度。VARCHAR和TEXT类型是变长类型，其存储需求取决于列值的实际长度(在前面的表格中用L表示)，而不是取决于类型的最大可能尺寸。

##### CHAR和VARCHAR类型

CHAR(M)为固定长度字符串，在定义时指定字符串列长。当保存时，在右侧填充空格以达到指定的长度。M表示列的长度，范围是0~255个字符。检索CHAR值时，尾部的空格将被删除。

VARCHAR(M)是长度可变的字符串，M表示最大列的长度，M的范围是0~65535。VARCHAR的最大实际长度由最长的行的大小和使用的字符集确定，而实际占用的空间为字符串的实际长度加1(一个字符串结束字符)。VARCHAR在值保存和检索时尾部的空格仍保留。

CHAR是固定长度，所以它的处理速度比VARCHAR的速度要快，但是它的缺点就是浪费存储空间。所以对存储不大，但在速度上有要求的可以使用CHAR类型，反之可以使用VARCHAR类型来实现。

存储引擎对于选择CHAR和VARCHAR的影响：

- 对于MyISAM存储引擎，最好使用固定长度的数据列代替可变长度的数据列。这样可以使整个表静态化，从而使数据检索更快，用空间换时间。
- 对于InnoDB存储引擎，最好使用可变长度的数据列，因为InnoDB数据表的存储格式不分固定长度和可变长度，因此使用CHAR不一定比使用VARCHAR更好，但由于VARCHAR是按照实际的长度存储，比较节省空间，所以对磁盘I/O和数据存储总量比较好。

##### TEXT类型

TEXT列保存非二进制字符串，如文章内容、评论等。当保存或查询TEXT列的值时，不删除尾部空格。

##### ENUM类型

```
<字段名>ENUM('值1','值1',…,'值n')
```

ENUM是一个字符串对象，值为表创建时列规定中枚举的一列值。字段名指将要定义的字段，值n指枚举列表中第n个值。ENUM类型的字段在取值时，能在指定的枚举列表中获取，而且一次只能取一个。如果创建的成员中有空格，尾部的空格将自动被删除。

ENUM值在内部用整数表示，每个枚举值均有一个索引值；列表值所允许的成员值从1开始编号，MySQL存储的就是这个索引编号，枚举最多可以有65535个元素。ENUM值依照列索引顺序排列，并且空字符串排在非空字符串前，NULL值排在其他所有枚举值前。

ENUM列总有一个默认值。如果将ENUM列声明为NULL，NULL值则为该列的一个有效值，并且默认值为NULL。如果ENUM列被声明为NOTNULL，其默认值为允许的值列表的第1个元素。

##### SET类型

```
SET('值1','值2',…,'值n')
```

SET是一个字符串的对象，可以有零或多个值，SET列最多可以有64个成员，值为表创建时规定的一列值。指定包括多个SET成员的SET列值时，各成员之间用逗号`,`隔开。SET类型的列可从定义的列值中选择多个字符的联合。当创建表时，SET成员值的尾部空格将自动删除。

与ENUM类型相同，SET值在内部用整数表示，列表中每个值都有一个索引编号。

如果插入SET字段中的列值有重复，则MySQL自动删除重复的值；插入SET字段的值的顺序并不重要，MySQL会在存入数据库时，按照定义的顺序显示；如果插入了不正确的值，默认情况下，MySQL将忽视这些值，给出警告。

### 二进制类型

MySQL中的二进制字符串有BIT、BINARY、VARBINARY、TINYBLOB、BLOB、MEDIUMBLOB和LONGBLOB。

| 类型名称      | 说明                 | 存储需求               |
| ------------- | -------------------- | ---------------------- |
| BIT(M)        | 位字段类型           | 大约(M+7)/8字节        |
| BINARY(M)     | 固定长度二进制字符串 | M字节                  |
| VARBINARY(M)  | 可变长度二进制字符串 | M+1字节                |
| TINYBLOB(M)   | 非常小的BLOB         | L+1字节，在此，L<2^8^  |
| BLOB(M)       | 小BLOB               | L+2字节，在此，L<2^16^ |
| MEDIUMBLOB(M) | 中等大小的BLOB       | L+3字节，在此，L<2^24^ |
| LONGBLOB(M)   | 非常大的BLOB         | L+4字节，在此，L<2^32^ |

括号中的M表示可以为其指定长度。

##### BIT类型

位字段类型。M表示每个值的位数，范围为1~64。如果M被省略，默认值为1。如果为BIT(M)列分配的值的长度小于M位，在值的左边用0填充。默认情况下，MySQL不可以插入超出该列允许范围的值，因而插入数据时要确保插入的值在指定的范围内。

##### BINARY和VARBINARY类型

BINARY和VARBINARY类型类似于CHAR和VARCHAR，不同的是它们包含二进制字节字符串。

BINARY(M)类型的长度是固定的，指定长度后，不足最大长度的，将在它们右边填充`\0`补齐，以达到指定长度。无论存储的内容是否达到指定的长度，存储空间均为指定的值M。

VARBINARY(M)类型的长度是可变的，指定好长度之后，长度可以在0到最大值之间。实际占用的空间为字符串的实际长度加1。

##### BLOB类型

BLOB是一个二进制的对象，用来存储可变数量的数据。BLOB列存储的是二进制字符串(字节字符串)，TEXT列存储的是非进制字符串(字符字符串)。BLOB列是字符集，并且排序和比较基于列值字节的数值；TEXT列有一个字符集，并且根据字符集对值进行排序和比较。

### 转义字符

| 转义字符 | 转义后的字符   |
| -------- | -------------- |
| \"       | 双引号（"）    |
| \'       | 单引号（'）    |
| \\       | 反斜线（\）    |
| \n       | 换行符         |
| \r       | 回车符         |
| \t       | 制表符         |
| \0       | ASCII 0（NUL） |
| \b       | 退格符         |

一个字符串用双引号"引用时，该字符串中的单引号'不需要特殊对待，且不必被重复转义。同理，一个字符串用单引号'引用时，该字符串中的双引号"不需要特殊对待，且不必被重复转义。