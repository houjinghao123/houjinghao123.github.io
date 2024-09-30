+++
title = 'MySQL触发器'
date = 2024-09-29T17:34:22+08:00
categories = ["MYSQL"]
tags= ["mysql","数据库"]
+++

# **触发器**

## **1.介绍**

触发器是与表有关的数据库对象，指在insert/update/delete之前(BEFORE)或之后(AFTER)，触发并执行触发器中定义的SQL语句集合。

`使用别名OLD和NEW来引用触发器中发生变化的记录内容`，这与其他的数据库是相似的。现在触发器还只支持行级触发，不支持语句级触发。触发器的这种特性可以协助应用在数据库端确保数据的完整性,日志记录,数据校验等操作 。



| **触发器类型** | **NEW和 OLD**                                        |
| -------------- | ---------------------------------------------------- |
| INSERT型触发器 | NEW表示将要或者已经新增的数据                        |
| UPDATE型触发器 | OLD表示修改之前的数据, NEW表示将要或已经修改后的数据 |
| DELETE型触发器 | OLD表示将要或者已经删除的数据                        |

## **2.语法**

1）创建
````mysql
create trigger trigger_name
before/after insert/update/delete
on tbl_name for each row --行级触发器
begin
  trigger_stmt;
end;
````



2)查看
````mysql
show triggers;
````

3)删除
````mysql
drop trigger [schema_name.]trigger_name; --如果没有指定schema_name,默认为当前数据库。
````

## **案例**
````mysql
-- 通过触发器记录 tb_user 表的数据变更日志，将变更日志插入到日志表user_logs中, 包含增加,修改 , 删除 ;

-- 插入数据触发器
create trigger tb_user_insert_trigger
    after insert on tb_user for each row
begin
    insert into user_logs(id, operation, operate_time, operate_id, operate_params)
        values(null,'insert',now(),new.id, concat('插入的数据内容为:
id=',new.id,',name=',new.name, ', phone=', NEW.phone, ', email=', NEW.email, ',
profession=', NEW.profession));
end;
````


````mysql
-- 修改数据触发器

create trigger tb_user_update_trigger
    after update on tb_user for each row
begin
    insert into user_logs(id, operation, operate_time, operate_id, operate_params)values(null,'update',now(),new.id,concat('更新之前的数据:id=',old.id,',name=',old.name, ', phone=', old.phone, ', email=', old.email, ', profession=', old.profession, ' | 更新之后的数据: id=',new.id,',name=',new.name, ', phone=', NEW.phone, ', email=', NEW.email, ', profession=', NEW.profession));
end;
````
````mysql
-- 删除数据触发器
create trigger tb_user_delete_trigger
    after delete on tb_user for each row
begin
    insert into user_logs(id, operation, operate_time, operate_id, operate_params)
        value (null,'delete',now(),old.id,
        concat('删除之前的数据: id=',old.id,',name=',old.name, ', phone=',old.phone, ', email=', old.email, ', profession=', old.profession));
end;
````

