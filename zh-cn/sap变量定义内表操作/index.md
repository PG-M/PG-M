# ABAP变量定义内表操作

- 数据类型与数据对象
  - 在ABAP语句中，使用变量之前，必须针对变量进行声明。声明过程中，会涉及到以下两个概念：

    - 数据类型

      - 数据类型描述了数据对象的技术属性（类型和长度），用于定义数据对象

      - 本身不能被直接引用或赋值

      - 数据元素（Data Element）：用于定义一个字段类型的变量

      - 结构体（Structure）：用于定义一个内存中的结构体变量

      - 内表类型（Table Type）：用于定义一个内存中的内表

      - 数据类型分类

        - 全局类型（数据字典中创建的类型）DATA ELEMENT

          - STRUCTURE

          - TABLE TYPE

        - 标准类型（C ,N,D,T,F,I,P,STRING）

        - 局部类型（程序中单独声明的类型）

          - DATA TYPE

          - STRUCTURE TYPE

          - TABLE TYPE

        - 类型创建基本语句

          - TYPE OBJ[TYPE 类型 | LIKE 对象.

          - 例：TYPE gty_char TYPE  C LENGTH 10 （定义了一个CHAR类型长度为10的类型）

    - 数据对象

      - 数据对象（如文本、变量、常量）

      - DATA关键字用来声明的变量或者实体结构具有实际的内存空间

      - 系统标准参数

        - 系统定义的数据对象无需声明，可以在程序中直接使用（结构SYST中）

        - SY-SUBRC：返回代码值（上一步操作是否成功，成功为0，失败非0）

        - SY-UNMAE：当前用户名

        - SY-TCODE：当前事务

        - SY-DATUM：当前日期

        - SY-UZEIT：当前时间

        - SY-REPID：当前程序名

        - SY-TABIX：内表的第几行

  - 变量的声明

    - 变量声明基本语句

      - DATA obj1 [TYPE type1 |  LIKE obj1]  [value value1]

        - 例：DATA gv_char1 TYPE C LENGTH 10 .

        - DATA gv_char2 TYPE  gth_char.

        - DATA gv_char3  LIKE gv_char2.

    - 常量的声明
      - CONSTANTS  例：C_CHAR TYPE C LENGTH 10  VALUE 'ABC'.（必须带VALUE后缀）

    - 文本文字

      - 硬编码：单引号使用

      - 使用文本元素（TEXT ELEMENT）

      - 数字文字：不需要单引号，直接使用

  - 结构类型及结构的定义

    - 结构类型属于一种复杂类型，由多个数据元素作为组件组成，参照结构类型生成的数据对象称为结构体

    - 使用TYPES和DATA语句构造内部的结构体的语法格式如下：

      - 可以使用'-'符号访问结构体对象各组件：structure-field

      - TABLES语句，声明一个和数据库表名称完全相同的结构

  - 表类型及内表的定义

    - 表类型是ABAP中的另外一种复杂类型，该类型的数据对象称为内表。表类型跟结构体类型相似。内表所占的内存不能再定义时确定，他会随着数据的增加不断的扩充它所需要的内存

    - 在程序中，内表可以通过TYPE/DATA语句中的Table of附加项进行构造：TYPES | DATA:itab TYPE | LIKE tabkind OF linetype  [WITH KEY] [INITIAL SIZE n]
- 赋值方法

  - =号赋值 例：    GT_DATA1 = GT_DATA2

  - MOVE TO   例：MOVE GT_DATA2 TO GT_DATA1 

  - WRITE  例：      WRITE GT_DATA2 TO GT_DATA1 
    - 注意使用WRITE进行赋值的时候，后面的不能是I类型或 者P类型

  - 结构的赋值

    - MOVE STR1 TO STR 2nm

    - MOVE-CONRRESPONDING STR1 TO STR2
- 内表

  - 内表的分类

    - 标准表（STANDARD TABLE）

      - 系统为该表每一行生成一个逻辑索引，填充表时可以将数据附加再现有行之后，也可以插入到指定的位置，程序对内表的寻址可以通过关键字或者索引进行，再对表进行插入，删除等操作时，各行数据在内表的位置不变，系统进为重新排列各数据的索引值

      - DATA : itab TYPE [STANDARD] TABLE OF linetype

    - 排序表(SORTED TABLE）

      - 也具有为该表每一行生成一个逻辑索引，不同之处是排序表总是按期表关键字升序排序后在进行存储，其访问方式与标准表相同

      - DATA:itab TYPE SORTED TABLE OF linetype WITH UNIQUE | NON-UNIQUE KEY keyname

    - 哈希表（HASHED TABLE）

      - 没有索引，只能通过关键字来访问，系统用哈希算法来管理表中的数

      - DATA:itab TYPE HASHED TABLE OF linetype WITH UNIQUE KEY keyname

    - 另外，还有一般类型表：INDEX TABLE 和 任意表：ANY TABLE
      - 这两种表类型可以定义数据类型，但是不能生成数据对象，数据对象一般实在程序中动态指定的，一般是在Perform程序块或者是Field Symbols里使用

  - 和内表对应的结构体又叫工作区

  - 表头行

    -  在创建内表的同时可以使用选线 With header line 隐式的定义了一个工作表头行，也称作内表工作区  

    -  DATA :itab TYPE tabletype WITH HEADER LINE.

    -  用以上语句创建内表的同时就隐含的创建了一个内表的工作区

       - 内表的引用方法：itab[]

       - 工作区的引用方法 itab

  - 内表数据的操作

    - 内表数据的增加

      - 追加--APPEND

        - APPEND 相当于在内表的最后追加一行，他只能使用在索引表中且最好是标准表。而标准表是ABAP编程中最常用的表，所以这个语句很重要

        - APPEND[line | INITIAL LINE] TO itab. （插入一个空行）

        - APPEND LINES OF itab1[FORM n1][TO n2] TO itab2. (line:结构；itab：内表；idx索引值）

      - 插入-INSERT

        - 将行插入到内表中指定的位置，需要使用INSERT语句。对于索引表，可以指定某行的索引，则新航将插入到改索引所代表的行之前；对于哈希表，不能指定行的索引，系统会按照关键字将新行插入其特殊位置。给内表插入行可以为单行，也可以为多行，甚至可以把整个内表的数据插入到其他内表中。

        - 按照索引值插入（只能用于索引表）
          - INSERT line INTO itab INDEX idx

        - 按照关键字插入（可以用于所有表）
          - INSERT line INTO TABLE table

        - 多行插入
          - INSERT LINES OF itab1 [FORM n1][TO ne] INTO [TABLE] itab2 [INDEX idx]

      - 聚集附加--COLLECT

        - 这个语句的作用是将工作区里的关键字段值跟内表里的字段比较，如果相同的话不在内表里追加行，而是将工作区里的数值字段值跟内表里的相关行的字段值累加，然后将工作区里的数字值更新到内表的相关行里；如果比较后没有在内表里找到相关行就将工作区里的内容添加到内表里。

        - COLLECT line INTO ITAB

        - COLLECT实际运用中1oop的内表不需要排序，直接1oop累加计算。

        - 只有在 COLLECT 命令的所有非关键宇段均为数字（类型！P 或F)时才可在表中使用该命令。numeric (type l, P. or F)

        - 使用collect就要求所有的非key fields均是！,P或者F数据类型，另外要注意的是对于standard table而言，如果不指定key fields那么它的key fields就那些非1，P,F数据类型的fields, sorted table 和hashtable均必须指定key fields.

        - 注意：货币类型CURR实际类型是P，所以也可以使用collect累加

    - 内表数据的修改

      - MODIFY

        - 根据索引更改内表的语法如下；

        - MODIFY iitab[FORM wa] [INDEX idx] [TRANSPORTING f1 f2 ...]

        - 使用表关键字来更改内表行可以应用于所有类型的内表，语法如下；

        - MODIFY TABLE itab FROM wa [TRANSPORTING f1 f2...]
- 附

  - char类型去空格

    - 去前空格：CONDENSE  str 

    - 去左空格：SHIFT lv_value LEFT DELETING LEADING SPACE.

    - 去右空格：SHIFT lv_value RIGHT DELETING LEADING SPACE.
