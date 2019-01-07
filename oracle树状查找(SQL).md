# oracle 树状查找

##前言
当一张表中存储树状结构时，如何将其数据拉出。除了在代码中使用递归遍历去拉出数据外，可以直接使用SQL将表中的数据拉出。

##内容

### 语法
语法如下：
 > select [column] from [tableName] where [rule1] start with [rule2] connnect by [rule3]

###解释
rule1为筛选条件。
rule2是输入条件，如：parent_id = "3569"。
rule3为链接条件，一般为column1 = column2（当前节点的column1等于下一个节点的column2） ，如：parent_id = id（自下向上查找）、id = parent_id（自上向下查找）。
可在column中增加level，可查询出当前节点位于树的层级。

###样例

> select * from User 
where address = "地球"
start with parent_id = "3596"
connect by prior id = parent_id

查询User表找出address为地球、父id为3596的节点及其子节点（自上向下）