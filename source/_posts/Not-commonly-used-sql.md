title:  '不常用的sql'
date: 2015-10-16 14:22:47
tags: mysql
---
不常用的是最难记住的。
<!--more-->
## Alter

### 添加列
```
alter table table_name add column_name data_type unique/default/not-null comment 'comments';
```
### 删除列
```
alter table table_name drop column_name;
```
### 更新列
```
alter table table_name change old_column_name new_column_name data_type unique/default/not-null comment 'comments';
```
### 添加约束
```
alter table table_name add constraint fk_name foreign key(foreign_column_name) references foreign_table_name(references_column_name) on delete/update xxx;
```
mysql 外键约束 on delete和on update

on delete rule：
* restrict：约束
    如果存在从数据，不允许删除主数据。
* no action
    如果存在从数据，不允许删除主数据。
* cascade：级联
    删除主数据，顺便也删掉从数据。
* set null
    删除主数据，从数据外键的值设为null。

on update rule：
* restrict：约束
    修改主数据中被关联的field，如果有该主数据有从数据，不允许修改。
* no action
    修改主数据中被关联的field，如果有该主数据有从数据，不允许修改。
* cascade
    修改主数据中被关联的field，如果有该主数据有从数据，顺便一起修改从数据的外键值。
* set null
    修改主数据中被关联的field，如果有该主数据有从数据，从数据的外键值设为null。

### 删除约束
删除外键示例
```
alter table table_name drop foreign key fk_name;
```
## Trigger

### 添加trigger
```
delimiter $$

create trigger trigger_name
before update on table_name
for each row
begin
    SET new.updated = NOW();
end;
$$

delimiter ;
```


### 删除trigger
```
drop trigger trigger_name;
```

（未完待续）
