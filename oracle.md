#Oracle 相关事项

## 注意事项

所有表空间文件需放置在磁盘上，不可放置在内存临时盘中。

业务数据库至少需100G表空间大小，酌情添加数据库表空间文件。

##Oracle的安装
详见“centos 7.5 oracle11g部署.docx”

##Oracle优化
登入sqlplus
```
sqlplus / as sysdba
```

###redo日志组调整
- 1、创建3个新的日志组


```
ALTER DATABASE ADD LOGFILE GROUP4('/usr/oracle/app/oradata/orcl/redo04a.log','/usr/oracle/app/oradata/orcl/redo04b.log') SIZE 2048M;

ALTER DATABASE ADD LOGFILE GROUP5('/usr/oracle/app/oradata/orcl/redo05a.log','/usr/oracle/app/oradata/orcl/redo05b.log') SIZE 2048M;

ALTER DATABASE ADD LOGFILE GROUP6('/usr/oracle/app/oradata/orcl/redo06a.log','/usr/oracle/app/oradata/orcl/redo06b.log') SIZE 2048M;
 ```
- 2、切换当前日志到新的日志组

```
alter system switch logfile;

alter system switch logfile;

alter system switch logfile;

```
- 3、删除旧的日志组

```
alter database drop logfile group 1;

alter database drop logfile group 2;

alter database drop logfile group 3;
```

### undo表空间设置
根据需要设置空间建议至少20G。

- 1、 查看undo表空间

```
select tablespace_name , status , count(*) from dba_rollback_segs group by tablespace_name , status;

TABLESPACE_NAME                STATUS             COUNT(*)
------------------------------ ---------------- ----------
UNDOTBS1                       ONLINE                   10
SYSTEM                         ONLINE                    1
```
- 2 、创建新的undo表空间UNDOTBS2

‘’中的路径随系统路径变化而变化

```
create undo tablespace UNDOTBS2 datafile '/home/oracle/undotbs02.dbf' size 30720M;
```
- 3、切换UNDOTBS2为新的undo表空间

```
alter system set undo_tablespace = undotbs2 scope=both;
```
- 4、检查确认UNDOTBS1中没有ONLINE的segment

```
select status,segment_name from dba_rollback_segs where status not in ('OFFLINE') and tablespace_name='UNDOTBS1';

no rows selected
```
- 5、删除旧的UNDOTBS1

```
Drop tablespace UNDOTBS1 including contents and datafiles;
Tablespace dropped.
```
## oracle常用语句

- 给表空间增加数据文件

```
ALTER TABLESPACE name ADD DATAFILE 'PATH/*.DBF' SIZE 50M;
```
- 新增数据文件，并且允许数据文件自动增长

```
ALTER TABLESPACE nameADD DATAFILE 'PATH/*.DBF' SIZE 50M AUTOEXTEND ON NEXT 5M MAXSIZE 100M;
```
- 序列迁移语句生成

```
select 'create sequence '|| SEQUENCE_NAME || ' minvalue '||MIN_VALUE||' maxvalue '||MAX_VALUE||'   start with '||LAST_NUMBER||' increment by '||INCREMENT_BY||' cache '||50||' ;'    
from user_sequences
```
- 创建表空间：

```
create tablespace 表间名 datafile '数据文件名' size 表空间大小

```
- 创建用户

```
create user 用户名 identified by 密码 default tablespace 表空间表;
```
- 授给用户权限

```
GRANT connect, resource TO 用户名
```

##AWR报告生成

```
生成快照 exec dbms_workload_repository.create_snapshot();

生成快照 exec dbms_workload_repository.create_snapshot();

@？/rdbms/admin/awrrpt.sql
```

## 导出导入

- 1、查看管理员目录

```
select * from dba_directories;
```
- 2、导出数据
```
expdp 用户名/密码@orcl TABLES=emp,dept dumpfile=expdp.dmp DIRECTORY=目录名;
```
- 3、 导入数据

```
impdp 用户名/密码 DIRECTORY=目录名 DUMPFILE=expdp.dmp TABLE_EXISTS_ACTION=append;
```