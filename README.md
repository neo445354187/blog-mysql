# MySQL

## 常规知识

1. 数据库系统（DataBase System）=数据库管理系统（DBMS）+数据库（DataBase）
数据库：对大量信息进行管理的高效解决方法，按照数据结构来组织，存储和管理数据的库；
表table：数据是保存在表内，保存在一个表内的的数据，应该具有相同的数据结构；
行&列：行用于记录数据，列用于规定数据格式；
记录：行内的数据；
字段：数据的某个列；
SQL：数据管理系统，用来管理数据的语言，结构化查询语言；

2. 
a 打开服务查看进程：`CMD--services.msc `
b 启动或停止进程：`CMD--net start mysql 或 CMD--net stop mysql`
`mysqld --defaults-file=d:\wamp\bin\mysql\mysql5.6.12\bin\···`
c 连接服务器
`mysql -hlocalhost -P3306 -uroot -p`
`Enter password :********`
备注：以上有些代码没搞懂

重点：在本机的安装wamp环境，可以在CMD中输入
`d:\wamp\bin\mysql\mysql5.6.12\bin\mysql.exe`    //能进入但是不能操作
就可以运行MySQL程序（猜测：应该是客户端）；

重点2：直接点击wamp ，选择MySQL ，点击MySQL console；

3. SQL （结构化查询语言，Structured Query Language）
根据操作对象不同，分为不同的语言
a 数据操作（管理）语言
   查询，获得数据   |   管理，增加，删除，修改数据；
b 数据定义语言（保存数据的格式进行定义）
c 数据控制语言（针对数据库软件服务进行操作）

4. 语法 (编辑代码，复制粘贴进去)
a 创建数据库    `Create database db_name[数据库选项]`;	//要求有语句结束符分号

备注：标识符（数据库名）命名规则：大小写取决于当前操作系统，windows不区分，Linux区分，那直接采用区分的，推荐使用下划线；标识符可以使用任意字符，数字、符号，甚至中文，但是一些特殊的组合：纯数字组合，特殊符号，包括MySQL内部关键字应该用标识符限定符来包裹，限定符为反引号\`\`
上面代码作用：在MySQL数据目录下，形成一个目录，目录名时数据库名(特殊字符，则使用编码的形式保存)，目录内存在一个文件，用于保存 [数据库选项]  信息（默认保存为Db.opt）;
例：`create database db_name character set utf8;`

b 数据查询
b.1 查看当前存在的数据库		`show databases;`
b.2 查看数据库的创建语句（如属性）	`show create database db_name;`
b.3 数据库删除			`Drop database db_name;` (删除后不可恢复)
b.4 修改数据库信息			`Alter database db_name [修改指令];`
例：`alter database php_1 character set gbk;    `//就把默认值为utf-8的属性给修改掉了；
b.5 数据库更名方式：
	b.5.1 简单的可以直接修改目录名；
	b.5.2 将数据库内容全部导出，新建一个数据库，将内容导入，删除旧数据库；(rename 修改数据库名有数据丢失风险，官方建议不使用，但是用rename修改表名完全可以)
X 表类型为MYISAM引擎把库名字对应的文件夹名改了
X.1、关闭mysqld
X.2、把data目录中的db_name目录重命名为new_db_name
X.3、打开mysqld

Z 表类型为INNODB引擎分为下面几个步骤：
Z.1 按新名字建立一个数据库
Z.2 删除原有库中所有表上的触发器
Z.3 使用rename table命令将表从原数据库复制到新数据库
Z.4 在新数据库上重新创建2.2中被删除的触发器
Z.5 在新数据库上重新创建存储过程、自定义函数、Events等
```
RENAME TABLE命令语法：
RENAME TABLE db_name.table1 TO new_db_name.table1,
                     db_name.table2 TO new_db_name.table2;
```
备注：上面大写部分为语法；



5. 表操作

数据库是表的容器；表必须属于某个数据库，进行表操作时，都会指定当前的默认数据库；
A 
a  没有指定默认数据情况下：
	可以通过语法.（即点）来强制指明数据表所属的数据库；如：库.表 db_name.tbl_name;库和表都出现特殊标识符，需要用反引号一个一个的包裹，提示：创建时名字必须（是必须）用到反引号的，那么修改，删除时，名字也一定要用反引号，如果创建时不是必须要用反引号，那么后面用不用反引号都可以操作；

b 指定默认数据库
	Use db_name;只是设定了默认数据库，不会影响其他数据库，即也可以强制操作其他库；

B 语法
a 创建表 		`create table tbl_name(列结构)[表选项]; `  //一定要用括号包裹列结构，即使只有一列；
备注：列结构（定义)：列名	列的数据类型      [列的属性(约束)]
简单的字符串类型	`varchar()`			//括号中是设置最大的字符长度
日期类型		data	
先分析，需要保存的实体信息，拥有哪些属性。这些属性应该如何保存；列属性：zerofill 、`not null`;
例1：
```
create table php_1.table_1 (
班级 varchar(20),
学号 date
);
```
例2：
```
create database php_2;	
use php_2;		//指定默认数据库
create table php_2.table_1 (
班级 varchar(20),
学号 date
)engine=innodb charset=utf8;
```
b 查看表
b.1 显示全部表		`show tables [like 'pattern']; `
其中`like pattern` 部分表示
例：`show tables like 'exam_%'`;   //   %表示任意字符的任意个数（通配符 ），显示数据库也适用；

b.2 查询某个表的创建信息	`show create table tb_name;`
备注：如果表的内容过多，结构显示混乱，可以使用 \G作为语句结束符代替分号；

b.3 查看表结构（描述表结构） 	`Describe tbl_name;` (describe 可以简写 desc)
备注：注意上面代码中没有table 

c 删除表			`drop table tbl_name;`
备注：如果表存在就删除，其实不存在，删毛线啊（数据库也是），也可以采用：
	`drop table [if exists] tbl_name;`
例：`drop table if exists php_1; `	
备注：这样数据库就不会报错了；
另外：删除文件有些系统为了数据安全，不支持删除表，则要先在data中把表文件删除，然后再去删除表；

d 修改
d.1 修改表名	`rename table old_tbl_name to new_tbl_name;`	//数据库没有rename
说明：这个语法可以同时重名多个表；
例：`rename table old_tbl_name_1 to new_tbl_name_1,old_tbl_name_2 to new_tbl_name_2;`
说明2：重命名操作是移动（即，原来的表不在了）；
且支持跨数据库重命名（即新名字前面数据库名加点），这一步可以变相用于数据库重命名，创建一个新的数据库，将旧的数据库内的表，通过rename跨数据库操作，移动到新的数据库内，然后删除旧的数据库；

d.2 修改列定义 （操作的对象是字段，即列）	
修改表结构，上面的是子命令，上级是，	`alter table tbl_name [add | drop | change | modify]`
增加一个新列	Add 
例：`alter table exam_students add height int;` ?多个》
修改一个列定义	Modify
例：`alter table exam_students modify student_num varchar(40);`
重命名一个列	Change
例：`alter table exam_students change old_column_name new_column_name int;`
删除一个列	Drop
例：`alter table exam_students drop height;`

d.3 修改表选项	Alter table tbl_name 新的表选项；
例：`alter table exam_student charater set utf8;`


6. 数据操作 （增-删-改-查 操作对象是记录，即行）
A 创建数据（插入数据）	`insert into tbl_name (字段列表) values (值列表);`
例：`insert into student (name,stu_num) values (`xiaowang`,`php@2009`);`
备注：如果在插入时，为所有的字段设置值，那么可以省略字段列表，要求是值得顺序应该与表中字段的顺序一致；

B 获得数据（查询数据）	select 字段列表 from 表名 [ 查询条件 ];
例：`select * from student;`
`select name,stu_num from student where 1;`
`select * from student where score>=60;`
备注：字段列表可以使用 * 代替，表示所有的条件，查询条件可以省略，表示所有记录都要获得，相当于 where 1;

C 删除数据		`delete from 表名 条件; `
备注：本来条件语法是可选的，但是最好采用都写上条件，如果要删除所有数据，那么可以给条件where 1；
例：`delete from student where score<=60;`

D 修改数据		`update 表名 set 字段1=新值1,字段2=新值2,··· 条件;`
例：uptate student set score=100 where score>60;
备注：语法上条件可选，但是也采用必须写条件；
总体：语法中表格前面没有声明table了；

额外知识：设置varchar类型数据后，还只能是数字，不能用反引号；

7.
A 编码
```
character_set_database  	gbk	当前默认数据库编码
character_set_server		utf8	服务器编码（在my.ini中可以设置）
character_set_system		标识符，字段或者表名的编码
```
备注：还有更多编码用：`show variables like 'character_set%';`    	//这儿是单引号，不是反引号；

B 校对规则：它是一组规则，负责决定某一字符集下的字符进行比较和排序的结果。
每个字符集都支持不定数量的校对规则，可以通过如下指令：
例1：`show collation;`	//指令来查看数据库支持的校对规则。
例2：`show collation like 'utf8%';` 	//是单引号，不是反引号哦；
可以看到	字符集_地区名_比较规则
例：`utf8_roman_ci`
ci 	不区分大小写
cs	区分大小写
bin	字节比较

在设置字符集的时候，可以设置当前字符集所采用的校对规则，如果不设置校对规则，那么每一个字符集都有一个默认的校对规则；
字符集：影响数据如果保存；
校对集：影响数据排序关系；（gbk 可以按照中文的拼音排序）
utf8  中文占3个字节；（采用）
gbk  中文占2个字节；
例：
```
create table table_1(
`学号` varchar(10)
) character set gbk collate gbk_bin;
```
Order by 关键字，可以在获得数据时，将数据进行按照某个字段的排序；
例：`select * from tbl3 order by name;`

ORDER BY  后可加2个字段，用英文逗号隔开。
f1用升序， f2降序，sql该这样写
ORDER BY  f1, f2  DESC
也可以这样写，更清楚：
ORDER BY  f1 ASC, f2  DESC
如果都用降序，必须用两个desc
ORDER BY  f1 DESC, f2 DESC


8. 数据类型
A 整型
a 
```
   类型	 字节		最小值		            最大值
TINYINT	    1		-128/0		             127/255
SMALLINT	2		-32768/0		         32767/65535
MEDIUMINT   3		-8399608/0	             8388607/16777215
INT/INTEGE  4		-2147483648/0	         2147483647/4294967295
BIGINT	    8 		-9223372036854775808/0	
```
备注：如果只写TINYINT等，默认是有符号的，如果要求无符号，可以用unsigned控制只为正；
可以使用zerofill来进行前导零填充；
布尔bool类型，就是tinyint(1)的别名；
例：
```
create table table_1 (
a tinyint unsigned,
b tinyint
);
```
b 定义显示宽度：通过规定数据的显示宽度，达到统一显示的目的；配合zerofill完成，即如果数据没有达到规定最小宽度，则前面补0，如果达到或者超出最小宽度，按原样显示；
语法：类型 (M);	M就是显示最小宽度；
例：
`alter table table_1 add c tinyint(2) zerofill;`
备注：不影响数据范围；宽度大的数据不影响，不会截取；

B 小数类型；、
a 浮点：小数位数可以变化；
float	单精度	38次幂		但精度大概只有6位，其他都是估值；
double 	双精度       308次幂		但精度只有16为，其他都是估值；
支持：控制数值的范围。Type(M,D)  M表示数的总位数（不包括小数点和符号），D表示数的小数位数；这样规定后说明，小数部分位数最多为D位，整数部分位数最多为M-D位，两者必须同时符合才能通过；
备注：这个规定了数值范围，与整型的类型（M）不同；
例：
```
create table table_2(
a float(5,2),
b double(8,3)
);
insert into table_2 values (0.23E3,456.7E2);
```
b 定点数：`decimal(M,D)` M 是总位数，D 小数位数。M默认为10，D默认为0；（占用空间大）
作用：不会出现数的精度丢失；且也支持zerofill，unsigned(不过这个很少采用);

总结:整型 类型（M）与小数 类型（M，D）在作用上的不同：
a 前者规定最小显示宽度（不影响范围），不够可以配合zerofill补0，后者规定整数和小数最大位数（即数值范围），位数不够可以通过zerofill补0；


C 时间和日期类型
```
类型				存储空间	范围
Datetime 		年月日时分秒	8	1000-01-01 00:00:00到9999-12-31 23:59:
Date		年月日		3	1000      到 9999
Timestamp	时间戳		4	1970       到2038
Time		时间		3	-838:59:59到838:59:59
YEAR		年		1	1901到2155 
```
备注：timestamp 时间戳内部存储时是整型，但是表示时是日期时间（显示模式与datatime一样）    ， 时间戳最长到达2037年左右溢出；

例：
```
create table dt_1(
a datatime,
b timestamp
);
insert into dt_1 values ('2015-2-18 11:11:11','2015-02-18 11:11:11')；//时间 以字符串表示；
```
`select a,b+0 from dt_1;`		//检索时+0，可以获取没有分隔符格式；
备注：其实时间分隔符可以任意(但不建议用)，甚至可以没有；如：
`insert into dt_1 values ('2015:2:18 11:11:11','20150218 11:11:11');`
备注：
a  如果年是2位表示，则是70~69 即1970~2069；（不建议使用）
b 支持0值，表示当前没有规定，如 2013-04-0  表示4月整个月；

c Time类型 	表示意义：c.1一天中的时间；c.2 表示时间间隔，在表示间隔时，可以使用天来表示；
格式：D `HH:MM:SS`		//其中D 代表天数；
例：
```
insert into t_1 values ('122211');
insert into t_1 values ('12:22:11');
insert into t_1 values ('5 12:22:11');
```
备注：站在MySQL上，用以上时间类型，站在php程序员和项目上，通常保存时间是unix的时间戳（整型），即php中的时间戳；


D 字符串类型
类型	最大长度		备注
char	255（个字节）	char(M)  M表示允许的严格限定的字符串长度（即分配的储存空间
			已经固定
varchar	65535（字节）	varchar(M) M表示允许的最大长度（即分配的最大储存空间，如果小于分配空间，那么可以自动节省空间）；

备注：varchar的字节65535中如果字节大于了255，则需要两个字节保存字节长度，还有一个字节预留空字符串，varchar真实65532，当然可以通过设置not null消除预留字节，变成65533；例如 varchar类型的'abc'，占用的长度4；
M表示的是字符数，而不是字节数，但是总的长度是按照字节计算的
ps：5.7版本，varchar(255)可以保存254个汉字或者其他字符（ps：第一个字节用于保存长度）；
    varchar在utf-8中最多可以保存21800多（ps：具体数字没测试）个汉字
```
create table s_3(
a char(5),		//即使最终赋值为'z',也是固定分配储存空间5个字节；
b varchar(5),	//最大分配5个字节，如果最终赋值为'abc' ,则占用空间为4个字节；
c varchar(65533) not null
) character set latin1;
```
备注：一条记录的总长度也限制了必须小于等于65535；

text	65535		全部字节都可以用来保存数据；还有longtext 等等类型；
备注：相对于varchar，text的长度不用统计到记录的总长度中；
兄弟类型：Tinytext	longtext 表示字符串长度不一样；
例：
```
create table s_5(
a text,
b text
) character set latin1;
```

enum 	1,2 枚举选项量（65535）	单选选择
set	1、2、4、8、16元素数量64	可以不选，可以选任意多个；
例：
```
create table s_6(
a enum('female','male'),	//内部存储'female','male'对应着1,2；
) ;
```
例2：
```
create table s_8(
hobby set('a','b','c','d','e','f','g')
) ;
insert into s_6 values ('d,e'); 	//选择多个值在一个引号里；
select hobby+0 from s_8;	//结果显示：24（即状态，且不重复）
```
Binary,varbinary,blob		Binary(char),varbinary(varchar),blob(text)对比	二进制数据


9. 定义列属性（列约束）
A 是否为空	规定一个字段的值，是否可以为null；值NULL(默认)    |  NOT NULL
例：
```
create table php3(
a int not null,		//不能为空；
b int 
);
```

B 默认值属性
```
create table php3(
a int not null default 20,		
b int not null default 10
);
```
备注：没有给出值，才会被默认值填充，给出值为NULL，值就是null，不会是默认值；	
默认值的设置需要使用固定值；


C 主键 | 唯一索引
主键（primary key）：可以唯一标识某条记录的字段或者是字段的集合(即某个字段不能唯一标识，把多个字段综合起来标识)；
主键不建议采用实体真实的属性；作用：便于查找；
a 字段上设置；
例：
```
create table teacher(
t_id int primary key,
t_name varchar(5)
);
```
备注：主键值必须唯一，主键值不能为NULL，但类型可以为负，那么负数和零可以作为主键；
额外知识：设置字符集，`set names gbk;`

b 在定义完字段后再用 primary key (字段名)，相对于前者，这个优势是可以用字段集合作为主键；
例：
```
create table teacher1(
t_id int ,
t_name varchar(5),
primary key (t_id)
);
```
例2：
```
create table teacher1(
t_id int primary key,
t_name varchar(5),
primary key (t_id,t_name)
);
```
备注：一个主键包含了两个字段，不是两个字段都是主键，规定：一个表只有一个主键；


D 自动增长 ：为每条记录提供给一个唯一的标识；每次插入记录时，将某个字段的值自动增加1；
	使用auto_increment 标识； 需要整型，还需要有索引；插入数据时，可以选择插入null（采用）或者不插入（不插入时需要values前面有列名列表，才能对应值）；
例：
```
create table teacher4(
t_id int primary key auto_increment,
t_name varchar(5)
);
insert into teacher4 values (null,'1234');
```
备注：上例就可以自动增长；如果不插入null，那么采用：
```
insert into teacher4 (t_name) values ('1234');
```
备注：auto_increment默认初始值为1，设置初始值，可以通过更改表选项完成，如：
`alter table teacher4 auto_increment 10;`	//设置10为自动增长初始值；
备注：如果设置的初始值小于已经有的主键值，这样设置时无效的；
手动插入主键值，如果主键值不存在，那么可以插入；
主键列中的主键值是可以更新的（即更改，用update ）；
还有如：主键值为20、21、22，三条记录被删除后，在增加记录，主键值默认为23；
总结：主键最主要属性唯一性；自动增长属性初始值设置不能小于已有的主键值；


E 外键属性（约束） foreign key:如果一个实体（如：student）的某个字段(student:class_id)指向（引用）另一个实体(class)的主键(class:class_id),就称student实体的class_id是外键；
主实体（父实体，父表）：被指向实体
从实体（子实体，从表）：负责指向的实体；
作用：用于约束于关系内的实体，增加子表记录时，是否有与之对应的父表记录；在删除或者更新主表记录时，从表应该如果处理相关记录，即级联操作；

定义一个外键：在从表上，增加一个外键字段，指向主表的主键，使用关键字foreign key;
语法：`foreign key (外键字段) references  主表名 (关联字段) [主表记录删除时的动作] [主表记录更新时的动作]`
例：
```
drop table if exists class;	//如果存在表class，则删除它
create table class(
class_id int primary key auto_increment comment '班级id',
class_name varchar(10) not null default 'php' comment '班级名称'
) character set utf8;
```
//上面utf8编码数据存储到服务器上的编码；而代码：`set names gbk;` //这个作用是客户端与服务器通信的编码；
```
drop table if exists student;
create table student(
s_id int primary key auto_increment,
s_name varchar(10) not null default '',
class_id int,
foreign key (class_id) references class (class_id)
) character set utf8;
```
设置级联操作：主表数据发生改变时，与之关联的从表数据应该如何处理；

a 主表更新：使用关键字 on update	指的是主表的主键值发生变化时，执行什么级联操作；

b 主表删除：使用关键字 on delete 
以上两个关键字可以同时存在
允许的级联动作：
cascade 关联操作，如果主表被更新或删除，那么从表也会执行相应的操作；
set null  设置为null ，表示从表不指向任何主表记录，条件外键字段声明列属性时不能为空；
restrict  拒绝主表的相关操作；（级联操作默认动作）
以上三个关键字在on update 或者on delete 关键字后不允许同时存在；

修改外键：先删除外键，再新建外键，通过修改表完成；
`alter table tbl_name drop foreign key 外键名;`
删除外键需要通过制定外键名称达到目的，外键名需要用show create table 从表名;显示出来的CONSTRAINT 后面单引号中代码就是产生的默认外键名；
删除的是列约束，记录还在；
可以通过创建外键时，指定名称，或者通过使用MySQL默认生成的名称； 
`alter table tbl_name add foreign key (外键字段) references  主表名 (关联字段) [主表记录删除时的动作] [主表记录更新时的动作];`
例：
```
alter table student drop foreign key student_ibfk_1;
alter table student add foreign key (class_id) references class (class_id) on delete cascade;
```
备注：一般项目不需要用到外键；php程序一般不用外键；


F 注释 comment：可以针对每个字段或者每个表进行注释，可以让我们看到相关信息；



10. 实体之间的关系
A 一 一对应（对象为记录）
两个实体表内，存在相同的主键字段，如果记录的主键值等于另一个关系表内的记录的主键值，则两条记录一一对应（垂直分隔）；

B 一对多（对象为记录）
一个实体，对应多个其他实体；例如：一个班级对应多个学生；
设计：在多的实体表那端，增加一个字段，用于指向该实体所属的另外的实体的标识（即主键）；

C 多对多 （对象为记录） 看为两个一对多；
设计：典型的，利用一个中间表（表中两字段分别是操作两个表的主键字段，两个字段共同组成主键），表示实体之间的对应关系；中间表的每个记录，表示一个关系；

总结：以上所有关系都可以化成 多对一，关系指向：两个表拥有同一个字段，多条记录拥有相同的字段值的表，指向那这个字段作为主键（即字段值永远不同）的表 ；前表的字段成为外键，即外键（表）指向主键（表）；


10 存储引擎：表的数据类型（存储方式）

表类型：默认的服务器类型，通过my.ini  可以设置：`default-storage-engine=INNODB;
alter table student engine myisam;`   	//student为表名，engine myisam 为表属性
// 在表不存在外键的情况下，上行代码起作用；
innodb & myisam 的区别：
a 保存的文件方式不同:
myisam，一个表保存为三个文件，后缀frm 、myd、myi对应保存的结构、数据、索引；
innodb，一个表一个文件，后缀为frm，innodb存储表空间（ibdatal）在保存数据和索引

选择存储引擎的依据：a 性能 b 功能；
例：
```
create table class(
class_id int primary key auto_increment,
class_name varchar(10)
) engine innodb character set utf8;
```

作者：oscarwin
链接：https://www.zhihu.com/question/20596402/answer/211492971
来源：知乎
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

区别：
1. InnoDB 支持事务，MyISAM 不支持事务。这是 MySQL 将默认存储引擎从 MyISAM 变成 InnoDB 的重要原因之一；
2. InnoDB 支持外键，而 MyISAM 不支持。对一个包含外键的 InnoDB 表转为 MYISAM 会失败；  
3. InnoDB 是聚集索引，MyISAM 是非聚集索引。聚簇索引的文件存放在主键索引的叶子节点上，因此 InnoDB 必须要有主键，通过主键索引效率很高。
   但是辅助索引需要两次查询，先查询到主键，然后再通过主键查询到数据。因此，主键不应该过大，因为主键太大，其他索引也都会很大。
   而 MyISAM 是非聚集索引，数据文件是分离的，索引保存的是数据文件的指针。主键索引和辅助索引是独立的。 
4. InnoDB 不保存表的具体行数，执行 select count(*) from table 时需要全表扫描。而MyISAM 用一个变量保存了整个表的行数，
   执行上述语句时只需要读出该变量即可，速度很快；    
5. InnoDB 最小的锁粒度是行锁，MyISAM 最小的锁粒度是表锁。一个更新语句会锁住整张表，
   导致其他查询和更新都会被阻塞，因此并发访问受限。这也是 MySQL 将默认存储引擎从 MyISAM 变成 InnoDB 的重要原因之一；

如何选择：
1. 是否要支持事务，如果要请选择 InnoDB，如果不需要可以考虑 MyISAM；
2. 如果表中绝大多数都只是读查询，可以考虑 MyISAM，如果既有读写也挺频繁，请使用InnoDB。
3. 系统奔溃后，MyISAM恢复起来更困难，能否接受，不能接受就选 InnoDB； 
4. MySQL5.5版本开始Innodb已经成为Mysql的默认引擎(之前是MyISAM)，说明其优势是有目共睹的。如果你不知道用什么存储引擎，
   那就用InnoDB，至少不会差。

11. select 子句
A 排序 order by
按照字段值进行排序；
语法：order by 字段	升序 | 降序（asc | desc）		默认升序；
运行多字段排序：先按照第一个字段排序，如果说，不能区分，才使用第二个字段，依次类推；
备注：对检索（where）后进行排序；
例：`select * from table order by class_id desc,class_name asc;`

注意：如果是分组（合并），则应该使用对分组字段进行排序的group by 方法；

例：
```
 aa表         a            b
                123        10
                123        12
                1234      11
                1234      14
```
用：`select a, sum(b) from aa group by a;`
结果：
```
     a         b
    123      22
    1234    25
```
B 限制 limit ：限制获得的记录数量 ；（检索、排序后再限制，即先where、order by 、limit ）
语法：`limit offset,row_count;`

offset 	偏移量（开始位置）,默认从0开始，记录索引的下标也是0,1,2····
row_count 总记录数
例：`select * from table limit 2,5;`
说明：从第三行（包含）开始选取5条记录
例：`select * from table limit 5;` 	表示直接取5条记录；
备注：如果设置获取记录条数大于实际的记录数，就有多少条记录获取多少；

C distinct 去掉重复记录（重复记录只留一条）；
例：`select distinct class_id from class;`
备注：重复记录是指检索显示的distinct关键字后面的所有字段的字段值相同的记录；

D union 联合查询：将多条select语句的结果，合并在一起发给用户，称之为联合操作；
使用union 关键字联合连个select语句即可；
备注：union的结果有重复的记录，只保留一条（像执行了distinct），可以通过在union 后面加上all来显示全部，包括重复记录；
备注2: 多个select语句检索的字段数量和字段数据类型及其顺序必须一致（字段名可以不同）；
备注3：检索结果中列名称由第一条select语句的列名决定，这也可以看出union中select语句可以选择不同的表；
例：
```
(select t_name,days from teacher_class where c_name='php0115' order by days desc limit 1)
union all
(select t_name,days from teacher_class where c_name='php0228' order by days desc limit 1);
```
注意：子语句（union下的select语句）结果排序：a 将子语句包裹在子括号内，b 子语句的order by配合limit ，才会生效；

对所有结果进行统一排序：
例：
```
(select t_name,days from teacher_class where c_name='php0115' )
union all
(select t_name,days from teacher_class where c_name='php0228' ) order by days;
```
提示：子语句的括号不是必须的，但不建议；
例：
```
(select id,name,price from test )
union all
(select name,price,id from test2);
```
备注：从上例可以看出，除了字段个数要求相等意外，其他没有要求，

12. 子查询：语句内部的查询语句；
例：`select t_name from teacher where days = (select max(days) from teacher_class);`
备注：父语句不一定是查询语句；子查询需要用括号包裹起来；

子查询分类，不同的分类，会有不同的使用方式：
分类标准：
a子查询的返回值形式；
返回值分类：单值（标量子查询）列（列子查询） 行（行子查询） 表（表子查询）
使用方法
	*标量:获得一个值之后，使用关系运算符进行判断。<>=！= <= >=
例：`select t_name from teacher_class where days <(select max(days) from teacher_class);`
	
列子查询：强调是一列。多个行的一列值；	使用集合类的操作符来完成 in |not in ，还有其他集合操作符：

any(集合) 集合中任何一个；

 =any(集合) 等于集合中的任何一个即可（即in）,

all(集合) 集合中的所有元素，

!all(集合) 不等于集合中的所有元素(即not in)，

!=any(没有选择作用)；

some(集合) 集合中的一些，等于any；

all,any和some 可以使用= ，!=之外运算符；

例：`select t_name,c_name,days from teacher_class where t_name in (select t_name from teacher_class where c_name='php0228');`
	
行子查询（返回一行）：在参与比较时，使用括号可以构建一行(field1，field2);
例：`select t_name,gender,c_name from teacher_class where (gender,c_name) = (select distinct gender ,c_name from teacher_class where t_name ='李白' and c_name ='php0115 limit 1');`
备注：注意用括号构建一行部分；
	
表子查询（返回一个表）:如果用于在from子句内，from 子句内要求是一个表，不能为一个结果，应该给返回的表取别名（用as 别名）
例：`select * from (select t_name,c_name,days from teacher_class where days >20) as temp where t_name like '李%';`
备注：后面where条件是对表temp操作的；外部查询所使用的列名是由子查询指定的；

b 子查询出现的位置:

- where (subquery )
- from  (subquery )
- exists  (subquery ) 判断依据：如果子查询有返回数据，则认为exists 表达式返回真，否则返回假；
例：`select * from teacher_class where exists (select * from where teacher_class.id=t_id);`
备注：exists前面有where 哦；teacher_class.id 为teacher_class表的id；


13 连接查询 join：每个实体一个表，一个业务逻辑使用多个实体的数据，多张表在一起使用，将多个表的记录连接起来；
总体思路：将所有数据按照 某种条件 连接起来，在进行筛选处理；
按连接条件不同，连接分类：

a 内连接：数据内部的连接，要求连接的多个数据都必须存在才能进行连接 ，其实一个表中信息在另一个表中不存在，也能进行，只是在最终结果里只会显示两个表都存在的信息（只显示能对应的记录），要把不同时存在的信息也显示出来，用外连接；
语法：tbl_left inner join tbl_right on 连接条件;
例：`select join_teacher.t_name,join_teacher_class.begin_date from join_teacher inner join join_teacher_class on join_teacher.id=join_teacher_class.t_id where days>20;`

备注：内连接的处理：内连接在连接时是可以省略连接条件，意味着所有左表的数据都要与右表的记录做一个连接。共存在M*N个连接；这种连接就称之为交叉连接（或称笛卡尔积，不建议使用），此时可以使用cross join 代替inner join；
备注2：在MySQL中inner join 是默认方式；即inner join （采用）和join 一样；
备注3：内连接条件写法：where写法：先形成笛卡尔积，然后匹配，on写法（采用）：一条一条的匹配条件得到;using的写法，using要求负责连接的两个实体之间的字段名称一致，语法：using (字段)
eg:
```
SELECT a.article_id, a.title, ac.cat_name, a.add_time, a.file_url, a.open_type, ac.cat_id, ac.cat_name  FROM `3fx`.`ecs_article` AS a INNER JOIN `3fx`.`ecs_article_cat` AS ac ON a.is_open = 1 AND a.cat_id = ac.cat_id AND ac.cat_type = 1 ORDER BY a.article_type DESC, a.add_time DESC LIMIT 8
```
注意：得到验证的sql语句是ON换成where，上面这条不一定成功，但是注意观察ON后面不仅仅是连接条件，还有判断条件；


备注4：查询条件与外连接通用，不过外连接	不能使用where作为连接条件；
备注5：字段前面的表限制，如果不冲突，不奇异，可以不写，但建议写上；
备注6：利用表别名可以简写代码，将上例的代码简写：
备注7：还可以加入列别名，
```
select  t_id as t_id s_id as s_id t.t_name ,c.begin_date from join_teacher as t inner join join_teacher_class as c on t.id=c.t_id where days > 20;
```
备注：上面列 的列别名会在最终的显示表中显示出来，不然就直接两个列名 id id ；


b 外连接：连接的数据有数据不存在，称为外连接，结果可以出现没有连接上的顺序；
语法：`tbl_left left outer join tbl_right on  条件`	outer可以省略；
语法：`tbl_left right outer join tbl_right on  条件`	outer可以省略；
连接分类
左外连接：在连接时，如果出现左边表，数据连接不到右边表情况（即on后面条件不成立），则左边表的数据在最终结果内保留，结果中右表的字段值为NULL，而右表连接不到的左表的数据会被抛弃；
右外连接：与上面对应，保留右表中不能连接记录；

备注：如果要保留左右表的不能连接记录，用union 连接左外连接和右外连接，并且union默认有distinct功能，如果要全部union all；
备注：外连接必须要写条件；

例：`select join_teacher.t_name,join_teacher_class.begin_date from join_teacher left outer join join_teacher_class on join_teacher.id=join_teacher_class.t_id;`

c 自然连接：MySQL通过自己的判断完成连接过程，即不需要指定连接条件，MySQL会使用多表内的，相同字段作为连接条件；
内自然连接：` tbl_left natural join tbl_right `        	 //相当于inner join +using
外自然连接
	左外   语法： `tbl_left natural left join tbl_right `    	//相当于left join +using
	右外    语法：`tbl_left natural right join tbl_right`	//相当于rgiht join +using

备注：连接查询与联合查询区别：前者是在字段方面有所增减，后者是在记录方面有所增减；
```
select student.s_name,teacher4.t_name from teacher4 inner join student on teacher4.t_id=student.s_id;
select s_name,t_name from student inner join teacher4 on t_id=s_id;
```


总结：连接是以一个表为基础，每条记录与右边表的每条记录核对（M*N），成功则保留，最终合成一个 新表 ，整体是字段的增加；当然合成新表后还可以再连接表，甚至表可以连接自己，分别给两个自己取上别名；用的时候把通过哪个字段连接得到的表想象出来，然后再查询，就清晰了；
例：`select c1.class_name as `主队` ,c2.class_name as `客队` , m.match_time as `比赛时间` ,m.match_result as `比赛结果` from  class as c1 inner join match3 as m on c1.id=m.host_id inner join  class as c2 on c2.id =m.guest_id;`
备注：主队 ，客队用单引号或者反引号包裹都行，他们会成为结果显示的字段名；


14. select 可以将检索到的数据保存到服务器的文件内；
语法：`select * into outfile '文件地址' from     ···`

备注：'文件地址' 如果没有可以创建文件，但不能创建文件夹，且不能覆盖已有文件；
备注：如果用outfile 导出文件，可以用`load data infile '文件地址' into table tbl_name;`导入
例：`select data infile 'd:\wamp\data\one' into table teacher;`
备注：导入时，涉及到数据增加，需要考虑是否冲突情况（主要是表中主键）,通常可以在导出文件时，将主键导出为null（select后的列表主键字段为null，这样导出主键字段值就全部null），利用自动增长的特性，可以形成新的主键；

设置输出数据格式(outfile和infile都适用)；
备注2：默认采用行来区分记录，而采用制表符（tab）区分字段；MySQL支持在到处数据时，设置记录与字段的分隔符。通过如下选项：
fields:设置字段选项
lines：设置行选项（记录选项）
先看默认：字段以  '\t'结束 		记录  以 空字符串开始，以'\n'结束；
设置特殊符号
例：
```
select * into outfile 'd:\wamp\data\one' 
fields terminated by '|'  enclosed by 'x'
lines terminated by '\n' starting  by ':'
from teacher_class where t_name='韩信';
```
备注：enclosed 是设置什么来包裹，是被x包裹；
备注2：常规的，所有记录应该通过行来显示，例外的是保存二进制数据，如blob binary，就不能使用outfile，而是select * into dumpfile  '文件位置' from ···

额外知识1：主键和自动增值是独立的，没有任何直接联系，可以单独设置，也可以单独删除，主要用到语法：

a alter table tbl_name modify  id int primary key;	//为id设置主键
b alter table tbl_name drop primary key;		//删除表tbl_name 的主键
c alter table tbl_name modify id int auto_increment;//为id设置自动增长属性，就算 id int auto_increment;代码中完全没有主键，作用是，原来就有主键，主键还在，原来没有，那通过 c 中代码，也没有主键；
d alter table tbl_name modify id int;		//这样就删除了自动增长属性；
备注：primary key 是不受字段其他属性设置影响的，即可以不体现在modify id int;中，但是auto increment 就需要直接体现出来modify id int auto_increment;
总结：设置主键和自动增长都是用modify，而删除，主键是用drop，自动增长用modify；


15 insert
说明：可以不用讲所有字段都插入数据，如果需要完成部分字段插入，则必须要写字段列表；
说明2：如果插入部分字段，可以用set语句：
`insert  into teacher set t_name='张无忌'，c_name="武术";`
说明3：使用值列表语法，可以同时插入多条记录，每条记录采用记录构造符括号完成即可；
说明4：插入数据时，如果主键冲突会如何？默认有主键约束，不会插入成功，但是可以在insert语法内，控制主键冲突时，改成执行更新操作；
例：`insert into teacher (id,t_name,c_name) values (3,'杨过','黯然销魂掌') on duplicate key update t_name='杨过' ,c_name= '黯然销魂掌';` 
备注：update 么有set 哦！
说明5：主键冲突时，还可以用replace代替；
`replace into  teacher (id,t_name,c_name) values (3,'杨过','黯然销魂掌');`
备注：replace作用：如果记录主键存在，则数据就更新，记录主键不存在，则插入记录；



插入数据源：除了使用自定义的数据外，还可以使用select语句查询到的数据，作为插入数据源；
例：`insert into teacher (t_name,c_name) select s_name,c_name from student;`
备注：里面values 也被替换掉了；要求字段数量和类型应该相同，和union相同；

default关键字，表示默认值；
例：`insert intoteacher values (1,2,'xxx',default);`
备注：default就可以设置该条记录该字段为默认值；也可以default(t_name)


15. 删除数据
A delete 运行使用条件（删除符合条件的数据）
a 允许使用limit 限制删除的记录数；
常见的是limit 配合order by 来使用；先将结果排序，在删除固定数量的记录；

b 允许连接删除：允许使用join 语法，同时删除多个表内的记录；
语法：delete from 需要删除数据的表1，表2 using 表连接操作 条件；
例：`delete from one,two using one join two  on one.public_field=two.public_field where one_id=2;`
备注：上例删除的是表one和表two 通过public_field字段连接起来形成的新表中字段one_id的值为2的记录的组成部分在表one和表two中的记录；

B truncate 清空表；类似`delete from table`，但不同是truncate 不会返回删除的记录数，且重建自动增长的主键；因为delete逐行删除，而truncate删除表，在新建表；

16 更新数据
update 支持where 、order by 和limit ，且支持多表同时更新（类似delete 多表删除）；
语法：`update t1 join t2 on t1.x=t2.x where cond;`
多表更新例子：
`update one join two on one.public_field=two.public_field set one_data ='x' ,two_data='y' where one_id=3;`

备注：除了没有连接插入，其他都有，包括连接查询，连接删除，连接更新；

17备份/还原

A 备份
方案1：针对于myisam类型，直接将tbl_name.frm 、tbl_name.nyd、tbl_name.myl三个文件，保存，备份即可；需要的时候，直接解压到移动到相应的数据库目录内即可；
注意：如果是同样方法，处理的innodb表结构的文件，则使用show table 时，也可以看到表名，但是不能使用的；

方案2（通用）
思路：将建表结构与插入数据的sql语句生成并保存，下次如果需要该结构和数据直接将数据语句执行即可；
利用MySQL提供的工具完成的（mysqldump.exe）,但不是sql语言的一部分，不需要再MySQL命令行客户端执行，而是在mysqldump.exe直接运行即可；
执行方式：
a 设置环境变量，可以直接调用，有无后缀名无所谓；
b 直接通过文件路径找到文件，并执行；
执行语法（先连接数据库）：mysqldump -uroot -p db_name > 备份文件地址
```
linux : mysqldump --opt table -u root -p123456 > /root/bk.sql
windows : mysqldump --opt table -u root -p123456 > D:/bk.sql
```
备注：？？没有主机ip，到了真实服务器杂用呢？

B 还原 ：将是将备份方案2中生成的sql语句执行以下就行了；
a 在mysql客户端直接执行即可，先创建新的数据库（如果备份的是表，则切换到已有数据库），使用 source 指令，可以指定需要执行sql语句的源代码文件；
语法：source 文件地址	(采用)
例：mysql>source d:/wamp/www/data.sql;
特别：分隔符是/，不是转义符；

特别注意：a 备份和还原代码中的地址不能有符号包裹；b 备份代码（在mysqldump.exe中执行）和还原代码（在mysql.exe中执行）都不能以分号结束，而是直接回车执行；

b 常见的备份操作：
b.1 备份整个数据库

`mysqldump -u用户名 -p密码  db_name >bak.sql    `		
备注：bak.sql为sql类型的文件名；
例：`mysqldump -uroot -p php_one > d:/php_one.sql`
注意：第一，必须有文件和文件名sql，第二代码完后没有分号；

b.2 备份不定数量的表

`mysqldump -u用户名 -p密码  db_name tbl_name1 tbl_name2··· >bak.sql`

18 视图（虚拟表）
A 视图创建
创建视图语法：`create view view_name [(新字段列表)] as select查询语句;`
备注:as 后面的语句就是查询语句，select返回一个表，新字段列表（可选）如果有中文要有反引号包裹；
作用：
a 创建视图后，视图就是数据库中的虚拟表，视图本身不保存数据，只是通过select查询语句获得相应的数据，虚拟感觉就像还保存了这个数据（其实没有保存数据，只保存了创建视图的sql语句，再次显示虚拟表时，还要执行相应的sql语句）；
b 通过视图可以利用union 和join 查询，来简化业务逻辑；
视图主要针对查询操作，当然有些视图也可以插入数据，并且映射到实际表；

B 删除视图：`drop view view_name;`
或者：`drop view [if exists] view_name;`

C 修改视图：`alter view v_name [(新字段列表)] as select_statement;`
备注：select_statement就是select语句；可选的新字段列表可以为原来的字段命新名；


D 视图的执行算法： merge和 temptable；
视图算法是指一个视图在什么时候执行，依据哪些方式执行；
merge：合并的执行方式，每当执行的时候，先将我们视图的sql语句与外部查询视图的sql语句混合在一起，最终执行；
temptable：临时表，模式，每当查询的时候，将视图所使用select语句生成一个结果临时表，再在当前的临时表内进行查询；

备注：尽量使用视图完成读操作；对视图的修改，也是对真实表的修改；删除视图时，不会影响到真实表；如果做外部接口，一个数据库多个应用，针对每个应用，采用不同的视图接口；

19. 事务
说明：一组sql语句操作单元。组内所有sql语句完成一个业务；
如果整组成功：意味着所有sql都实现，如果其中任何一条失败，意味着整个操作都失败，整个过程都没有意义；应该使数据库回到操作前的初始状态，这种特性就是事务；
思路：就是在一组操作之间，设计一个记号（备份点）；
实现：利用innodb存储引擎的事务日志功能（innodb 和bdb的存储引擎支持）；
sql执行分为两个阶段：a 执行语句阶段（结果在事务日志里，如果用该进程查询，也会显示更改后的结果，但其他进程或者绘画不会看到），b 将执行结果，提交到数据库阶段（成功执行，会自动提交）；所以如果需要执行事务，就需要关闭自动提交功能；
自动提交：存在一个系统变量 autocommit 可以对自动提交进行设置；
语法：`set autocommit =0;  （这种关了不会自动开，通过set autocommit =1; `不建议采用）
`show variables like 'autocommit';` 查看设置是否成功；

sql语法执行后，状态：
成功则执行语法：commit ;(语句就这一条)
失败则回到备份点语法：rollback; （语句就这一条）
常见的事务指令：
`start transaction`  （begin也行，但不建议）功能是关闭自动提交，事务结束后（即输入commit;或者rollback后），将会开启自动提交；
备注：怎么判断执行成功还是失败呢？
事务特点：a 原子性 b 一致性（当一个事务进行的时候，其他用户或者进程无法更改数据），c 事务之间的隔离性；4 持久性；ACID

**说明**
- 事务开启中，插入数据，先在表中预留插入记录位置(在自增id上有体现)，无论最终是**提交还是回滚**，表中那个id都将不会被其他插入的记录使用
- 事务中先插入记录，事务中在查询结果会包含前面事务中插入的记录，但事务外面肯定不行


20. 触发器（猜想：直接用PDO的事务代替这一操作）
监听数据操作：表中设置一个对每行数据的一个监听器，监听相关事件，每当事件发生时，会执行一段sql完成的一段功能代码；
触发器元素：事件，执代码；
创建触发器： `create trigger  trigger_name   事件  on table_name `执行性代码（即sql语句）;
事件：
```
插入   insert
删除   delete
修改   update
```
事件的时机：执行之前 before 和 执行之后 after
形成了六种事件：`before insert，before delete ，before update，after insert ，after delete，after update`
例：`create trigger jiaobanfei after update on student for each row update class set  salary=salary -20 ;`
注意：a 触发器不能同名；b 对于update 只能用set 进行操作，insert与delete只能借助第二张表才能实现需要的目的。

管理触发器：
删除   `drop trigger trigger_name;`
查看   `show create trigger trigger_name;`

获得触发该程序时的数据，利用触发程序内的 new 和 old 来完成；
old ：监听事件所在表上的数据，	在事件发生之前时的数据，旧的数据；
new：监听表上，事件发生之后，新处理完毕的数据。
上述数据是指触发该事件的表中的记录，old.字段   和 new.字段 永远代表触发事件表的更新前后的值
备注：事件是before insert和after insert，不能使用old，而事件before delete 和after delete，则不能使用new；

触发程序由多条sql语句组成：
a 语句组成语句块 （begin end）用来标识语句块；
b 语句块的语句需要独立的语句结束符，分号；
命令行：由于触发程序内使用分号作为语句结束符，那么命令行客户端碰到分号时，就应该理解成触发程序内子语句结束，而不是创建触发器的语句结束；所有应该通过修改命令行的语句结束符达到目的。delimiter 语句可以完成设置语句结束符；
例：
```
delimiter $$
create trigger ruxue after insert on student
for each row
begin
update class set count = count +1;
update class set money = money +20;
end
$$
delimiter ;
```
注意：设置的语句结束符记着换回来；

21 PHP操作MySQL
A 准备工作：MySQL服务器存在，利用客户端存在，现在要用php去操作，那么php就作为客户端；
a MySQL服务器要开放接口，运行其他语言操作，在windows下，需要将mysql提供的libmysql.dll 放置到可以被别的程序使用的地方，通常是 系统盘符的windows 下；
真实项目时MySQL的分开安装：mysql-server , mysql-client , mysql - devel 才能用其他语言去操作服务器；
b 相应的客户端，利用mysql服务器开放的接口，完成编程，操作mysql数据库，在php内，增加一个模拟客户端的功能；
常见的，php操作mysql的模拟客户端有三个：mysql、mysqli、PDO（采用）；使用其中之一即可；


B 载入相应的功能：载入php的相关扩展，在php的配置文件找到extension =php_mysql.dll 取消注释；php_mysql.dll该文件在ext文件夹中；

22 php 编码操作mysql
A 连接和认证 
mysql_connect(连接目标，用户，密码);
说明：连接目标是 服务器地址 :  端口号	
例：（php中的代码）
$server = 'localhost:3306';
$user ='root';
$pass = '';
$link = mysql_connect ($server ,$user,$pass);
var_dump($link);
备注：别忘了设置通信编码；

B 执行相应的sql 语句
mysql_query (); 发送一条语句；
例：（php代码）
$query = "show databases";		//show databases 没有以分号结束；
$result=mysql_query($query);
备注：当语句执行成功后，会有结果返回给php；
结果分为两种形式，如果执行的语句有具体返回结果，就是结果集，如果执行语句没有具体返回结果，就是返回执行语句的状态（成功或者失败）

C 处理结果
a 决定于执行结果，两种处理方式：a 只返回布尔值，表示是否执行成功，直接根据布尔值，给出用户提示即可；
b 返回为结果集，存在返回数据，在结果集内提取我们需要的数据；用fetch 系列函数操作；
```
mysql_fetch_assoc() 
mysql_fetch_row()
mysql_fetch_array()
```
上述三个函数，功能一致：在结果集内，取得一条记录，区别在于返回值得形式上，都是数组，区别在于小标；
assoc 返回是关联数组，下标是字段名（字符串）
row 返回是索引数组，下标是字段位置（0,1,2）
array返回是混合数组，下标有两个部分，字段名和索引位置；
备注：上面三个函数任意一个执行一次，就算用其他两个函数执行，返回结果也是下一条记录；
备注2：返回结果集语句 show、desc、select、explain;（能在黑窗口看到的）
           返回布尔值：insert 、update、delete等等；

4 释放连接
`mysql_close()`;释放连接函数；

注意：a 连接资源，原则上，执行任何一个操作，都应该指明当前所使用的连接资源，但是php可以自动帮助我们找到已经存在的连接资源；如果操作多个数据库连接资源，就必须指定（建议）；具体做法是在`mysql_query($query,$link)`等函数都可以指定连接资源，但是fetch 类函数不用指定；
注意2：结果都是以记录的形式返回的，即使只有一条记录的一个字段；


23. 功能函数
A 错误处理：php利用msyql扩展执行sql语句时，一旦sql语句发生错误，不会显示错误信息；称之为 静默模式  的错误处理机制；
获得错误信息函数
`mysql_error([ resource $link_identifier ] )`;错误信息，返回上一个 MySQL 操作产生的文本错误信息 
`mysql_errno([ resource $link_identifier ] )`;错误编号，返回上一个 MySQL 操作中的错误信息的数字编码 
例1：
```
if (!mysql_query($query)){
	//失败
	echo mysql_error();
}
例2：
if(!mysql_query($query)){
	//失败
	exit(mysql_errno().":".mysql_error());
}
```
备注：实际操作中个，封装一个执行sql的方法的类，所有的操作都调用这个类的方法，在方法内部做错误处理
B 获得执行结果额外信息
a 获得结果集内的数据数量
`mysql_num_rows(resource $result)`;	//适用于查

b 操作后受影响的行数；
`mysql_affected_rows()`;    //适用于增、删、改


c 获得最新的自动增长的字段的值，通常每个表都会有自动增长的id作为主键，但是该值在插入数据时我们不知道，是数据库通过计算生成的，如果插入数据后，需要刚获得插入数据，利用生成主键id，获得刚刚生成的主键；
`mysql_insert_id([ resource $link_identifier ] );` 取得上一步 INSERT 操作产生的 ID ，$link_identifier就是打开的连接资源；

d 如果资源使用完毕，也应该释放资源；
`mysql_free_result(结果集)`;


24. SQL编程
A 
SQL:结构化查询语言；
注释符号    # 或者 --
块注释       /*  */
语句结束符  ;   \g   \G(格式化显示)
可以用delimiter 修改语句结束符；

B 变量
a 字段名
b mysql有很多系统默认变量，例如 show variables like 'char%';	#这个是查询系统变量；
c 用户自定义变量
	定义一个变量 	set 变量名=变量值;
注意：为了区分系统变量和字段与用户自定义变量，需要在用户变量前，增加@标识符；
例如：`set @who = '刘作超';select @who;`
例如：`set @total = (select count(*) from teacher );`

d 定义一个变量 select into
语法：select 字段列表 表达式 ... into 变量列表;
例：`select 10,15,20 into @a,@b,@c;`
`select @a,@b,@c;`
例2：`select c_name from class where id=2 into @name; `从表class中取出c_name值赋值给@name;
注意：select into @var 要求只能返回一行，且变量数量要跟返回值得数量相同；

e 利用select表达式达到为变量赋值的目的；
语法：`select @who := '小李';` 	#这句是赋值，但是:=同样适用于set 和where等后面；
但例：
`select @who = '小李';`	#这句是判断变量@who与小李相不相等，相等返回1，不等返回0

f 作用域：用户定义的函数，是全局的（在函数内直接可用），也存在函数内的局部变量；

g 有效期：会话结束（连接结束）


C 函数
a 内置函数（看一下ppt还有用户手册）*****



a.1 数值函数
Abs(X)，绝对值 abs(-10.9) = 10
Format(X，D)，格式化千分位数值 format(1234567.456, 2) = 1,234,567.46
Ceil(X),向上取整 ceil(10.1) = 11
Floor(X),向下取整 floor (10.1) = 10
Round(X),四舍五入去整
Mod(M,N) M%N M MOD N 求余 10%3=1
Pi(),获得圆周率
Pow(M,N) M^N
Sqrt(X)，算术平方根
Rand(),随机数
TRUNCATE(X,D) 截取D位小数

a.2 时间日期函数
Now(),current_timestamp(); 当前日期时间
Current_date();当前日期
current_time();当前时间
Date(‘yyyy-mm-dd HH;ii:ss’);获取日期部分
Time(‘yyyy-mm-dd HH;ii:ss’);获取时间部分
Date_format(‘yyyy-mm-dd HH;ii:ss’,’ %D %y %a %d %m %b %j');
Unix_timestamp();获得unix时间戳
From_unixtime();//从时间戳获得时间




时间日期函数
Now(),current_timestamp(); 当前日期时间
Current_date();当前日期
current_time();当前时间
Date(‘yyyy-mm-dd HH;ii:ss’);获取日期部分
Time(‘yyyy-mm-dd HH;ii:ss’);获取时间部分
Date_format(‘yyyy-mm-dd HH;ii:ss’,’ %D %y %a %d %m %b %j');
Unix_timestamp();获得unix时间戳
From_unixtime();//从时间戳获得时间
hour(now())  这样可以返回当前时间的小时，其他类似；


a.3 字符串函数
LENGTH (string )   //string长度，字节
CHAR_LENGTH(string)    //string的字符个数
SUBSTRING (str , position [,length ])   //从str的position开始,取length个字符
REPLACE (str ,search_str ,replace_str )   //在str中用replace_str替换search_str
INSTR (string ,substring )   //返回substring首次在string中出现的位置
CONCAT (string [,... ])   //连接字串
CHARSET(str)  //返回字串字符集
LCASE (string )  //转换成小写
LEFT (string ,length )   //从string2中的左边起取length个字符
LOAD_FILE (file_name )   //从文件读取内容
LOCATE (substring , string [,start_position ] )   //同INSTR,但可指定开始位置
LPAD (string ,length ,pad )   //重复用pad加在string开头,直到字串长度为length
LTRIM (string )  //去除前端空格
REPEAT (string ,count )   //重复count次
RPAD (string ,length ,pad)   //在str后用pad补充,直到长度为length
RTRIM (string )   //去除后端空格
STRCMP (string1 ,string2 )   //逐字符比较两字串大小


a.4 流程函数：
CASE WHEN [condition] THEN result [WHEN [condition] THEN result ...] [ELSE result] END   多分支
IF(expr1,expr2,expr3)  双分支。


a.5 聚合函数
Count()
Sum();
Max();
Min();
Avg();
Group_concat()


a.6 其他常用函数
Md5();		#都支持，项目中，
password(); 	#mysql的用户名密码；两次sha1();
sha1();		#项目中常见
ISNULL(var)	#判断变量是否为空

b 存储函数-自定义函数
b.1  语法：`create function function_name (参数列表) `  返回值类型   函数体；
例：
```
delimiter $$
create function sayHello() returns varchar(20) 
begin
return 'hello world！';
end
$$
delimiter ;
```
注意：自定义函数是与当前的数据库绑定的（即其他数据库如果没生命这个函数，不能调用），可以通过指定数据库来调用该函数；

b.2 sql中流程控制
b.2.1 分支 
	if 条件2  then
		条件1满足执行的语句；
	elseif 条件2 then
		条件2满足执行的语法 ;
	···
	else
		上面的条件全都不满足执行的语句
	end if;
说明：elseif 和 else 可以省略；
例：判断时间
```
delimiter $$
drop function if exists pan;		#这儿删除函数不能有括号；
create function pan() returns varchar(10)	#注意是returns 不是return；
begin
	if hour(now()) >=18 then
	return 'abc';
	else 
	return 'def';
	end if;			#这儿的分号也必须要；无论是分支结构还是循环结构结束后都有分号；
end
$$
delimiter ;
```
备注：如果提醒the function has none of DETERMINISTIC；则查看浏览器收藏夹里的mysql文件夹；


b.2.2 循环
	while 条件 do
 	循环体
	end while;

例：1到10和
```
delimiter $$
drop function if exists xun;
create function xun() returns int 
begin
	declare i int default 0;
	declare total int default 0;
	while i<10 do
	set total = total + i;		#一定要用set才行；
	set i=i+1;
	end while;
	return total;
end
$$
delimiter ;
```

b.2.2.2 循环的提前终止；
leave   标签	作用：跳出循环
iterate 标签	作用：终止本次循环；
注意，不是根据leave 和iterate所在位置来终止哪个循环的，而是由循环的标签来决定的；

标签：给循环起的名字
语法：
标签名: while ···
···
end while 标签名;
例：
```
delimiter $$
drop function if exists xun;
create function xun() returns int 
begin
	declare i int default 0;
	declare total int default 0;
	w : while i<10 do
	set i=i+1;
	if i=5 then
	iterate w;
	end if;
	set total = total + i;
	end while w;
	return total;
end
$$
delimiter ;
```

c 函数内使用的变量
@var 的形式相当于全局变量，函数内外通用；

局部变量
函数参数：参数，同样需要确定类型；语法： 参数名  类型   	（说明这儿没有@符号了；）
备注：一个函数可以有多个参数，参数之间用逗号分隔；

函数局部变量
语法：declare  变量名   类型  default 默认值;


额外知识：
between  value1 and value2；范围比较value1<=var<=value2;
例：select * from class2 where id between 2 and 5；

可以使用 ROLLUP, 它能用一个问询提供双层分析。将一个 WITH ROLLUP修饰符添加到GROUP BY 语句，使询问产生另一行结果，该行显示了所有年份的总价值

25 为字段设置索引
方式一：在创建表时设置，在声明字段类型的最后用   index index_name(字段名)	                          
备注：字段名没有引号，如果是汉字还有待测试，猜测:可能加反引号；
例：
```
drop table if exists session;
create table session (
sid varchar(32) not null default '',
sdata text,
utime int not null default 0,
uip char(15) not null default '',
uagent varchar(300) not null default '',
index session_sid(sid)
);
```
方式二：在已有的表上，为字段加索引；
语法：`alter table tbl_name add index(字段名);`
语法二：`alter table tbl_name add index index_name(字段名);` //最好设置index的name值；
例：`alter table test add index(id);`
其余语法：
```
ALTER TABLE table_name DROP INDEX index_name;		//删除索引；
SHOW INDEX FROM tblname\G
```
//\G只是改变显示方式而已，索引名是Key_name对应的值；

索引分类：
PRIMARY, INDEX, UNIQUE 这3种是一类
PRIMARY 主键。 就是 唯一 且 不能为空。主键,一个表中只能有一个主键，该键值不能为 NULL ,不能重复；
INDEX 索引，普通的
UNIQUE 唯一索引。 不允许有重复。一一个表中可以有多个唯一性索引，该键值不能重复,但是可以有多个记录值为 NULL
FULLTEXT 是全文索引，用于在一篇文章中，检索文本信息的。
备注：效率不能一概而论

26. 设置utf8编码
A 更改数据库配置文件（有时可省略）：
	a.1 在[client]字段里加入default-character-set=utf8，如下： 
		[client] 
		port = 3306 
		socket = /var/lib/mysql/mysql.sock 
		default-character-set=utf8 
	a.2 在[mysqld]字段里加入character-set-server=utf8，如下： 
		[mysqld] 
		port = 3306 
		socket = /var/lib/mysql/mysql.sock 
		character-set-server=utf8 
	a.3 在[mysql]字段里加入default-character-set=utf8，如下： 
		[mysql] 
		no-auto-rehash 
		default-character-set=utf8 
备注：设置完成后，可以用SHOW VARIABLES LIKE 'character%';应该只有 character_set_filesystem和character_sets_dir的值不是utf8 ，其余都是说明设置成功；记得要重启服务器，才生效哦！

B 设置传输编码(有时可省略)，如：
`set names utf8;`

C 创建数据库时，设置编码，例：
`create database cishop charset utf8;`

D 创建表时，设置编码字符，例：
```
create table ci_category(
	cat_id smallint unsigned not null auto_increment primary key comment '商品类别ID',
	cat_name varchar(30) not null default '' comment '商品类别名称',
	parent_id smallint unsigned not null default 0 comment '商品类别父ID',
	cat_desc varchar(255) not null default '' comment '商品类别描述',
	sort_order tinyint not null default 50 comment '排序依据',
	unit varchar(15) not null default '' comment '单位',
	is_show tinyint not null default 1 comment '是否显示，默认显示',
	index pid(parent_id)
)engine=MyISAM charset=utf8;
```

E 把php文件改成utf8；
F 把浏览器显示更改为utf8；


27 建立商品属性表，猜测可以直接和商品表合并；
例：（这里省略商品信息）
```
CREATE table ci_test(
	goods_attr_id int unsigned not null auto_increment primary key comment '编号ID',
	goods_id int unsigned not null default 0 comment '商品ID',
	goods_attr_value varchar(5000) not null default 0 COMMENT '属性名和属性值',
	index goods_id(goods_id)
)engine=innoDB charset=utf8;
```
在模板中代码(只是测试，不是最终的):
```
<form action="<?php echo base_url('admin/goods/test_insert');?>" method="post" >
	<table>
		<tr>
			<td>产品参数名</td>
			<td>产品参数值</td>
		</tr>
		<tr>
			<td><input name="attr_value[]" value="<?php echo isset($data[0])? $data[0] :"";?>" type="text"/></td>
			<td><input name="attr_value[]" value="<?php echo isset($data[1])? $data[1] :"";?>" type="text"/></td>
		</tr>
		<tr>
			<td><input name="attr_value[]" value="<?php echo isset($data[2])? $data[2] :"";?>" type="text"/></td>
			<td><input name="attr_value[]" value="<?php echo isset($data[3])? $data[3] :"";?>" type="text"/></td>
		</tr>
		<tr>
			<td><input name="attr_value[]" value="" type="text"/></td>
			<td><input name="attr_value[]" value="" type="text"/></td>
		</tr>
		<tr>
			<td><input name="dosubmit" value="提交" type="submit"/></td>
			<td><input name="next" value="下一页" type="button"/></td>
		</tr>
	</table>
</form>
```
控制器中代码（特别注意：验证输入数据的代码）
```
public function test_insert(){
	$data = $this->input->post('attr_value',TRUE);
	$mark = TRUE;	//如果输入数据正常，则为真，反之为假；
	//判断是否按照属性--值 这样填写完整
	foreach($data as $key=>$value){
		$val_1 = trim($value);
		//如果属性名存在，而属性值不存在，则提醒用户，且暂时不提交
		if( ($key %2==0) && !empty($val_1) ){
			$val_2 = trim($data[$key+1]);
			if(empty($val_2)){
				$mark = FALSE;
				//因为下标和真实输入框编号有差距
				echo ($key+2)."输入框不能为空！";
			}
		}
		//如果属性值存在，而属性名不存在，则提醒用户，且暂时不提交
		elseif(($key %2==1) && !empty($val_1) ){
			$val_2 = trim($data[$key-1]);
			if(empty($val_2)){
				$mark = FALSE;
				echo $key."输入框不能为空！";
			}
			
		}
	}
	if($mark){
		//把接收到的数组数据转化为字符串
		$str = trim(implode('||',$data),'||');
		//如果数据经过检验，则插入数据库
		$this->goods_model->test_insert($str);
		
	}
}
```

28 group by
A 语法可以根据给定数据列的每个成员对查询结果进行分组统计，最终得到一个分组汇总表。
特别注意：
理解：先不用管group by 以及其子句，先想象直接select语句，然后再以group by 后面字段为条件（以group by后字段值相同为一组，如果select子句有的字段，而group by后没有，最后结果中这种类型的字段值以第一条搜索到的记录的字段值为准），进行列函数(聚合函数，如sum(),count())；
例：`select name, age,count(*) from test group by name;`
显示结果：
```
name 	age		count(*)
haha	9 		1
hehe	8 		2
lydia	10 		1
neo 	7 		2
```
说明：select 搜索其子句后，count(*)以name字段值相同为操作对象；


B HAVING 进行聚合数据后再进行条件筛选语句，而where子句在聚合数据之前筛选；说明：聚合数据是指group by + count()函数，并且位置大致为 where ... group by ... having ... order by ...		//语句1
eg:`select name ,count(*)  from data group by name having name like '%lin%' order by couny(*) DESC;`

C 在语句1 + with rollup;可以得到汇总信息
猜测如：逐渐忽略分组字段(即：认为字段值相同，都为NULL，达到合并)
```
+------+-------------+

| year | SUM(profit) |

+------+-------------+

| 2000 |        4525 |

| 2001 |        3010 |

| NULL |        7535 |

+------+-------------+

eg2：
rank1 subrank1 		3
rank1 subrank2 		3
rank1 NULL        	6
rank2 subrank1 		1
rank2 subrank2 		2
rank2 NULL       	3
rank3 subrank1 		1
rank3 NULL       	1
NULL  NULL        	10
```

29. ISNULL、IFNULL、NULLIF用法：

A  ISNULL(expr) 的用法：
如expr 为null，那么isnull() 的返回值为 1，否则返回值为 0。 
```
mysql> select isnull(1+1);
-> 0
mysql> select isnull(1/0);
-> 1
```
使用= 的null 值对比通常是错误的。 

isnull() 函数同 is null比较操作符具有一些相同的特性。请参见有关is null 的说明。

B  IFNULL(expr1,expr2)的用法：

假如expr1   不为   NULL，则   IFNULL()   的返回值为   expr1; 
否则其返回值为   expr2。IFNULL()的返回值是数字或是字符串，具体情况取决于其所使用的语境。
```
mysql>   SELECT   IFNULL(1,0);   
                  ->   1   
mysql>   SELECT   IFNULL(NULL,10);   
   ->   10   
 mysql>   SELECT   IFNULL(1/0,10);   
           ->   10   
mysql>   SELECT   
IFNULL(1/0,'yes');   
->   'yes'  
IFNULL(expr1,expr2)
```
的默认结果值为两个表达式中更加“通用”的一个，顺序为STRING、   REAL或   
INTEGER。假设一个基于表达式的表的情况，     或MySQL必须在内存储器中储存一个临时表中IFNULL()的返回值：   
`CREATE   TABLE   tmp   SELECT   IFNULL(1,'test')   AS   test `  
在这个例子中，测试列的类型为   CHAR(4)。

C  NULLIF(expr1,expr2)  的用法：  
如果`expr1 = expr2`成立，那么返回值为NULL，否则返回值为expr1。这和`CASE WHEN expr1=expr2`
```
THEN   NULL   ELSE   expr1   END相同。     
mysql>   SELECT   
NULLIF(1,1);   

           ->   NULL   
mysql>   SELECT   NULLIF(1,2);   
            ->   1  
```
如果参数不相等，则   MySQL   两次求得的值为     expr1   。

## 知识补充

### concat
`CONCAT()`函数用于将多个字符串连接成一个字符串。
使用数据表Info作为示例，其中`SELECT id,name FROM info LIMIT 1;`的返回结果为
```
+----+--------+
| id | name   |
+----+--------+
|  1 | BioCyc |
+----+--------+
```
1、语法及使用特点：
`CONCAT(str1,str2,…)`                   
返回结果为连接参数产生的字符串。如有任何一个参数为NULL ，则返回值为 NULL。可以有一个或多个参数。

2、使用示例：
`SELECT CONCAT(id, '，', name) AS con FROM info LIMIT 1;`返回结果为
```
+----------+
| con      |
+----------+
| 1,BioCyc |
+----------+
```
`SELECT CONCAT('My', NULL, 'QL');`返回结果为
```
+--------------------------+
| CONCAT('My', NULL, 'QL') |
+--------------------------+
| NULL                     |
+--------------------------+
```

实践：
表结构同下面的group_concat例子中的表结构

获取
```
select concat(bankName,'_' ,bankId,'__',createTime) from fi_banks;
```
![](img/001.png)

备注：连接同一条记录中的字段值

### CONCAT_WS
使用函数`CONCAT_WS()`。使用语法为：`CONCAT_WS(separator,str1,str2,…)`
`CONCAT_WS()` 代表 `CONCAT With Separator` ，是`CONCAT()`的特殊形式。第一个参数是其它参数的分隔符。分隔符的位置放在要连接的两个字符串之间。分隔符可以是一个字符串，也可以是其它参数。如果分隔符为 NULL，则结果为 NULL。函数会忽略任何分隔符参数后的 NULL 值。但是`CONCAT_WS()`不会忽略任何空字符串。 (然而会忽略所有的 NULL）。

如`SELECT CONCAT_WS('_',id,name) AS con_ws FROM info LIMIT 1;`返回结果为
```
+----------+
| con_ws   |
+----------+
| 1_BioCyc |
+----------+
```
`SELECT CONCAT_WS(',','First name',NULL,'Last Name');`返回结果为
```
+----------------------------------------------+
| CONCAT_WS(',','First name',NULL,'Last Name') |
+----------------------------------------------+
| First name,Last Name                         |
+----------------------------------------------+
```

### Group_concat

参考资料：<http://blog.sina.com.cn/s/blog_4e808acf01009qna.html>
`GROUP_CONCAT()`是MySQL数据库提供的一个函数，通常跟GROUP BY一起用,具体可参考MySQL官方文挡:<http://dev.mysql.com/doc/refman/5.0/en/group-by-functions.html#function_group-concat>
语法:
`GROUP_CONCAT([DISTINCT] expr [,expr ...] [ORDER BY {unsigned_integer | col_name | expr} [ASC | DESC] [,col_name ...]] [SEPARATOR str_val])`

1.例如:
`SELECT student_id, GROUP_CONCAT(courses_id) AS courses FROM student_courses WHERE student_id=2 GROUP BY student_id; `
```
+------------+---------+ 
| student_id | courses | 
+------------+---------+ 
| 2 | 3,4,5 |
+------------+---------+ 
```
这 就不需要用php循环了
```
$row = $pdo->query("SELECT student_id, GROUP_CONCAT(courses_id) AS courses FROM student_courses WHERE student_id=2 GROUP BY student_id");
$result = explode(',', $row['courses']); 
```
2.当然分隔符还可以自定义，默认是以“,”作为分隔符，若要改为“|||”，则使用SEPARATOR来指定，例如：
```
SELECT student_id, GROUP_CONCAT(courses_id SEPARATOR '|||') AS courses FROM student_courses WHERE student_id=2 GROUP BY student_id;
+------------+---------+ 
| student_id | courses | 
+------------+---------+ 
| 2 | 3|||4|||5 |
+------------+---------+ 
```
3.除此之外，还可以对这个组的值来进行排序再连接成字符串，例如按courses_id降序来排：
```
SELECT student_id, GROUP_CONCAT(courses_id ORDER BY courses_id DESC) AS courses FROM student_courses WHERE student_id=2 GROUP BY student_id;
+------------+---------+ 
| student_id | courses | 
+------------+---------+ 
| 2 | 5,4,3 |
+------------+---------+ 
```
4.需要注意的：
a.int字段的连接陷阱

当你用group_concat的时候请注意，连接起来的字段如果是int型，一定要转换成char再拼起来，
否则在你执行后(ExecuteScalar或者其它任何执行SQL返回结果的方法)返回的将不是一个逗号隔开的串，
而是byte[]。

该问题当你在SQLyog等一些工具中是体现不出来的，所以很难发现。
```
select group_concat(ipaddress) from t_ip 返回逗号隔开的串
select group_concat(id) from t_ip 返回byte[]
select group_concat(CAST(id as char)) from t_dep 返回逗号隔开的串
select group_concat(Convert(id , char)) from t_dep 返回逗号隔开的串
```
附Cast,convert的用法：
`CAST(expr AS type)`, `CONVERT(expr,type)` , `CONVERT(expr USING transcoding_name)`
`CAST()` 和`CONVERT()` 函数可用来获取一个类型的值，并产生另一个类型的值。

这个类型 可以是以下值其中的 一个：
```
BINARY[(N)]
CHAR[(N)]
DATE
DATETIME
DECIMAL
SIGNED [INTEGER]
TIME
UNSIGNED [INTEGER]
```

实践：
AGM导入solr表操作
```
$base = $this->track_attribute_model->alias('ta')
    ->join('__ATTRIBUTE__ a ON ta.attribute_id=a.attribute_id')
    ->join('__TRACK__ t ON t.id = ta.track_id', 'LEFT')
    ->field('ta.track_id, t.name, t.bpm, t.duration, group_concat(a.attribute_id,\'_\',replace(a.name, \' \', \'_\') SEPARATOR \' \') as tags')
    ->where(['a.language' => 'en-us'])
    ->group('ta.track_id')
    ->select();
```
注意：group_concat()里面的转移符是针对字符串是用单引号包裹的，与group_concat()用法没有关系

实践2：
表结构
```
CREATE TABLE `fi_banks` (
  `bankId` int(11) NOT NULL AUTO_INCREMENT,
  `bankName` varchar(50) NOT NULL,
  `dataFlag` tinyint(4) NOT NULL DEFAULT '1',
  `createTime` datetime NOT NULL,
  PRIMARY KEY (`bankId`),
  KEY `bankFlag` (`dataFlag`)
) ENGINE=InnoDB AUTO_INCREMENT=25 DEFAULT CHARSET=utf8 COMMENT='银行表';
```
获取
```
select group_concat(bankName,'_' ,bankId,'__',createTime SEPARATOR '|||') from fi_banks group by dataFlag;
```
结果
```
中国工商银行_17__2016-05-20 11:19:27|||中国农业银行_18__2016-05-20 11:19:27|||中国人民银行_19__2016-05-20 11:19:27|||招商银行_20__2016-05-20 11:19:27|||中兴银行_21__2016-05-20 11:19:27|||交通银行_22__2016-05-20 11:19:27|||深圳发展银行_23__2016-05-20 11:19:27|||中国光大银行_24__2016-05-20 11:19:27
```
备注：连接过程是先同一条记录的不同字段连接，然后进行不同记录间连接



**b.长度陷阱**
用了group_concat后，select里如果使用了limit是不起作用的.
用group_concat连接字段的时候是有长度限制的，并不是有多少连多少。但你可以设置一下。

使用`group_concat_max_len`系统变量，你可以设置允许的最大长度。
程序中进行这项操作的语法如下，其中 val 是一个无符号整数：
`SET [SESSION | GLOBAL] group_concat_max_len = val;`
若已经设置了最大长度， 则结果被截至这个最大长度。

在SQLyog中执行` SET GLOBAL group_concat_max_len = 10 `后，重新打开SQLyog，设置就会生效。

### timestamp默认值
猜测：timestamp默认值CURRENT_TIMESTAMP或者'0000-00-00 00:00:00'，前者在同一
个表中只能使用一次

## 高性能 MySQL

### 第 1 章 MySQL 架构与历史

#### 读写锁
A 共享锁(读锁)：共享的，或者说互不阻塞的，猜测：多个用户可以同时操作；
B 排他锁(写锁)：排他性，一个写锁会阻碍其他的写锁和读锁，即在给定的时间内，
              只有一个用户能执行写入操作，并阻止其他用户写入和读取，并且写锁比读锁拥有更高的优先级；

#### 锁粒子
A 表锁：MySQL最基本的锁策略，也是开销最小的锁，即一个用户在对表进行写操作(增、删、改)时，需要先获取写锁，这回阻碍其他用户对该表的所有操作(增、删、改、查)；

#### 行级锁：最大程度的支持并发处理(同时也会带来最大的锁开销)，作用和表锁类似，只不过作用对象换成了行；

#### 事务
概念：一组原子性的SQL语句，或者说一个独立的工作单元；事务中的所有SQL语句都能执行，则执行，其中任何一条无论什么原因无法执行，则全部执行失败；
备注：如果不显示的开启一个事务，则每条SQL语句都被当做一个事务，并自动提交

事务的ACID属性
原子性(atomicity)：一个事务必须被视为一个最小的不可分割的工作单元，整个事务要么全部提交成功，要么全部回滚；

一致性(consistency):数据库总是从一个一致性状态转移到另一个一致性状态；

隔离性(isolation):通常来说，一个事务所做的修改在最终提交之前，对其他事务是不可见的,当然这个根据隔离级别不同，有所不同；

持久性(durability)：一旦事务提交，其所做的修改就会永久保存在数据库中；


#### 隔离级别
```
隔离级别			脏读可能性			不可重复读可能性 		幻读可能性 		加锁读	
READ UNCOMMITTED 		YES 				YES 					YES 		NO
READ COMMITTED 			NO 					YES 					YES 		NO
REPEATABLE READ 		NO 					NO 						YES 		NO
SERIALIZABLE 			NO 					NO 						NO 			YES
```
备注：由上到下分别是 未提交读、提交读、可重复读(默认隔离级别)、可串行化
备注2：InnoDB储存引擎可以通过多版本并发控制(MVCC，行级锁的变种)解决幻读问题
备注3：设置隔离级别，如设置当前回话的隔离级别：
`SET SESSION TRANSACTION ISOLATION LEVEL READ COMMITED;`
备注4：事务是下层的储存引擎实现的，所以在同一个事务中不能使用多个储存引擎

#### 死锁
概念：两个或者多个事务对同一资源相互占用，请求锁定对方所占用的资源，从而导致恶性循环现象；
备注：InnoDB目前处理死锁方法是，将持有最少行排他锁的事务进行回滚

#### 储存引擎
A SQL语句：`SHOW TABLE STATUS LIKE 'yii_user' \G`
功能：显示表`yii_user`的表信息
备注：至于显示出的信息含义可以参考《高性能MySQL》pdf的50页；

B InnoDB 储存引擎
说明：该InnoDB 储存引擎基于聚簇索引，聚簇索引对主键查询有很高的性能，但是它的二级索引(即非主键索引)必须包含主键列，所以主键列很大的话，其他的所有索引都会很大。
所以，如果表中的索引很多，那么主键应当尽可能的小；
建议：阅读MySQL官方手册中的'InnoDB 事务模型和锁'一节；


### 第 4 章 Schema 与数据类型优化

7 优化数据类型
A 简单几个原则：
a 更小的通常更好，一般情况下，应该尽量采用正确储存数据的最小类型，如tinyint unsigned等；
b 简单就好，简单的数据类型的操作通常需要更少的CPU周期，例如，整型比字符操作的代价更低，锁用整型来存储日期、时间、IP地址；
c 尽量避免NULL，通常情况下最好指定NOT NULL;

B 整数类型：`TINYINT、SMALLINT、MEDIUMINT、INT、BIGINT`，分别使用8、16、24、32、64位储存，储存范围是 -2e(N-1) 到 2e(N-1)，其中N是储存空间位数；
备注：对于储存和计算来说，INT(1)和INT(10)是相同的，并没有太大区别；

C 实数类型：带有小数部分的数字
a FLOAT和DOUBLE类型支持使用标准的浮点运算进行近似运算；
b DECIMAL类型用于储存精确的小数。但是CPU是不支持DECIMAL直接计算的，MySQL自身实现了DECIMAL的高精度计算，相对而言，CPU支持原生浮点计算，所以浮点运算明显更快。MySQL5.0以及更高版本中DECIMAL类型允许最多65个数字。

建议：对于高精度的精确计算可以使用DECIMAL,但如果数据量比较大的话，可以采用BIGINT代替DECIMAL，只需要将货币根据小数位数乘以相应的倍数即可，如需要精确财务到万分之一，则可以将所有金额乘以一百万储存在BIGINT中即可，这样可以同时避免浮点数计算不精确和DECIMAL精确计算代价高的问题；

D 字符串类型
a VARCHAR 存储可变长度的字符串。、
使用范围：字符串列的长度变化比较大，列的更新很少(如果更新长度增大，则需要额外工作)；
备注：虽然 `VARCHAR(5)`和`VARCHAR(200)`存储'hello'的空间开销一样，但是更长的列会消耗更多内存，所以还是还是越小越好原则；

b CHAR 类型是定长的，根据定义的字符串长度分配足够的空间；
使用范围：储存较短的字符串，或者所有的值都接近同一个长度；
备注：字符串长度定义的是字符数，不是字节数；

E BLOG和TEXT类型：为储存很大的数据而设计的字符串数据类型，分别采用二进制和字符方式存储；
上述两种类型属于两个数据类型家族：
二进制型：`TINYBLOB、SMALLBLOB、BLOB、MEDIUMBLOB、LONGBLOB`
字符串类型：`TINYTEXT、SMALLTEXT、TEXT、MEDIUMTEXT、LONGTEXT;`
备注：BLOB是SMALLBLOB的同义词，TEXT是SMALLTEXT的同义词
使用范围：尽量避免使用这两种类型，无法避免，则需要在用到BLOB字段的地方都使用SUB`STRING(column,length)`将列值转化为字符串，这样就可以使用内存表了，但是要确保截取的字符串足够短，不会使临时表的大小超过max_heap_table_size或tmp_table_size，超过MySQL会将临时表转化为MyISAM磁盘临时表；

F 枚举类型：把一些不重复的字符串储存成一个预定义的集合；

G 日期和时间类型
a DATETIME 表示大范围的时间，从1001到9999年，精度为秒，与时区无关；(一般不采用)

b TIMESTAMP 表示时间戳，精度为秒，受时区影响，并且默认为NOT NULL；
建议：采用TIMESTAMP储存时间戳，最好别用整数储存时间戳；
建议2：如果需要储存微秒级时间戳，则可以采用BIGINT或者DOUBLE

H 位数据类型

备注：标识列(猜测：一个表的外键列)最好的类型是整数类型，尽量避免字符串作为外键列，并且还要确保关联的表中使用同样的数据类型；

备注2：一张表中不要使用太多的列，太多是指上千列；

备注3：MySQL限制每个关联操作最多只能有61张表，一个粗略的规则时，如果希望查询执行得快速并且并发好，单个查询最好在12张表内做关联；

8 范式和反范式
范式：在数据库中，每个事实数据会出现并且只出现一次；
反范式：在数据库中，信息是冗余的，可能存储在多个地方；

范式优点：
范式化的更新操作通常比反范式化要快；
范式化的表通常更小，更好的放在内存里，所以执行操作更快；
很少有多余的数据意味着检索列表数据时，更少用DISTINCT或者GROUP BY；

范式缺点：
通常查询语句需要关联，执行代价很高；

第一范式(1NF)：列(字段)是最小的的单元不可再分。

第二范式(2NF)：满足1NF前提下,非主键列(表中的非主键字段)必须完全依赖于全部主键而非部分主键。依赖理解为取决于，即字段值由主键列决定。

第三范式(3NF)：满足2NF前提下,非主键外的所有字段必须互不依赖，即不能实现依赖传递。

bc范式：bc范式是在第三范式的基础上的一种特殊情况，既每个表中只有一个候选键（在一个数据库中每行的值都不相同，则可称为候选键）。如果有多余候选键，则不满足bc范式。


备注：
缓存表：储存哪些比较简单的从schema其他表获取(但是每次获取的速度比较慢)的数据表；
汇总表：保存的是GROUP BY 语句聚合数据的表；

9 计数器表，如：统计网站访问量
A 建表
```
CREATE TABLE yii_daily_hit_counter(
	day date not null,
	slot tinyint unsigned not null,
	cnt int unsigned not null,
	primary key(day,slot)
) ENGINE=InnoDB;
```
B 插入数据
```
INSERT INTO yii_daily_hit_counter (day,slot,cnt)
VALUES (CURRENT_DATE,RAND()*100 ,1)
ON DUPLICATE KEY UPDATE cnt=cnt+1;
```
备注：ON DUPLICATE KEY UPDATE作用：如果主键或唯一索引不存在，则插入，如果主键或者唯一索引存在，更新 `cnt = cnt+1`

C 定期执行任务：定期将统计归为0槽(slot字段)
```
UPDATE yii_daily_hit_counter AS c
	INNER JOIN (
		SELECT day,SUM(cnt) AS cnt, MIN(slot) AS mslot
		FROM yii_daily_hit_counter
		GROUP BY day
	) AS x USING(day)
SET c.cnt = IF(c.slot = x.mslot,x.cnt,0),
	c.slot = IF(c.slot = x.mslot,0,c.slot);
DELETE FROM yii_daily_hit_counter WHERE slot != 0 AND cnt = 0;
```

4.5 加速ALTER TABLE 操作速度
A 需求：如果需要更改表的默认值
坏的选择:
```
mysql> ALTER TABLE file
-> MODIFY COLUMN rental_duration TINYINT(3) NOT NULL DEFAULT 5;
```
原因：要复制整张表进行操作；

好的选择：
```
mysql> ALTER TABLE file
-> ALTER COLUMN rental_duration SET DEFAULT 5;
```
原因：列的默认值实际是储存在.frm文件中，所以可以直接修改该文件，上行语句就是直接修改文件；

B 更改.frm文件来修改表结构
参考：该书PDF174页；



### 第 5 章 创建高性能的索引
暂时总结：查询语句的条件以及查询列尽量跟多列索引的列一样性能最好；

5.1 索引可以包含一个或者多个值，如果索引包含多个列，那么列的顺序也十分重要，因为MySQL只能高效的使用索引的左最前缀列(CREATE TABLE语句中定义索引内字段的顺序)，后面的列在前面的字段值依次相同时，再起到排序作用，所以如果不是按照索引的最左列开始查找，则无法使用索引；

注意：包含多个列的索引不能跳过索引中的列，进行查询，即要查询索引中后面的列，必须在SQL中包含该列左边的全部列(而且左边列还不能是范围查询，如LIKE)；

注意2：如果查询中有某个列的范围查询，则其右边的所有列都无法使用索引优化查找，例如有查询` WHERE last_name='Smith' AND first_name LIKE 'j%' AND dob = '1976-12-23'`，这个查询只能使用索引的前两列，因为这里的LIKE是一个范围查询(但是服务器可以把其余列用于其他目的)。如果范围查询列值的数量有限，那么可以通过使用多个等于条件来代替范围条件；

注意3：B-Tree索引对如下查询有效
以索引` KEY (last_name,first_name,dob)`为例
A 全职匹配
	全职匹配指的是索引中所有列进行匹配，例如前面提到的索引可用于查找姓名为Cuba Allen、出生于1960-01-01的人；
B 匹配最左前缀
	前面提到的索引可用于查找所有姓为Allen的人，即只使用索引的第一列；
C 匹配列前缀
	也可以只匹配某一列的值的开头部分。例如前面提到的索引可用于查找所有以J开头的姓的人。这个条件就满足上面的注意2了；
D 匹配范围值
	例如前面提到的索引可用于查找姓在Allen 和 Barraymore之间的人。
E 精确匹配某一列并范围匹配另一列
	前面提到的索引也可用于查找所有姓为Allen，并且名字是K开头的人。即第一列last_name 全匹配，第二列first_name 范围匹配（也要注意2的情况）。
F 只访问索引的查询
	查询的判断条件和查询的字段都是在一个多列索引中，也叫覆盖索引；


5.2 索引的优点
A 索引大大减少了服务器需要扫描的数据量；
B 索引可以帮助服务器避免排序和临时表；
C 索引可以将随机的I/O变为顺序I/O；
备注：最常见的B-Tree索引，按照顺序存储数据，所以MySQL可以用来做ORDER BY 和GROUP BY的操作，这句话的意思也潜在说明，ORDER BY 和GROUP BY 后的字段要添加索引；

备注2：索引适用与中大型表，小型表不需要建立索引，而特大型表则需要分区技术，对于表的数量特别多的系统，可以建立元数据信息表

5.3.1 独立的列：索引列不能是表达式的一部分，也不能是函数的参数
例：
`mysql> SELECT actor_id FROM sakila.actor WHERE actor_id + 1 = 5;`//这种查询无法使用actor_id列索引
例2：
`mysql> SELECT ... WHERE TO_DAYS(CURRENT_DATE) - TO_DAYS(date_col) <= 10;`

5.3.2 前缀索引和索引选择性
A 索引的选择性
概念：不重复的索引值(也称为基数)和数据表的记录总数的比值。索引的选择性越高则查询效率越高，如唯一索引选择性是1，这是最好的索引选择性，性能也最好；

B 
a 计算前缀的选择性和完整列的选择性
```
mysql> SELECT COUNT(DISTINCT LEFT(city,3))/COUNT(*) AS sel3,
	   COUNT(DISTINCT LEFT(city,4))/COUNT(*) AS sel4,	
	   COUNT(DISTINCT LEFT(city,5))/COUNT(*) AS sel5,	
	   COUNT(DISTINCT LEFT(city,6))/COUNT(*) AS sel6,	
	   COUNT(DISTINCT LEFT(city,7))/COUNT(*) AS sel7,	
	   COUNT(DISTINCT city)/COUNT(*) AS reality
	FROM sakila.city_demo;
```
备注：同时显示取前缀长度为3、4、5、6、7以及真实长度的索引选择性比较，越接近的好(当然还是应该考虑前缀长度不易过长)；

b 判断数据是否分布均匀，如上述a中结果前缀为7时，接近于真实的选择性，则再查看前缀7的索引具体数据与真实的具体数据对比；
先获取前缀为7长度的索引数据记录数
```
mysql> SELECT COUNT(*) AS cnt, LEFT(city,7) AS pref 
		FROM sakila.city_demo GROUP BY pref ORDER BY cnt DESC LIMIT 5;
```
在获取真实的索引数据记录数
```
mysql> SELECT COUNT(*) AS cnt, city
		FROM sakila.city_demo GROUP BY city ORDER BY cnt DESC LIMIT 5;
```
备注：将两者数据进行对比，是否真实接近，接近则可以建立前缀索引了，否则，需要加大前缀长度，再次进行；

c 建立前缀索引
```
mysql> 	ALTER TABLE sakila.city_demo ADD KEY (city(7));
```

5.3.3 多列索引
tip：把WHERE条件里面的列都建立索引是不可取的；
tip：在多个列上都建立独立的单索引大部分情况下，并不能提高MySQL的查询性能；

备注：可以使用EXPLAIN分析查询语句，例如：
```
mysql> EXPLAIN SELECT film_id,actor_id FROM sakila.film_actor
WHERE actor_id = 1 OR film_id =1 \G
```

重点：
MySQL能将查询的多个单列索引合并，但这只是一种优化结果，在实际上更多的时候说明表的索引建的很糟糕
a 当出现服务器对多个索引做相关操作时(通常为多个AND条件)，通常意味着需要一个包含所有相关列的多列索引，而不是多个独立的单独列索引。

b 当服务器需要对多个索引联合操作时(通常有多个OR条件)，通常需要耗费大量CPU和内存资源在算法的缓存、排序和合并操作上。特别是当其中有些索引的选择性不高，需要合并扫描返回的大量数据的时候。

c 更重要的是，优化器不会把这些计算到‘查询成本’中，优化器只关心随机页面读取。这会使得查询的成本被'低估'，导致该执行计划还不如直接走全表扫描。这样做不但会消耗更多的CPU和内存资源，还可能会影响查询的并发性，但如果是单独运行这样的查询则往往会忽略并发性的影响。
通常来说，还不如将查询改为union的方式往往更好；

5.3.4 选择合适的索引顺序(适用于B-Tree)
A 建立索引顺序，例：
创建一个(staff_id,customer_id)的索引，还是(customer_id，staff_id)的索引
第一步：获取要建立索引列的选择性，选择选择性更高的列放在左边(大方向)
```
mysql> 	SELECT COUNT(DISTINCT staff_id)/COUNT(*),
	   		COUNT(DISTINCT customer_id)/COUNT(*)
		FROM payment;
```
第二步：再具体查看具体列的值分布，如果某值的记录数过多(比如超过十万，那索引效果基本没有)
```
mysql> 	SELECT staff_id,COUNT(*) FROM payment GROUP BY staff_id;
```
结果：
staff_id 	COUNT(*)
1 			20
2 			50
3 			500000
...
分析：staff_id为3时，有500000条记录(现实中也可能出现这种情况，比如管理应用账号)，这样索引作用几乎没有了，出现这种情况，采取的措施是更改应用程序代码，并把staff_id对应项再做细分；

5.3.5 簇族索引：并不是一种单独的索引类型，而是一种数据储存方式。数据按聚簇索引键值依次存放在索引的叶子页中。
猜测：InnoDB会自动优先选择主键作为作为聚簇索引，如果没有设置主键，则会选择一个唯一的非空索引代替。如果没有这样的索引，InnoDB会隐式定义一个主键来作为聚簇索引；
一个表只能有一个聚簇索引（不过覆盖索引可以模拟多个聚簇索引）

A 聚簇索引优点：主要提高查询速度，具体查看pdf 199页；

B 聚簇索引缺点：二级索引(非聚簇索引)可能比想象的更大，因为二级索引的叶子节点包含了引用行的主键列，具体查看pdf 199页；

额外知识：如果表中有部分数据被删除，MySQL本身不会自动回收已有空间，需要我们执行命名：
`OPTIMIZE TABLE table_name;`//进行优化，有点像360碎片整理，频率一周一次，或一月一次；

备注：顺序的主键什么时候会造成更坏的结果？
答：对于高并发工作负载，在InnoDB 中按主键顺序插入可能会造成明显的争用。主键的上界会成为'热点'。因为所有的插入都发生在这里，所以并发插入可能会导致间隙锁竞争。另外一个热点可能是AOTU_INCREMENT 锁机制，如果遇到这个问题可能需要重新考虑设计表或者应用，或者更改innodb_autoinc_lock_mode配置。

5.3.6 覆盖索引
概念：一个索引(一般为多列索引)包含(或者说覆盖)所有需要查询的字段值，我们就称之为“覆盖索引”；
功能：能够极大的提高索引效率，因为只需要扫描索引，而无需回到表；
备注：并不是所有类型的索引都可以成为覆盖索引，如：哈希索引、空间索引、全文索引都不存储索引列的值，所有不能用于覆盖索引；

5.3.7 使用索引扫描来做排序：利用索引本身的排序进行排序
满足下面条件中任意一条即可
A 第一种：索引的顺序和ORDER BY 字句的顺序完全一致，并且所有列的排序方向(倒序或者正序)都统一时，MySQL会自动使用索引对结果进行排序。如果查询需要关联多张表，则只有当ORDER BY字句引用的字段全部为第一张表时，才能使用索引做排序。ORDER BY 字句和查找型查询的限制一样：需要满足索引的最左前缀的要求，否则，MySQL需要执行排序操作，而无法利用索引排序。

B 第二种：前导列为常量的时候。如果WHERE或者JOIN字句对这些列指定了常量，就可以弥补索引的不足；
例：
`表索引为 UNIQUE KEY rental_date (rental_date,inventory_id,customer_id)`

下面两种都可以利用索引来排序(SELECT 子句中字段可以不是索引中字段)
a `... WHERE rental_date = '2015-05-05' ORDER BY inventory_id DESC;`
分析：WHERE子句提供常量条件，而使用了第二列进行排序，将两列组合在一起就形成了索引的最左前缀；

b `... WHERE rental_date > '2015-05-05' ORDER BY rental_date,inventory_id;`
分析：因为ORDER BY 子句的字段是索引的最左前缀，所以可以，除了这两种情况，其他的都不行；
具体例子参考pdf 213页


5.3.8 压缩（前缀压缩）索引
关于MyISAM储存引擎的；

5.3.9 冗余和重复索引
A 重复索引：在相同列上按照相同顺序创建的相同索引；
例：
 ...
 UNIQUE (ID),
 INDEX (ID),
 ...
 备注：主键索引、唯一索引都是通过索引实现的，所以算相同索引，如果不同索引类型，并不算重复索引，如：创建KEY(col)和FULLTEXT KEY(col)两种索引；

 B 冗余索引
 分析：如果创建了索引(A,B)，再创建索引(A)就是冗余索引，因为这只是前一个索引的前缀索引。因此索引(A,B)也可以当作索引(A)来使用（这种冗余只对B-Tree来说的）。但是如果再创建索引(B,A)，则不是冗余索引，索引(B)也不是，因为索引(B)不是索引(A,B)的最左前缀列。另外，其他类型的索引(例如哈希索引或者全文索引)也不会是B-Tree索引的冗余索引，而无论覆盖的索引列是什么；

 备注：扩展索引时不用扩展主键列，因为主键列本来就在二级索引里。所以在列(A)上的索引就相当于在(A,ID)上的索引(ID是主键)。如果有像WHERE A = 5 ORDER BY ID 这样的查询，这个索引会很有用。但如果将索引扩展为(A,B),则实际变成了(A,B,ID)，那么上面的查询的ORDER BY 子句就无法使用该索引做排序，而只能用文件排序了。所以，建议使用Percona工具箱中的pt-upgrade工具来仔细检查计划中的索引变更；

 5.3.10 未使用索引
 通过两个工具来定位未使用的索引，在Percona Server 或者 MariaDB中先打开userstates服务器变量(默认是关闭的)，然后让服务器正常运行一段时间，再通过查询INFORMATION_SCHEMA.INDEX_STATISTICS就能查询到每个索引的使用频率；
 另外，还可以使用Percona Toolkit 中的pt-index-usage,该工具可以读取查询日志，并对日志中的每条查询进行EXPLAIN操作，然后打印出关于索引和查询的报告；

 5.3.11 索引和锁
 索引可以让查询锁定更少的行。如果你的查询从不访问那些不需要的行，那么就会锁定更少的行，从两方面来看，这对性能都有好处；(说明索引在一个层次，真实的锁跟实际的数据又在另一个层次)

5.4.1 支持多种过滤条件
前要说明：在一个索引中，一个字段范围查询后，索引声明中的右边字段将无法再使用索引；
特别注意：IN()却不算范围，所以索引某个字段采用in(如：`sex in ('F','M')`)，索引声明中sex右边字段还可以继续使用索引；

A 创建一个过滤多个条件的索引，如`(sex,country,age)`;
说明：如果搜索条件中没有指定sex值，则可以用In()绕过；
如：WHERE sex in('F','M') AND country in('china','america') AND age between 18 and 25;
说明：age其实也可以用age in(18,19,20,21,22,23,24,25)代替
备注：in()虽然好，但是也不要滥用，因为一条查询语句中的所有in()中的可能都被会优化器转化为一种组合，如：上面语句会被转化为2*2=4种组合，组合数最好不要上千个；


5.4.2 避免多个范围条件
例：`WHERE last_online > DATE_SUB(NOW(),INTERVAL 7 DAY) AND age BETWEEN 18 AND 25;`
说明：这种两个条件没法使用索引，所以再创建一个active字段，通过定时任务来判断在最近7天是否登录，所以字段active只有俩值，可以用`in()`来代替了,`in()`是'多个等值条件查询'；

5.4.3 优化排序
A 对于选择性比较低的列，可以增加一些特殊索引来做排序。例如，可以创建(sex,rating)索引用于下面的查询：
`mysql> SELECT <cols> FROM profiles WHERE sex='M' ORDER BY rating LIMIT 10;`

问题：如果查询偏移量很大时，查询就会出现严重性能问题；如：
`mysql> SELECT <cols> FROM profiles WHERE sex='M' ORDER BY rating LIMIT 100000,10;`
解决方法一：反范式、预先计算和缓存；
解决方法二：限制用户能够偏移的数量，如天猫的通过翻页最多显示6000个，其实用户体验也行；(采用)
解决方法三：使用延迟关联，先使用索引获取查询主键，再关联原表获取需要的行，例：
```
mysql> SELECT <cols> FROM profiles INNER JOIN(
		SELECT <primary key cols> FROM profiles
		WHERE x.sex='M' ORDER BY rating LIMIT 100000,10
	) AS x USING(<primary key cols>);
```

备注：
```
mysql> SHOW INDEX FROM table_name; //展示表中的的索引
mysql> DROP INDEX 索引名 ON 表名; 	//删除表中具体索引；
```

总结：
'三星系统'：索引将相关记录放在一起获得一星；如果索引中的数据顺序和查找中的排列顺序一致获得一星；如果索引中列包含了查询中需要的全部列（覆盖查询）再获得一星;

猜测：查询流程是先在索引中排查出需要的行(即得到行的主键)，再回到表中根据得到的行主键依次(注意，特别适用于排序)寻找具体的行；

猜测2：
最优化查询：覆盖索引查询，根本不用回到具体的表中去查找具体的行，所以最快；

猜测3：如果必须要回到具体的表中去查找具体的行，那么也先要用索引，把需要查询的行范围缩小，即使先获取到缩小范围的主键，再和原表连接查询也可以，如：
```
mysql> SELECT <cols> FROM profiles INNER JOIN(
		SELECT <primary key cols> FROM profiles
		WHERE x.sex='M' ORDER BY rating LIMIT 100000,10
	) AS x USING(<primary key cols>);
```
待定：这是优化的选择，可以看出LIMIT 子句的执行在储存引擎中执行的，不是在索引中(除覆盖索引)；



### 第 6 章 查询性能优化
6.2 低效查询分析步骤
A 确认应用程序是否在检索大量超过需要的数据。这通常意味着访问了太多的行，但有时候也可能是访问了太多的列；

B 确认MySQL服务器层是否在分析大量超过需要的数据行；


6.2.1 是否向数据库请求了不需要的数据
错误现象：
A 查询了不需要的记录
解决：加LIMIT 子句

B 多表关联返回了全部列，即返回`SELECT * FROM .. JOIN .;`
解决：具体写出需要的列，如：`SELECT a.name,b.* FROM ...`
备注：有些时候有缓存，当然也可以取出多的列；

C 重复查询相同的数据

6.2.2 MySQL是否扫描额外的记录

A MySQL应用WHERE 条件的三种方式，从好到坏依次为：
a 在索引中使用WHERE 条件来过滤不匹配的记录。这是储存引擎层完成的。猜测：如WHERE 主键 = '';在explain中Extra的值显示NULL；

b 使用索引覆盖扫描(在Extra列中出现 Using index)来返回记录，直接从索引中过滤不需要的记录并返回命中结果。这是MySQL服务器层完成的，但无需再回到表查询记录。

c 从数据表中返回数据，然后过滤不满足条件的记录(在Extra列中出现Using Where)。在MySQL服务器层完成，MySQL需要先从数据库表读出记录然后过滤；

6.3.2 切分查询：将大查询切分成小查询，每个查询功能完全一样，只完成一小部分，每次只返回一部分查询结果；
如：定期删除大量数据
`mysql> DELETE FROM messages WHERE created < DATE_SUB(NOW(),INTERVAL 3 MONTH);`
可以分割为：
```
rows_affected = 0
do {
	rows_affected = do_query(
		"DELETE FROM messages WHERE created < DATE_SUB(NOW(),INTERVAL 3 MONTH) LIMIT 10000"
	)
} while rows_affected > 0 
```
分析：一次删除一万行，这样分开执行；

6.3.3 分解关联查询
例：
```
mysql>  SELECT * FROM tag
			JOIN tag_post ON tag_post.tag_id = tag.id
			JOIN post ON tag_post.post_id = post.id
		WHERE tag.tag='mysql';
```
可以分解成：
```
mysql> 	SELECT * FROM tag WHERE tag='mysql';
mysql> 	SELECT * FROM tag_post WHERE tag=1234;//1234是上一个查询获取到的；
mysql> 	SELECT * FROM post WHERE post.id in (123,456,567,9098,8904);
```
//`in()`中是上一个查询获取到的；
分析：分解的优点
a 让缓存效率更高；
b 将查询分解后，执行单个查询可以减少锁的竞争；
c 在应用层做关联，可以更容易对数据库进行拆分，更容易做到高性能和可扩展；
d 查询本身效率也可能会有所提升。(最好后面自己测试一下，从关联表变为in()方式看，可能会提升)
e 可以减少冗余记录的查询；

将关联查询分解的场景：
a 当应用能够方便地缓存单个查询的结果的时候；
b 当可以将数据分布到不同的MySQL服务器上的时候；
c 当能够使用in()的方式来代替关联查询的时候；
d 当查询能够使用同一个数据表的时候；


6.4.1 MySQL客户端/服务器通信协议

A php查询例子
```
<?php
	$link = mysql_connect('localhost','user','p4ssword');
	//这里php函数已经将全部结果都缓存到内存了，如果数据很大，很消耗内存和cpu
	$result = mysql_query('SELECT * FROM HUGE_TABLE',$link);
	while ($row = mysql_fetch_array($result)){
		//do something
	}
```
下面php函数不缓存结果到内存
```
<?php
	$link = mysql_connect('localhost','user','p4ssword');
	$result = mysql_unbaffered_query('SELECT * FROM HUGE_TABLE',$link);
	while ($row = mysql_fetch_array($result)){
		//do something
	}
```

6.4.3 查询优化处理
A 查询花费检查
```
mysql> 	SELECT SQL_NO_CACHE COUNT(*) FROM sakila.film_actor;
mysql> 	SHOW STATUS LIKE 'Last_query_cost';//显示上一条查询做了多少数据页查询；
```
B MySQL关联执行策略
	MySQL对任何关联都执行嵌套循环关联操作，即MySQL先在一个表中循环取出单条数据，然后再嵌套循
环到下一个表中寻找匹配的行，依次下去，直到找到所有表中匹配的行为止，然后根据各个表匹配的行，返回查询中需要的各个列。MySQL会尝试在最后一个关联表中找到所有匹配的行，如果最后一个关联表无法找到更多的行以后，MySQL会返回上一层次关联表，看是否能找到更多的匹配记录，以此类推迭代执行。(这只是最基本的执行，其实MySQL还做了很多优化工作)
例：
```
mysql> 	SELECT tbl1.col1,tbl2.col2
		FROM tbl1 INNER JOIN tbl2 USING(col3)
		WHERE tbl.col1 IN(5,6);
```
假设MySQL按照查询中的表顺序进行关联操作，我们则可以用下面的伪代码表示MySQL将如何完成这个查询
```
//可以看出是从一个表中把所有符合条件的记录都取出来
outer_iter = iterator over tbl1 where col1 IN(5,6) 	
outer_row = outer_iter.next
while outer_row 				//循环
	inner_iter = iterator over tbl2 where col3 = outer_row.col3;
	inner_row = inner_iter.next
	while inner_row
		output [ outer_row.col1,inner_row.col2 ] 	//输出
		inner_row = inner_iter.next
	end
	outer_row = outer_iter.next
end
```
上面的执行计划对于单表查询和多表关联查询都适用，如果是一个单表查询，那么只需要完成上面的外层基本操作。对于外连接，上面的执行过程仍然适用。例如我们将上面查询修改如下：
```
mysql> 	SELECT tbl1.col1,tbl2.col2
		FROM tbl1 LEFT OUTER JOIN tbl2 USING(col3)
		WHERE tbl.col1 IN(5,6);
```
伪代码只有一点点不同
```
outer_iter = iterator over tbl1 where col1 IN(5,6) 	
outer_row = outer_iter.next
while outer_row 				//循环
	inner_iter = iterator over tbl2 where col3 = outer_row.col3;
	inner_row = inner_iter.next
	if inner_row		//不同
		while inner_row
			output [ outer_row.col1,inner_row.col2 ] 	//输出
			inner_row = inner_iter.next
		end
	else 				//不同
		output [outer_row.col1,NULL]    //不同
	end 				//不同
	outer_row = outer_iter.next
end
```

C MySQL处理文件排序方式
a 如果ORDER BY 子句中的所有列都来自关联的第一个表，那么MySQL在关联处理第一个表的时候就会进行文件排序。如果是这样，那么在MySQL的EXPLAIN结果中可以看到Extra字段会有"Using filesort"。

b 除a之外的所有情况，MySQL都先将关联的结果存放到一个临时表中，然后在所有的关联都结束后，再进行文件排序，这种情况下在MySQL的EXPLAIN结果中可以看到Extra字段会有"Using temporary;Using filesort"。如果查询中有LIMIT 的话， LIMIT 也会在排序之后应用，所以即使范围较少的数据，临时表和需要排序的数据量仍然会非常大(MySQL5.6改善了这一点，先只返回满足条件的记录)。


6.5 MySQL查询优化器的局限性

6.5.1 关联子查询

A 糟糕的查询案例：WHERE 条件中的`IN()`里的子查询语句；
例：
```
mysql> 	SELECT * FROM sakila.film
		WHERE film_id IN(
			SELECT film_id FROM sakila.film_actor WHERE actor_id = 1
		);
```
备注：因为MySQL的优化器会'优化'到一种极其糟糕的'优化'方案；

改进方案：改进为连接查询
```
mysql> 	SELECT film.* FROM sakila.film
			INNER JOIN sakila.film_actor USING(film_id)
		WHERE acotr_id = 1;
```
另一个改进方案：
```
mysql> 	SELECT * FROM sakila.film
		WHERE EXISTS(
			SELECT * FROM sakila.film_actor WHERE actor_id=1
				AND film_actor.film_id = film.film_id);
```
6.5.2 UNION 的限制
说明：有时，MySQL无法将限制条件从外层'下推'到内层，这使得原本能够限制部分返回结果的条件无法应用到内层查询的优化上。
解决方法：在每个子查询上都写上限制条件
例：
```
mysql> 	(SELECT first_name,last_name
		FROM sakila.actor
		ORDER BY last_name)
		UNION ALL
		(SELECT first_name,last_name
		FROM sakila.customer
		ORDER BY last_name)
		LIMIT 20;
```
说明：这条查询将会把actor中的200条记录和customer表中的599条记录存放在一个临时表中，然后再从临时表中取出前20条。可以通过在UNION的两个子查询中分别加上 LIMIT 20来减少临时表中的数据；
```
mysql> 	(SELECT first_name,last_name
		FROM sakila.actor
		ORDER BY last_name
		LIMIT 20)
		UNION ALL
		(SELECT first_name,last_name
		FROM sakila.customer
		ORDER BY last_name
		LIMIT 20)
		LIMIT 20;
```
6.5.9 在同一个表上查询和更新
说明：MySQL不允许对同一个表同时进行查询和更新；（即不允许同时改、查）
例：下面语句无法运行
```
mysql> 	UPDATE tbl AS outer_tbl
			SET cnt = (
				SELECT count(*) FROM tbl AS inner_tbl
				WHERE inner_tbl.type = outer_tbl.type
			);
```
解决方法：可以更改为
```
mysql> 	UPDATE tbl
			INNER JOIN(
				SELECT type,count(*) AS cnt
				FROM tbl
				GROUP BY type
			) AS der USING(type)
		SET tbl.cnt = der.cnt;
```
备注：子查询被认为是一个临时表，感觉同时查询和更新很有用哦；

6.7 优化特定类型的查询

6.7.1 优化`COUNT()`查询
`COUNT()`函数两个作用：统计某个列值的数量，另一个是统计行数
说明：统计列值时要求列值时非空的(不统计NULL)。如果在COUNT()的括号中指定列或者列的表达式，则统计的就是这个表达式有值的结果数。
说明2：统计行数时，直接使用`COUNT(*)`， 意义清晰，效果也很好；

说明3：根据条件计数，可以采用可以采用SUM(条件)，而不用COUNT()；
如：`SELECT SUM(code='AA') FROM yii_country;`

6.7.2 优化关联查询(连接查询优化)
A 确保ON或者USING子句中的列上有索引。在创建索引的时候就要考虑到关联的顺序。当表A和表B 用到列c关联的时候，如果优化器的关联顺序是B、A，那么就不需要在B表的对应列上建立索引。没有用到的索引只会带来额外的负担。一般来说，除非有其他的理由，否则只需要在关联顺序中的第二个表的相应列上建立索引。

特别注意：
情况一：`select A.\*，B.\* from A left join B ON A.id=B.id where A.name="hehe";`
左表A(不一定是驱动表)，id字段和name字段必须建立索引，**B表id必须建立索引**
mysql选择的驱动表为A表

情况二：`select A.\*，B.\* from A left join B ON A.id=B.id where B.name="hehe";`
左表的id字段必须建立索引，右表字段name必须建立索引，B  表id可以不建立索引
mysql选择的驱动表为B表，可以看出mysql参考了where后面的索引，决定了哪个是驱动表

情况三：`select A.\*，B.\*, C.\* from A left join B ON A.id=B.id ON A.id=C.id where A.name="hehe";`
左表A表字段id和name都必须建立索引，表B和表C的id字段都必须建立索引
mysql选择的驱动表为A表

情况四：`select A.\*，B.\*, C.\* from A left join B ON A.id=B.id ON B.id=C.id where A.name="hehe";`
左表A表字段id和name都必须建立索引，表B和表C的id字段都必须建立索引
mysql选择的驱动表为A表，说明其实多表连接查询后面的ON等于条件，同第一个ON一样

情况五：`select A.\*，B.\*, C.\* from A left join B ON A.order_id=B.id ON A.track_id=C.id where A.name="hehe";`
A表并不需要在order_id,track_id上建立索引，只需要在name字段建立索引，而B表和C表都需要在各自表的id上建立索引

备注1：这两种情况已经测试过了

备注2：
inner join 自动选择join顺序，可能不是最优
left join 或者 straight_join强制左边的表作为驱动表
right join 则相反
多表join中，排序列如果不属于驱动表，则无法利用索引完成排序；

备注3：

- 始终要记住的就是能通过筛选返回的结果集较小的表适合做驱动表
- explain 显示结果的第一个表为驱动表

B 确保任何ORDER BY 和GROUP BY 中的表达式 **只涉及到同一个表中的列**，这样MySQL才有可能使用索引来优化这个过程；

结论1：连接查询ON等式左右两边字段都必须建立索引，只要是关联字段就建立索引(虽然有时用不到，但是直接默认建立)

结论2：ORDER BY 和 GROUP BY 后面字段尽量是同一个表的字段



6.7.2 优化子查询
建议：把子查询尽可能用关联查询代替；


优化GROUP BY WITH ROLLUP最好的方法是将WITH ROLLUP功能转移到应用程序中处理；


6.7.5 优化LIMIT分页

问题：当LIMIT 加上偏移量很大的时候，性能下降，如：LIMIT 10000,20 这是MySQL需要查询10020条记录，然后只返回最后20条记录，前面的10000条记录都会被抛弃，这样的代价很高；
常规写法：
`mysql>     SELECT film_id,description FROM sakila.film ORDER BY title LIMIT 10000,20;`
优化写法：
`mysql>     SELECT film_id,description FROM sakila.film INNER JOIN (SELECT film_id FROM sakila.film ORDER BY title LIMIT 10000,20) AS lim USING(film_id);`
优化原理：先利用覆盖索引找出，找出需要的记录，然后再和原表关联操作一次，找出需要的列；

6.7.6 优化SQL_CALC_FOUND_ROWS
说明：SQL_CALC_FOUND_ROWS 通过提示可以得到总记录数，但是这个效率极其低下，不采用；
优化方法：a 一次取出大量数据，做缓存，当然这个也就知道了记录数；b 直接使用count(*);

6.7.7 优化UNION查询
说明：很多优化策略在UNION查询中都没法很好地使用，经常需要手工地将WHERE 、LIMIT 、ORDER BY等子句'下推'到UNION 的各个子查询中。
说明2：除非确实需要消除重复的行，否则就一定要使用UNION ALL,这一点很重要；如果没有关键字ALL，MySQL会给临时表加上DISTINCT选项，这会导致临时表做唯一性检查，代价会非常高；

额外知识：优化where 条件中的 or 
A 情况一：如果能转化为 IN()，那么采用IN()代替；in和or的效率下定义的时候，应该再加上一个条件，就是所在的列是否有索引或者是否是主键。如果有索引或者主键性能没啥差别，如果没有索引，in()的效率就会高很多；

B 情况二：如果 or 两边的字段上有索引，则在情况一不满足时，将查询语句转化为UNION (有没有ALL，根据情况而定)；

C 情况三：不满足情况一，or左右两边字段又没有索引，则保持原样or；

额外知识2：一切的  != 等等之类的写法都会导致全表扫描；


6.8.1 使用MySQL构建队列表

A 在任何情况下，都不要使用SELECT FOR UPDATE；原因：应该是会表锁，会阻塞；



### 第 7 章 MySQL 高级特性

7.1 分区：在物理上将一个表对应的文件，分割成多个小文件，甚至可以储存到其他磁盘；
猜测：分区应该就是水平分表，垂直分表，通过主键关联并不是分区；
注释：分区适用于一个表的所有数据和索引；不能只对数据分区而不对索引分区，反之亦然，同时也不能只对表的一部分进行分区。

分区细节：
一个表最多只能有1024个分区；
分区表达式必须是整数，或者返回整数的表达式。
MySQL主键的限制，判断分区的标准列(无论该列是否在函数中)，要么在主键/unique key 中被包括，要么不设置主键和unique key;问题：标准列为主键索引第二列，分区能不能判断出来？猜测：能；
分区表中无法使用外键约束；

TIP:分区表是关联操作的第二张表，且关联条件是分区键，MySQL就只会在对应的分区里匹配行；

A 分区优点：
a 与单个磁盘或文件系统分区相比，可以存储更多的数据。
b 对于那些已经失去保存意义的数据，通常可以通过删除与那些数据有关的分区，很容易地删除那些数
据。
c 查询可以得到极大的优化，这主要是借助于满足一个给定WHERE 语句的数据可以只保存在一个或
多个分区内，这样在查找时就不用查找其他剩余的分区。

B 分区类型
a RANGE 分区：基于属于一个给定连续区间的列值，把多行分配给分区。按照RANGE分区的表是通过如下一种方式进行分区的，每个分区包含那些分区表达式的值位于一个给定的连续区间内的行。这些区间要连续且不能相互重叠，使用VALUES LESS THAN操作符来进行定义。
例：
```
CREATE TABLE employees (
id INT NOT NULL,
fname VARCHAR(30),
lname VARCHAR(30),
hired DATE NOT NULL DEFAULT '1970-01-01',
separated DATE NOT NULL DEFAULT '9999-12-31',
job_code INT NOT NULL,
store_id INT NOT NULL
)
PARTITION BY RANGE (store_id) (
	PARTITION p0 VALUES LESS THAN (6),
	PARTITION p1 VALUES LESS THAN (11),
	PARTITION p2 VALUES LESS THAN (16),
	PARTITION p3 VALUES LESS THAN MAXVALUE
);
```
说明：MAXVALUE 表示最大的可能的整数值。现在，store_id列值大于或等于16（定义了的最高值）的
所有行都将保存在分区p3中。在将来的某个时候，当商店数已经增长到25, 30, 或更多，可以使用ALTER TABLE语句为商店21-25, 26-30,等等增加新的分区

例2：已验证
```
CREATE TABLE yii_ts (
	id INT auto_increment,
	purchased tinyint, 
	primary key (id,purchased)   //标记1
) ENGINE=InnoDB CHARSET=utf8
PARTITION BY RANGE(purchased) (
	PARTITION p0 VALUES LESS THAN (50) 
		DATA DIRECTORY = 'e:/data' 
		INDEX DIRECTORY = 'e:/idx', //标记2
	PARTITION p1 VALUES LESS THAN (100),
	PARTITION p3 VALUES LESS THAN MAXVALUE
);
```
标记1说明：MySQL主键的限制，判断分区的标准列(无论该列是否在函数中)，要么在主键/unique key 中被包括，要么不设置主键和unique key;
注： 在Windows中将忽略DATA DIRECTORY和INDEX DIRECTORY选项。

b LIST 分区：类似于按RANGE分区，区别在于LIST分区是基于列值匹配一个离散值集合中的某个值来进
行选择。
例：
```
CREATE TABLE employees (
id INT NOT NULL,
fname VARCHAR(30),
lname VARCHAR(30),
hired DATE NOT NULL DEFAULT '1970-01-01',
separated DATE NOT NULL DEFAULT '9999-12-31',
job_code INT,
store_id INT
)
PARTITION BY LIST(store_id)
PARTITION pNorth VALUES IN (3,5,6,9,17),
PARTITION pEast VALUES IN (1,2,10,11,19,20),
PARTITION pWest VALUES IN (4,12,13,14,18),
PARTITION pCentral VALUES IN (7,8,15,16)
)；
```
备注：删除某个分区可以使用查询“ALTER TABLE employees DROP PARTITION pWest；”
备注2：如果试图插入列值（或分区表达式的返回值）不在分区值列表中的一行时，那么“INSERT”查询将失败并报错(没有如“VALUES LESS THAN MAXVALUE”这样的包含其他值在内的定义)。例如，假定LIST分区的采用上面的方案，下面的查询将失败：
```
INSERT INTO employees VALUES
(224, 'Linus', 'Torvalds', '2002-05-01', '2004-10-12', 42, 21);
```
c HASH分区：基于用户定义的表达式的返回值来进行选择的分区，该表达式使用将要插入到表中的这些
行的列值进行计算。主要用来确保数据在预先确定数目的分区中平均分布。在RANGE和LIST分区中，必须明确指定一个给定的列值或列值集合应该保存在哪个分区中；而在HASH分区中，MySQL 自动完成这些工作，你所要做的只是基于将要被哈希的列值指定一个列值或表达式，以及指定被分区的表将要被分割成的分区数量。
	要使用HASH分区来分割一个表，要在CREATE TABLE 语句上添加一个“PARTITION BY HASH (expr)
”子句，其中“expr”是一个返回一个整数的表达式。它可以仅仅是字段类型为MySQL整型的一列的名字。此外，你很可能需要在后面再添加一个“PARTITIONS num”子句，其中num是一个非负的整数，
它表示表将要被分割成分区的数量。
例：
```
CREATE TABLE employees (
id INT NOT NULL,
fname VARCHAR(30),
lname VARCHAR(30),
hired DATE NOT NULL DEFAULT '1970-01-01',
separated DATE NOT NULL DEFAULT '9999-12-31',
job_code INT,
store_id INT
)
PARTITION BY HASH(store_id)
PARTITIONS 4;
```
备注：“expr”还可以是MySQL 中有效的任何函数或其他表达式，只要它们返回一个既非常数、也非随机数的整数。
（换句话说，它既是变化的但又是确定的）。但是应当记住，每当插入或更新（或者可能删除）一行，这个表达式都要计算一次；这意味着非常复杂的表达式可能会引起性能问题，尤其是在执行同时影响大量行的运算（例如批量插入）的时候。
备注2：
使用了“PARTITION BY HASH”时，MySQL将基于用户函数结果的模数来确定使用哪个编号的分区。换句话，
对于一个表达式“expr”，将要保存记录的分区编号为N ，其中“N = MOD(expr, num)”，即取余数。例如，假定表t1 定义
如下，它有4个分区：
```
CREATE TABLE t1 (col1 INT, col2 CHAR(5), col3 DATE)
PARTITION BY HASH( YEAR(col3) )
PARTITIONS 4;
```
如果插入一个col3列值为'2005-09-15'的记录到表t1中，那么保存该条记录的分区确定如下：
```
MOD(YEAR('2005-09-01'),4)
= MOD(2005,4)
= 1
```
备注3：LINEAR HASH分区
线性哈希分区和常规哈希分区在语法上的唯一区别在于，在“PARTITION BY” 子句中添加“LINEAR”关键字，如
下面所示：
```
CREATE TABLE employees (
id INT NOT NULL,
fname VARCHAR(30),
lname VARCHAR(30),
hired DATE NOT NULL DEFAULT '1970-01-01',
separated DATE NOT NULL DEFAULT '9999-12-31',
job_code INT,
store_id INT
)
PARTITION BY LINEAR HASH(YEAR(hired))
PARTITIONS 4;
```
说明：线性hash采用另一种算法，按照线性哈希分区的优点在于增加、删除、合并和拆分分区将变得更加快捷，有利于处理含有极其大量（1000G）数据的表。它的缺点在于，与使用常规HASH分区得到的数据分布相比，各个分区间数据的分布不
大可能均衡。



d KEY 分区：类似于按HASH分区，区别在于KEY分区只支持计算一列或多列，且MySQL 服务器提供其自
身的哈希函数。必须有一列或多列包含整数值。“CREATE TABLE ... PARTITION BY KEY”的语法规则类似于创建一个通过HASH分区的表的规则。它们唯一的
区别在于使用的关键字是KEY而不是HASH，并且KEY分区只采用一个或多个列名的一个列表。
例：
```
CREATE TABLE tk (
col1 INT NOT NULL,
col2 CHAR(5),
col3 DATE
)
PARTITION BY LINEAR KEY (col1)
PARTITIONS 3;
```
备注：在KEY分区中使用关键字LINEAR和在HASH分区中使用具有同样的作用

e 子分区
子分区是分区表中每个分区的再次分割，子分区既可以使用HASH希分区，也可以使用KEY分区。这 也被称为复合分区（composite partitioning）。
例1：
```
CREATE TABLE ts (id INT, purchased DATE)
PARTITION BY RANGE(YEAR(purchased))
SUBPARTITION BY HASH(TO_DAYS(purchased))
(
	PARTITION p0 VALUES LESS THAN (1990)
	(
	SUBPARTITION s0
		DATA DIRECTORY = '/disk0/data'
		INDEX DIRECTORY = '/disk0/idx',
	SUBPARTITION s1
		DATA DIRECTORY = '/disk1/data'
		INDEX DIRECTORY = '/disk1/idx'
	),
	PARTITION p1 VALUES LESS THAN (2000)
	(
		SUBPARTITION s0
			DATA DIRECTORY = '/disk2/data'
			INDEX DIRECTORY = '/disk2/idx',
		SUBPARTITION s1
			DATA DIRECTORY = '/disk3/data'
			INDEX DIRECTORY = '/disk3/idx'
	),
	PARTITION p2 VALUES LESS THAN MAXVALUE
	(
		SUBPARTITION s0
			DATA DIRECTORY = '/disk4/data'
			INDEX DIRECTORY = '/disk4/idx',
		SUBPARTITION s1
			DATA DIRECTORY = '/disk5/data'
			INDEX DIRECTORY = '/disk5/idx'
	)
);
```
例2：每个RANGE分区的数据和索引都使用一个单独的磁盘
```
CREATE TABLE ts (id INT, purchased DATE)
PARTITION BY RANGE(YEAR(purchased))
SUBPARTITION BY HASH(TO_DAYS(purchased))
(
	PARTITION p0 VALUES LESS THAN (1990)
	(
		SUBPARTITION s0a
			DATA DIRECTORY = '/disk0'
			INDEX DIRECTORY = '/disk1',//和例1的区别
		SUBPARTITION s0b
			DATA DIRECTORY = '/disk2'
			INDEX DIRECTORY = '/disk3'
	),
	PARTITION p1 VALUES LESS THAN (2000)
	(
		SUBPARTITION s1a
			DATA DIRECTORY = '/disk4/data'
			INDEX DIRECTORY = '/disk4/idx',
		SUBPARTITION s1b
			DATA DIRECTORY = '/disk5/data'
			INDEX DIRECTORY = '/disk5/idx'
	),
	PARTITION p2 VALUES LESS THAN MAXVALUE
	(
		SUBPARTITION s2a,
		SUBPARTITION s2b
	)
);
```
备注：
	每个分区必须有相同数量的子分区。在每个分区内，子分区的名字必须是唯一的，但是在整个表中，
没有必要保持唯一。
 	如果在一个分区表上的任何分区上使用SUBPARTITION 来明确定义任何子分区，那么就必须定义所有
的子分区。


备注：无论使用何种类型的分区，分区总是在创建时就自动的顺序编号，且从0开始记录，记住这一点非常重要。当有一新行插入到一个分区表中时，就是使用这些分区编号来识别正确的分区。例如，如果你的表使用4个分区，那么这些分区就编号为0, 1, 2和3。对于RANGE和LIST分区类型，确认每个分区编号都定义了一个分区，很有必要。对HASH分区，使用的用户函数必须返回一个大于0的整数值。对于KEY分区，这个问题通过MySQL服务器内部使用的哈希函数自动进行处理。
备注2：分区的名字是不区分大小写的

C 更改表分区选项
，假设有一个使用下面CREATE TABLE语句建立的按照RANGE分区的表：
```
CREATE TABLE trb3 (id INT, name VARCHAR(50), purchased DATE)
PARTITION BY RANGE(YEAR(purchased))
(
	PARTITION p0 VALUES LESS THAN (1990),
	PARTITION p1 VALUES LESS THAN (1995),
	PARTITION p2 VALUES LESS THAN (2000),
	PARTITION p3 VALUES LESS THAN (2005)
);
```
现在，要把这个表按照使用id列值作为键的基础，通过KEY分区把它重新分成两个分区，可以使用下面的语句：
`ALTER TABLE trb3 PARTITION BY KEY(id) PARTITIONS 2;`
这和先删除这个表、然后使用`CREATE TABLE trb3 PARTITION BY KEY(id) PARTITIONS 2`重新创建这个表具有同样的效果。问题：表里的数据呢？

18.3.1(MySQL手册) RANGE 和LIST 分区管理

A 删除一个RANGE或LIST分区比增加一个分区，MySQL手册PDF 918页 ；

记住下面一点非常重要：当删除了一个分区，也同时删除了该分区中所有的数据。

如果希望从所有分区删除所有的数据，但是又保留表的定义和表的分区模式，使用TRUNCATE TABLE命令。

如果希望改变表的分区而又不丢失数据，使用`ALTER TABLE ... REORGANIZE PARTITION`语句。

查看表的分区结构：`SHOW CREATE TABLE 表名`

注意：由`ALTER TABLE ... DROP PARTITION`语句引起的、从表中删除的行数并没有被服务器报告出来，就好像通过同等的DELETE查询操作一样。删除LIST分区使用和删除RANGE分区完全相同的“ALTER TABLE ... DROP PARTITION”语法。但是，在对其后使用这个表的影响方面，还是有重大的区别：在这个表中，再也不能插入这么一些行，这些行的列值包含在定义已经删除了的分区的值列表中


A 增加一个RANGE或者LIST分区到一个前面的已经分区的表，使用`ALTER TABLE ... ADD PARTITION`语
句。如：`ALTER TABLE ADD PARTITION (PARTITION p3 VALUES LESS THAN (2000));`

要点：对于通过RANGE分区的表，只可以使用ADD PARTITION添加新的分区到分区列表的高端。设法通过这
种方式在现有分区的前面或之间增加一个新的分区，将会导致下面的一个错误：
`mysql> ALTER TABLE members ADD PARTITION (PARTITION p3 VALUES LESS THAN (1960));`
参考：《MySQL手册》PDF 921页；

B 在不丢失数据清空下，重新定义分区的方式
例：
```
ALTER TABLE members REORGANIZE PARTITION p0 INTO (
	PARTITION s0 VALUES LESS THAN (1960),//也就是说可以变相增加'小'数据(比1960小)的分区咯
	PARTITION s1 VALUES LESS THAN (1970)
);
```
实际上，这个命令把分区p0分成了两个新的分区s0和s1。同时，它还根据包含在两个“PARTITION ... VALUES...”子句中的规则，把保存在分区p0中的数据移入到两个新的分区中，所以分区s0中只包含YEAR(dob)小于1960的那些行，s1中包含那些YEAR(dob)大于或等于1960但是小于1970的行。
一个REORGANIZE PARTITION语句也可以用来合并相邻的分区。可以使用如下的语句恢复成员表到它以前的分区：
```
ALTER TABLE members REORGANIZE PARTITION s0,s1 INTO (
	PARTITION p0 VALUES LESS THAN (1970)
);
```
使用“REORGANIZE PARTITION”拆分或合并分区，没有数据丢失。在执行上面的语句中，MySQL 把保存在分
区s0和s1中的所有数据都移到分区p0中。`REORGANIZE PARTITION`的基本语法是：
```
ALTER TABLE tbl_name REORGANIZE PARTITION partition_list INTO (partition_definitions);
```
参考：《MySQL手册》PDF 922页；

18.3.2. HASH和KEY分区的管理

A 使用`ALTER TABLE ... COALESCE PARTITION`命令来合并(只能减少分区数量)HASH或KEY分区。如：
`mysql> ALTER TABLE clients COALESCE PARTITION 6;`
备注：对于按照HASH，KEY，LINEAR HASH，或LINEAR KEY分区的表， COALESCE能起到同样的作用；

B 使用`ALTER TABLE ... ADD PARTITION`命令来增加(增加分区数量)HASH或KEY分区。如：
```
mysql> ALTER TABLE clients ADD PARTITION PARTITIONS 18;
```
注释：`ALTER TABLE ... REORGANIZE PARTITION`不能用于按照HASH或HASH分区的表。

18.3.3 分区维护
MySQL不支持命令CHECK TABLE，OPTIMIZE TABLE，ANALYZE TABLE，或REPAIR TABLE。
作为替代，可以使用ALTER TABLE 的许多扩展来在一个或多个分区上直接地执行这些操作，
如下面列出的那样：
	重建分区:这和先删除保存在分区中的所有记录，然后重新插入它们，具有同样的效果。它可用于整
理分区碎片。
示例：
```
ALTER TABLE t1 REBUILD PARTITION (p0, p1)
```
优化分区：如果从分区中删除了大量的行，或者对一个带有可变长度的行（也就是说，
有VARCHAR，BLOB，或TEXT类型的列）作了许多修改，可以使用“ALTER TABLE ... OPTIMIZE
PARTITION”来收回没有使用的空间，并整理分区数据文件的碎片。
示例：
`ALTER TABLE t1 OPTIMIZE PARTITION (p0, p1)`
在一个给定的分区表上使用“OPTIMIZE PARTITION”等同于在那个分区上运行`CHECK PARTITION，ANALYZE
PARTITION`，和`REPAIR PARTITION`。
· 分析分区：读取并保存分区的键分布。
示例：
```
ALTER TABLE t1 ANALYZE PARTITION (p3);
```
· 修补分区： 修补被破坏的分区。
示例：
```
ALTER TABLE t1 REPAIR PARTITION (p0,p1);
```
· 检查分区： 可以使用几乎与对非分区表使用CHECK TABLE 相同的方式检查分区。
示例：
```
ALTER TABLE trb3 CHECK PARTITION (p1);
```
这个命令可以告诉你表t1的分区p1中的数据或索引是否已经被破坏。如果发生了这种情况，使用“ALTER
`TABLE ... REPAIR PARTITION`来修补该分区。


7.2 视图
概念：视图本身是一个虚拟表，不存放任何数据。

A 视图的性能优化
创建视图'
```
mysql> 	CREATE VIEW Oceania AS 
			SELECT * FROM Country WHERE Continent = 'Oceania'
			WITH CHECK OPTION;
```
从视图中查询信息
`mysql>     SELECT Code,Name FROM Oceania WHERE Name = 'Australia';`//效率低
优化方法：将创建视图的条件加入查询条件中，猜测：查询视图本质也是去表查询
`mysql>     SELECT Code,Name FROM Oceania WHERE Continent = 'Oceania' Name = 'Australia';`

7.3 外键约束
说明：InnoDB 是目前MySQL中唯一支持外键的内置储存引擎；

外键缺点：使用外键是有成本的。比如外键通常要求每次在修改数据时都要在另外一张表中多执行一次查找操作。虽然InnoDB强制外键使用索引，但还是无法消除这种约束检查的开销。如果外键列的选择性低，则会导致一个非常大且选择性低的索引。例如，例如在非常大的表上有一个status列，并希望限制这个状态列的取值，如果该列只能取三个值-虽然这个列本身很小，但是如果主键很大，那么这个索引就会很大，而且这个索引除了这个外键限制外，没有其他任何作用了；

外键优点：外键在相关的删除和更新上，也比在应用中维护要更高效；

总结：如果只是使用外键做约束，那么通常在应用程序中使用该约束更好(即一般不采用外键)。

7.4.2 触发器
触发器：出现错误，不好排除，尽量不用触发器和储存过程；

额外知识：mysql持久化连接可以跨请求的，即一个php脚本执行完成后，连接不断开，下一个php脚本执行还可以用这个连接；

7.11 分布式(XA)事务
概要：分布式事务可以让储存引擎级别的ACID可以扩展到数据库层面，甚至可以扩展到多个数据库之间。

7.12 查询缓存
概念：MySQL查询缓存保存查询返回的完整结果。当查询命中该缓存，MySQL会立刻返回结果，跳过解析、优化、执行阶段；查询缓存会跟踪缓存结果中的每一个表，如果相关表发生变化，所有缓存数据都将失效；
常规情况下，最好默认关闭；

7.12.4 如何配置和维护查询缓存
以下为配置参数
query_cache_type
是否打开查询缓存。可以设置OFF、ON和DEMAND。DEMAND表示只有在查询语句中明确写明SQL_CACHE的语句才放入查询缓存。这个变量可以是会话级别的也可以是全局级别的
query_cache_size
查询缓存使用的总内存空间，单位为字节。这个值必须是1024的整数倍，否则MySQL实际分配的数据会和指定的略有不同；
query_cache_min_res_unit
在查询缓存中分配内存块时的最小单位。
query_cache_limit
MySQL能够缓存的最大结果。如果查询结果大于这个值，则不会被缓存。如果事先知道结果会超出，则建议在查询语句中直接加入 SQL_NO_CACHE来避免查询缓存带来的额外消耗；
query_cache_wlock_invalidate
如果某个数据表被其他的连接锁住，是否仍然从查询缓存中返回结果。默认为OFF，通常也采用默认；



### 第 10 章 复制

10.1.2 	复制如何工作
A 复制三个步骤
a 在主库上把数据更改记录到二进制日志(Binary Log) 中 (这些记录会被称为二进制日志事件)。
b 备库将主库上的日志复制到自己的中继日志(Relay Log)中。
c 备库读取中继日志的事件，将其重放到备库数据上。


10.2 配置复制

A 配置复制步骤
a 在每天服务器上创建复制账号。
b 配置主库和备库
c 通知备库连接到主库并从主库复制数据。


10.4.1 一主库多备库
A 简单的拓扑结构
a 为不同的角色使用不同的备库(例如添加不同的索引或使用不同存储引擎)。
b 把一台备库当做代用的主库，除了复制，没有其他数据传输。
c 将一台备库放到远程数据中心，用作灾难恢复。
d 延迟一个或多个备库，以备灾难恢复。
e 使用其中一个备库，作为备份、培训、开发或者测试使用服务器。

备注：复制大部分出现在配置主-从数据库那里。

### 第 11 章 可扩展的MySQL
这一章：现在来说，太难，太空

11.3 负载均衡
负载均衡的基本思路很简单：在一个服务器集群中尽可能的平均负载量。通常的做法是在服务器前端设置一个负载均衡器（一般专门的硬件设备）。然后负载均衡器将请求的连接路由到最空闲的服务器。
来自《高性能MySQL》PDF 568页；

负载均衡有五个常见的目的：
a 可扩展性：负载均衡对某些扩展策略有所帮助，例如读写分离时从备库读数据。
b 高效性：负载均衡有助于更高效的使用资源，因为它能够控制请求被路由到何处。如果服务器的处理能力各不相同，这就尤为重要：你可以把更多的功能分配给性能更好的机器。
c 可用性：一个灵活的负载均衡解决方案能够使用时刻保持可用的服务器。
d 透明性：客户端无须知道是否存在负载均衡设置，也不需要关心在负载均衡器的背后有多少机器，他们的名字是什么。负载均衡器给客户端看到的是一个虚拟的服务器。
e 一致性：如果应用是有状态的(数据库事务，网站会话等)，那么负载均衡器就应将相关的查询指向同一个服务器，以防止状态丢失。应用无须去跟踪到底连接的是哪个服务器。

备注：负载均衡器演示图 在高性能MySQL》PDF 569页；

### 第 14 章 应用层优化


### 第 15 章 备份与恢复

15.3 设计MySQL备份方案
建议：
a 基于快照的备份，例如Percona XtraBackup 和 MySQL Enterprise Backup 是最好的选择。
b 保留多个备份集；
c 定期从逻辑备份(或者物理备份)抽取数据进行恢复测试。
d 保存二进制日志以用于基于故障时间点的恢复。
e 通过演练整个恢复过程来测试备份和恢复。
f 对于安全性要仔细考虑。
备注：如果需求不容丢失任何一个已提交事务，即使出现任何情况，这就需要特别的技术，例如将二进制日志保存在一个独立SAN卷或者使用DRBD磁盘复制。

15.3.2 逻辑备份还是物理备份
逻辑备份('导出')：将数据包含在一种MySQL能够解析的格式中，要么SQL，要么是以某种符号分割的文本
物理备份：直接复制存在硬盘上的数据和表定义等文件；

备注：两种备份方式都有优缺点，建议是：先使用物理复制，以此数据启动MySQL服务器实例并运行
mysqlcheck。然后，周期性地使用mysqldump执行逻辑备份。


增量备份：自从任意类型的上次备份后所有修改过的备份。
差异备份：对自上次全备份后所有改变的部分而做的备份。
例：假如在每周日做了一次全备份，在周一，对自周日以来所有的改变做一个差异备份。在周二，就两个选择：备份自周日以来所有的改变(差异)，或只备份自从周一备份后所有的改变（增量）。
建议：《高性能MySQL》PDF 638页；


重点：使用经验来看，用Xtrabackup全备数据，用blackhole增备binlog

### 附录 EXPLAIN

《高性能MySQL》 PDF 723页
A explain 的type列说明：依次从最差到最优

- ALL 	全表扫描，但查询中使用LIMIT，或者Extra列中显示`Using distinct/not exists`是例外；
- index 	这个跟全表扫描一样，只是MySQL扫描表时按索引次序进行而不是行。但是如果如果在Extra列中看到"Using index"，说明MySQL正在使用覆盖索引(，它只扫描索引的数据，而不是按索引次序的每一行，它比按索引次序扫描全表的开销要小得多。
- range 	范围扫描就是一个有限制的索引扫描。例如sql语句中带有BETWEEN AND 或者` WHERE > < != `等，以及`IN()`和`OR()`。
- ref 	这是一种索引访问(索引查找)，它返回所有匹配某个单个值的行。此类索引访问只有使用非唯一性索引或者唯一性索引的非唯一前缀时才会发生。
- eq_ref 	使用这种索引查找，MySQL知道最多只返回一条符合条件的记录。这种访问方法可以在MySQL使用主键或者唯一性索引查找时看到，它会将它们与某个参考值做比较。MySQL对这种索引优化得比较好。
- const,system 当MySQL能对查询的某个部分进行优化并将其转化为一个常量时，它就会使用这些访问类型 例如：如果通过某一行的主键放入WHERE子句里的方式来选取此行的主键，MySQL就能把这个查询转化为一个常量。 
- NULL 	这种方式意味着MySQL能在优化阶段分解查询语句，在执行阶段甚至用不着再访问表或者索引。
NULL system const eq_ref ref range index ALL

B explain 的possible_keys 列：显示哪些索引有助于高效的查找。

C explain 的key列：针对当前查询sql语句，采用的哪些索引。

D explain 的rows列：MySQL 估计要找到所需的行需要读取的行数(如果关联查询，则是内嵌循环关联数目)。

E explain 的Extra列：

- Using index :   此值表示MySQL将使用覆盖索引，以避免访问表。
- Using where :   这意味着MySQL服务器将在存储引擎检索行后在进行过滤。暗示着：查询受益于不同的索引。
- Using temporary:这意味着MySQL在对查询结果排序时会使用一个临时表。
- Using filesort: 这意味着MySQL会对结果使用一个外部索引排序，而不是按索引次序从表里读出行
`Range checked for each record(index map:N)`：这意味着没有好用的索引，新的索引将在联接 的每一行上重新估算。
```
create table yii_ts (
id int not null auto_increment primary key,
name char(20) default '',
age tinyint default 0,
key (name,age)
) engine=innodb charset=utf8;
```




## 一些优化技巧
1. 对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。

2. 应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描，如：`select id from t where num is null`可以在num上设置默认值0，确保表中num列没有null值，然后这样查询：`select id from t where num=0`
更正： is null或者直接在某个含有null的字段上搜索，是走索引的
确认：or和!=确实是没有走索引

3. 应尽量避免在 where 子句中使用!=或<>操作符，否则引擎将放弃使用索引而进行全表扫描。

4. 应尽量避免在 where 子句中使用or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描，如：`select id from t where num=10 or num=20`可以这样查询：`select id from t where num=10 union all select id from t where num=20`
备注：在WHERE 后的字段没有索引，就不要转化为UNION或者先添加索引在转化；

5. in 和 not in 也要慎用，否则会导致全表扫描，如：`select id from t where num in(1,2,3)` 对于连续的数值，能用 between 就不要用 in 了：`select id from t where num between 1 and 3`
备注：in()、or、between .. and .. 用explain来看都是一样的；
备注2：in(子句)最好用exists(子句)代替
备注3：in(具体值)是可以将就用的，如in(2,4,5)
备注4：not in() 一般都是全表扫描，无论括号中是具体值，还是子句都不行，如果是子句，可以尝试转化为not exists()，或者转化为 `join (select ....) `

6. 下面的查询也将导致全表扫描：`select id from t where name like '李%'`若要提高效率，可以考虑全文检索。

7. 如果在 where 子句中使用参数，也会导致全表扫描。因为SQL只有在运行时才会解析局部变量，
但优化程序不能将访问计划的选择推迟到运行时；它必须在编译时进行选择。
然 而，如果在编译时建立访问计划，变量的值还是未知的，因而无法作为索引选择的输入项。
如下面语句将进行全表扫描：`select id from t where num=@num`
可以改为强制查询使用索引：`select id from t with(index(索引名)) where num=@num
`


8. 应尽量避免在 where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描。
如：`select id from t where num/2=100`应改为:`select id from t where num=100*2`



9. 应尽量避免在where子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描。
如：`select id from t where substring(name,1,3)='abc'` ，name以abc开头的id；ps：'abc'=substring(name,1,3)这样也全表扫描
应改为:`select id from t where name like 'abc%'`

10. 不要在 where 子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引。

11. 在使用索引字段作为条件时，如果该索引是复合索引，
那么必须使用到该索引中的第一个字段作为条件时才能保证系统使用该索引，
否则该索引将不会被使用，并且应尽可能的让字段顺序与索引顺序相一致。



12. 不要写一些没有意义的查询，如需要生成一个空表结构：`select col1,col2 into #t from t where 1=0`
这类代码不会返回任何结果集，但是会消耗系统资源的，应改成这样：
create table #t(...)

13. 很多时候用 exists 代替 in 是一个好的选择：`select num from a where num in(select num from b)`
用下面的语句替换：
`select num from a where exists(select 1 from b where num=a.num)`

14. 并不是所有索引对查询都有效，SQL是根据表中数据来进行查询优化的，
当索引列有大量数据重复时，SQL查询可能不会去利用索引，如一表中有字段sex，male、female几乎各一半，
那么即使在sex上建了索引也对查询效率起不了作用。

15. 索引并不是越多越好，索引固然可 以提高相应的 select 的效率，
但同时也降低了 insert 及 update 的效率，因为 insert 或 update 时有可能会重建索引，
所以怎样建索引需要慎重考虑，视具体情况而定。一个表的索引数最好不要超过6个，
若太多则应考虑一些不常使用到的列上建的索引是否有 必要。

16. 应尽可能的避免更新 clustered 索引数据列，因为 clustered 索引数据列的顺序就是表记录的物理存储顺序，
一旦该列值改变将导致整个表记录的顺序的调整，会耗费相当大的资源。
若应用系统需要频繁更新 clustered 索引数据列，那么需要考虑是否应将该索引建为 clustered 索引。

17. 尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，
这会降低查询和连接的性能，并会增加存储开销。
这是因为引擎在处理查询和连接时会逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了。

18. 尽可能的使用 varchar/nvarchar 代替 char/nchar ，因为首先变长字段存储空间小，可以节省存储空间，
其次对于查询来说，在一个相对较小的字段内搜索效率显然要高些。

19. 任何地方都不要使用 `select * from t` ，用具体的字段列表代替`*`，不要返回用不到的任何字段。

20. 尽量使用表变量来代替临时表。如果表变量包含大量数据，请注意索引非常有限（只有主键索引）。

21. 避免频繁创建和删除临时表，以减少系统表资源的消耗。

22. 临时表并不是不可使用，适当地使用它们可以使某些例程更有效，
例如，当需要重复引用大型表或常用表中的某个数据集时。但是，对于一次性事件，最好使用导出表。

23. 在新建临时表时，如果一次性插入数据量很大，那么可以使用` select into 代替 create table`，
避免造成大量 log ，以提高速度；如果数据量不大，为了缓和系统表的资源，应先`create table`，然后insert。

24. 如果使用到了临时表，在存储过程的最后务必将所有的临时表显式删除，
先 `truncate table` ，然后` drop table` ，这样可以避免系统表的较长时间锁定。

25. 尽量避免使用游标，因为游标的效率较差，如果游标操作的数据超过1万行，那么就应该考虑改写。

26. 使用基于游标的方法或临时表方法之前，应先寻找基于集的解决方案来解决问题，基于集的方法通常更有效。

27. 与临时表一样，游标并不是不可使 用。对小型数据集使用 FAST_FORWARD 游标通常要优于其他逐行处理方法，
尤其是在必须引用几个表才能获得所需的数据时。在结果集中包括“合计”的例程通常要比使用游标执行的速度快。
如果开发时 间允许，基于游标的方法和基于集的方法都可以尝试一下，看哪一种方法的效果更好。

28. 在所有的存储过程和触发器的开始处设置 SET NOCOUNT ON ，在结束时设置 `SET NOCOUNT OFF` 。
无需在执行存储过程和触发器的每个语句后向客户端发送DONE_IN_PROC 消息。

29. 尽量避免大事务操作，提高系统并发能力。

30. 尽量避免向客户端返回大数据量，若数据量过大，应该考虑相应需求是否合理。

31. 尽量少用或者不用联合查询(类似 inner join、outer join这种 )，在php层实现联合；
备注：来自《高性能MySQL》6.3.3 ；PDF 239页；暂时先不用，涉及到左连接、内连接，以及排序好像不好操作；

32. 采用分表技术（大表分小表）
a)垂直分表：将部分字段分离出来，设计成分表，根据主表的主键关联
b)水平分表：将相同字段表中的记录按照某种Hash算法进行拆分多个分表

总结：
A sql语句总体禁忌：
```
OR 				代替用union all连接多条查询，或者用in()
<> 或 != 						
in(子句)    	用exists(子句)代替，但是in(具体值)是可以用的，如in(2,4,5)
not in(子句) 	用not exists(子句)代替
null 			避免where子句进行null判断，代替方法为设置默认值
```


## 触发器(TRIGGER)
MySQL包含对触发器的支持。触发器是一种与表操作有关的数据库对象，当触发器所在表上出现指定事件时，将调用该对象，即表的操作事件触发表上的触发器的执行。

创建触发器
在MySQL中，创建触发器语法如下：

代码如下:

```
CREATE TRIGGER trigger_name
trigger_time
trigger_event ON tbl_name
FOR EACH ROW
trigger_stmt
```
其中：
```
trigger_name：标识触发器名称，用户自行指定；
trigger_time：标识触发时机，取值为 BEFORE 或 AFTER；
trigger_event：标识触发事件，取值为 INSERT、UPDATE 或 DELETE；
tbl_name：标识建立触发器的表名，即在哪张表上建立触发器；
trigger_stmt：触发器程序体，可以是一句SQL语句，或者用 BEGIN 和 END 包含的多条语句。
```
由此可见，可以建立6种触发器，即：`BEFORE INSERT、BEFORE UPDATE、BEFORE DELETE、AFTER INSERT、AFTER UPDATE、AFTER DELETE`。

另外有一个限制是不能同时在一个表上建立2个相同类型的触发器，因此在一个表上最多建立6个触发器。

### `trigger_event` 详解
MySQL 除了对 `INSERT、UPDATE、DELETE` 基本操作进行定义外，还定义了 `LOAD DATA` 和 `REPLACE` 语句，这两种语句也能引起上述6中类型的触发器的触发。

`LOAD DATA` 语句用于将一个文件装入到一个数据表中，相当与一系列的 `INSERT` 操作。

`REPLACE` 语句一般来说和 `INSERT` 语句很像，只是在表中有 `primary key` 或 `unique` 索引时，如果插入的数据和原来 `primary key` 或 `unique` 索引一致时，会先删除原来的数据，然后增加一条新数据，也就是说，一条 `REPLACE` 语句有时候等价于一条。

INSERT 语句，有时候等价于一条 DELETE 语句加上一条 INSERT 语句。

- INSERT 型触发器：插入某一行时激活触发器，可能通过 `INSERT、LOAD DATA、REPLACE` 语句触发；
- UPDATE 型触发器：更改某一行时激活触发器，可能通过 `UPDATE` 语句触发；
- DELETE 型触发器：删除某一行时激活触发器，可能通过 `DELETE、REPLACE` 语句触发。

`BEGIN … END` 详解
在MySQL中，`BEGIN … END` 语句的语法为：
```
BEGIN
[statement_list]
END
```
其中，statement_list 代表一个或多个语句的列表，列表内的每条语句都必须用分号（;）来结尾。
而在MySQL中，分号是语句结束的标识符，遇到分号表示该段语句已经结束，MySQL可以开始执行了。因此，解释器遇到statement_list 中的分号后就开始执行，然后会报出错误，因为没有找到和 BEGIN 匹配的 END。

这时就会用到 `DELIMITER` 命令（`DELIMITER` 是定界符，分隔符的意思），它是一条命令，不需要语句结束标识，语法为：
```
DELIMITER new_delemiter
new_delemiter
```
可以设为1个或多个长度的符号，默认的是分号（;），我们可以把它修改为其他符号，如$：
`DELIMITER $`
在这之后的语句，以分号结束，解释器不会有什么反应，只有遇到了`$`，才认为是语句结束。注意，使用完之后，我们还应该记得把它给修改回来。

一个完整的创建触发器示例
假设系统中有两个表：
班级表 class(班级号 classID, 班内学生数 stuCount)
学生表 student(学号 stuID, 所属班级号 classID)
要创建触发器来使班级表中的班内学生数随着学生的添加自动更新，代码如下：

代码如下:下载地址 springmvc+mybatis+spring 整合SSM   
```
DELIMITER $
create trigger tri_stuInsert after insert
on student for each row
begin
declare c int;
set c = (select stuCount from class where classID=new.classID);
update class set stuCount = c + 1 where classID = new.classID;
end$
DELIMITER ;
```

### 变量详解
MySQL 中使用 `DECLARE` 来定义一局部变量，该变量只能在 `BEGIN … END` 复合语句中使用，并且应该定义在复合语句的开头，

即其它语句之前，语法如下：
```
DECLARE var_name[,...] type [DEFAULT value]
```
其中：
`var_name` 为变量名称，同 SQL 语句一样，变量名不区分大小写；type 为 MySQL 支持的任何数据类型；可以同时定义多个同类型的变量，用逗号隔开；变量初始值为 NULL，如果需要，可以使用 DEFAULT 子句提供默认值，值可以被指定为一个表达式。

对变量赋值采用 SET 语句，语法为：
```
SET var_name = expr [,var_name = expr] ...
```

### NEW 与 OLD 详解

上述示例中使用了NEW关键字，和 MS SQL Server 中的 INSERTED 和 DELETED 类似，MySQL 中定义了 NEW 和 OLD，用来表示

触发器的所在表中，触发了触发器的那一行数据。
具体地：

- 在 INSERT 型触发器中，NEW 用来表示将要（BEFORE）或已经（AFTER）插入的新数据；
- 在 UPDATE 型触发器中，OLD 用来表示将要或已经被修改的原数据，NEW 用来表示将要或已经修改为的新数据；
- 在 DELETE 型触发器中，OLD 用来表示将要或已经被删除的原数据；
使用方法： NEW.columnName （columnName 为相应数据表某一列名）
另外，OLD 是只读的，而 NEW 则可以在触发器中使用 SET 赋值，这样不会再次触发触发器，造成循环调用（如每插入一个学生前，都在其学号前加“2013”）。

### 查看触发器

和查看数据库（`show databases;`）查看表格（`show tables;`）一样，查看触发器的语法如下：
```
SHOW TRIGGERS [FROM schema_name];
```
其中，schema_name 即 Schema 的名称，在 MySQL 中 Schema 和 Database 是一样的，也就是说，可以指定数据库名，这样就

不必先`USE database_name;`了。

### 删除触发器

和删除数据库、删除表格一样，删除触发器的语法如下：
```
DROP TRIGGER [IF EXISTS] [schema_name.]trigger_name
```
触发器的执行顺序

我们建立的数据库一般都是 InnoDB 数据库，其上建立的表是事务性表，也就是事务安全的。这时，若SQL语句或触发器执行失败，MySQL 会回滚事务，有：

①如果 BEFORE 触发器执行失败，SQL 无法正确执行。
②SQL 执行失败时，AFTER 型触发器不会触发。
③AFTER 类型的触发器执行失败，SQL 会回滚

## 常用操作

### 设置密码
```
# /usr/local/mysql/bin/mysql -u root			#没有密码可以登录本机服务器
# mysql> delete from mysql.user where host !='localhost' and user='';
# mysql> FLUSH PRIVILEGES;
# mysql> set password for 'root'@'localhost'=PASSWORD('123456');
#退出程序，再用一下代码进入
# /usr/local/mysql/bin/mysql -u root -h localhost -p
```
备注：也许启动命令路径不再是 `/usr/local/mysql/bin/mysql`

### 设置允许远程连接

步骤一 使用
`GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY ‘password’WITH GRANT OPTION;`
命令可以更改远程连接的设置。说明：其中，root是用户名，mysql默认的是root ，password是密码自己设置。%表示任意主机。这样就允许远程任意一台主机访问了。

步骤二
使用`flush privileges;`命令刷新刚才修改的权限，使其生效。

步骤三 查看是否设置成功：`select host,user from user;`
结果：
```
host 		user
% 			root 		#表明成功
....
```

## 错误解决

### MySQL远程连接丢失问题解决方法（Lost connection to MySQL server）
报错：`Lost connection to MySQL server at ‘reading initial communication packet', system error: 0 `

思路： 其实问题很简单，都是MySQL的配置文件默认没有为远程连接配置好，只需要更改下MySQL的配置文件即可。

解决：
找到并修改my.cnf文件。在不同的Linux系统下，my.cnf放在不同的位置。这里以Ubuntu Server做示例，其他系统请根据情况自行找到my.cnf的路径。一般只会存放在/etc/my.cnf或者/etc/mysql/my.cnf下。
首先用vim打开my.cnf：
`vim /etc/mysql/my.cnf`
看看是否有绑定本地回环地址的配置，如果有，注释掉下面这段文字：（在文字之前加上#号即可）
`bind-address = 127.0.0.1`
然后找到[mysqld]部分的参数，在配置后面建立一个新行，添加下面这个参数：
`skip-name-resolve`
保存文件并重启MySQL：
`/etc/init.d/mysql restart`


### mysql5.7中timestamp默认值'0000-00-00 00:00:00'报错
情况：这是mysql5.7中调整了sql_mode和explicit_defaults_for_timestamp，导致了timestamp不再支持'0000-00-00 00:00:00'
解决：添加mysql配置文件内容，`/etc/mysql/my.cnf`或者`/etc/mysql/conf.d/*`（ps：后者需要前者中有`!inclludedir `声明引入）
```ini
sql-mode=STRICT_TRANS_TABLES,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
explicit_defaults_for_timestamp=1
```
ps：sql-mode参考：<https://blog.csdn.net/xinyuan_java/article/details/97137075>


## 锁
### mysql锁总结
a. 悲观锁：顾名思义，对数据操作比较悲观，持保守态度。先拿锁，然后操作数据，操作完释放锁
例：select * from table_name where id=1 for update，这是先用排他锁锁定数据，然后再去操作其他或者这个数据

b. 乐观锁：顾名思义，对数据操作比较乐观，持积极态度。先操作数据，然后再提交或者称为拿锁，如果没人操作该数据，
则提交，反之则回滚或者编辑数据
例：git操作中，先编写代码，然后提交代码，如果有人已经操作，则编辑冲突或者回滚，没人操作，则直接提交成功
在MySQL中，一般在表中多建一个version字段，作为版本信息，用于最后提交时比较

c. 
 	    行锁	    表锁	    页锁
MyISAM	     	√	 
BDB	 	        √	    √
InnoDB	 √	    √	 

开销、加锁速度、死锁、粒度、并发性能

表锁：开销小，加锁快；不会出现死锁；锁定力度大，发生锁冲突概率高，并发度最低
行锁：开销大，加锁慢；会出现死锁；锁定粒度小，发生锁冲突的概率低，并发度高
页锁：开销和加锁速度介于表锁和行锁之间；会出现死锁；锁定粒度介于表锁和行锁之间，并发度一般
死锁：官方定义如下：两个事务都持有对方需要的锁，并且在等待对方释放，并且双方都不会释放自己的锁。
    MySQL有两种死锁处理方式：
    
    等待，直到超时（innodb_lock_wait_timeout=50s）。
    发起死锁检测，主动回滚一条事务，让其他事务继续执行（innodb_deadlock_detect=on）。

d. 读锁（共享锁，S锁）：共享锁排除排他锁操作，即允许其他共享锁和select这种无锁的操作
- 行级读锁：lock in share mode：本事务和其他事务都能读，但都不能进行其他操作
- 表级读锁：lock table table_name read：
e. 写锁（排他锁，X锁）：排他锁是排除其他锁，即共享锁也不能操作，但运行select这种无锁操作，即select是拦不住的
- 行级写锁：这个好像是自动的
    备注：select ... for update 也是排他锁
- 表级写锁：lock table table_name write

f. innodb引擎：select操作不加锁，其他操作自动加排他锁
f. myisam引擎：select默认加表级读锁，其他操作自动加排他锁，而且在队列中排他锁比共享锁优先级高。读写操作基本是串行的。
    备注：myisam时可以join关联操作的，只是没有外键和事务
    参考<https://www.cnblogs.com/justlikeheaven/p/myisam-lock.html>


## 事务实现原理

前提说明：
注意的是事务的启动时机。
在可重复读RR隔离级别模式下，begin/start transaction 命令并不是一个事务的起点，在执行到它们之后的第一个操作 InnoDB 表的语句，
事务才真正启动。如果你想要马上启动一个事务，可以使用 start transaction with consistent snapshot 这个命令。

第一种启动方式，begin/start transaction一致性视图是在第执行第一个快照读语句时创建的;
第二种启动方式，start transaction with consistent snapshot一致性视图是在执行 start transaction with consistent snapshot 时创建的。

行记录隐藏字段

db_row_id，行ID，用来生成默认聚簇索引（聚簇索引，保存的数据在物理磁盘中按顺序保存，这样相关数据保存在一起，提高查询速度）
db_trx_id，事务ID，新开始一个事务时生成，实例内全局唯一；6byte，最近修改(修改/插入)事务ID：记录创建这条记录/最后一次修改该记录的事务ID
db_roll_ptr，undo log指针，指向对应记录当前的undo log；7byte，回滚指针，指向这条记录的上一个版本（存储于rollback segment里）
deleted_bit，删除标记位，删除时设置;为了节省磁盘空间，InnoDB有专门的purge线程来清理deleted_bit为true的记录。

链接：<https://www.jianshu.com/p/0ef46997c300>
参考：<https://blog.csdn.net/SnailMann/article/details/94724197>


### 原子性：利用undo log机制实现
### 持久性：利用redo log机制实现
### 一致性：利用原子性，持久性以及隔离性共同实现

### 隔离性：包含一下四个级别

#### read uncommited（读未提交）
    说明：读未提交模式，并没有进行特殊设计，因为读不会加任何锁，所以写操作在读的过程中修改数据，所以会造成脏读。
#### read commited（读已提交）
    说明：采用mvcc机制 + 每一个语句执行前都会重新算出一个新的视图。猜测：根据read commited有不可重复读特性反推
    应该是当前事务的transaction id没有变化，但是当前活跃事务ID数组发生变化，有些事务已经提交，所以造成了不可重复读；
#### repeatable read（可重复读）
    说明：采用mvcc机制 + 只需要在事务开始的时候创建一致性视图，之后事务里的其他查询都共用这个一致性视图。
#### serializable（串行化）

### 概念说明

#### mvcc机制：多版本并发控制
    mvcc-读：快照读，以数据版本的可见性规则实现，由 行记录的DB_TRX_ID + DB_ROLL_PTR 和一致性读视图（consistent read view）对比实现
    mvcc-增删改以及加锁读：当前读，直接操作当前已提交事务的最终值，而不是操作快照；这里需要锁机制起作用了
#### 一致性读视图（consistent read view）
    原理：
    1. 在实现上， InnoDB 为每个事务构造了一个数组，用来保存这个事务启动瞬间，
    当前正 在“活跃”的所有事务 ID。“活跃”指的就是，启动了但还没提交。
    数组里面事务 ID 的最小值记为低水位，当前系统里面已经创建过的事务 ID 的最大值加 1 记为高水位。
    这个视图数组和高水位，就组成了当前事务的一致性视图(read-view)。
    
    2. 处于高水位之前的所有 已提交事务 + 当前事务的数据都是可见的。这里处于构造数组中的事务中的原本数据，可以通过undo log获取满足要求的版本数据
    可见的依据是：DB_TRX_ID小于等于当前事务TRANSACTION ID，并且记录的DB_ROLL_PTR为NULL或者大于当前事务TRANSACTION ID
    
#### 快照读：
    
    SQL读取的数据是快照版本(snapshot)，也就是历史版本，普通的SELECT就是快照读。以undo log为实现机制
    
#### 当前读：
    
    SQL读取的数据是最新版本。通过锁机制来保证读取的数据无法通过其他事务进行修改。
    
    UPDATE、DELETE、INSERT、SELECT … LOCK IN SHARE MODE、SELECT … FOR UPDATE都是当前读。

### 分析并发事务数据操作思路
1. 确定是什么时候开始建立tranaction id和一致性读视图的，注意`start transaction`和
    `start transaction with consistent snapshot`的区别

2. 分析具体事务中一致性读视图情况，比如获取的活跃事务id数组情况，重点注意：只有普通读采用这个哈

3. 按时间先后顺序依次分析具体操作
    重点注意：只有简单的select才是快照读，而增删改和加锁读都是当前读，操作实际数据
    需要考虑排他锁的一些机制，比如：只有等先获取到排他锁的事务提交或回滚后，才能继续操作数据

### 额外说明
1. 增删改查单独语句其实就是一个事务

- 参考：<https://www.cnblogs.com/eternityz/p/12443278.html>
- 参考：<https://blog.csdn.net/dome_/article/details/90514673>
- 参考：<https://blog.csdn.net/u011212394/article/details/85056686>
- 参考：<https://blog.csdn.net/hla199106/article/details/99683010>
