# [SQL速通-007-源数据的筛选及CTE](https://www.bilibili.com/video/BV1NG411b7Me?spm_id_from=333.999.list.card_archive.click&vd_source=25ad87d81d08dd31ae67543201e6d536)

![SQL速通-007-源数据的筛选及CTE(1)](https://github.com/YQvQY/LearningCodingNotes/raw/main/SQL%E9%80%9F%E9%80%9ANotes/IMG/SQL-007-IMG(1).png)

## [WHERE (Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/queries/where-transact-sql?view=sql-server-ver16)

```sql
-- SELECT
SELECT [ ALL | DISTINCT ]   
    [TOP ( expression ) [PERCENT] [ WITH TIES ] ]   
    < select_list >   
    [ INTO new_table ]   
    [ FROM { <table_source> } [ ,...n ] ]   
    [ WHERE <search_condition> ]
    [ <GROUP BY> ]   
    [ HAVING < search_condition > ]
    
-- 今天的重点
[ WHERE <search_condition> ]
```

## [Search condition (Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/queries/search-condition-transact-sql?view=sql-server-ver16)

```sql
-- 搜索条件会在未来讲解
-- Syntax for SQL Server and Azure SQL Database  
  
<search_condition> ::=  
    MATCH (<graph_search_pattern>) | <search_condition_without_match> | <search_condition> AND <search_condition>

<search_condition_without_match> ::= 
    { [ NOT ] <predicate> | ( <search_condition_without_match> ) }   
    [ { AND | OR } [ NOT ] { <predicate> | ( <search_condition_without_match> ) } ]   
[ ...n ]   
  
<predicate> ::=   
    { expression { = | < > | ! = | > | > = | ! > | < | < = | ! < } expression   
    | string_expression [ NOT ] LIKE string_expression   
  [ ESCAPE 'escape_character' ]   
    | expression [ NOT ] BETWEEN expression AND expression   
    | expression IS [ NOT ] NULL   
    | expression IS [ NOT ] DISTINCT FROM   
    | CONTAINS   
  ( { column | * } , '<contains_search_condition>' )   
    | FREETEXT ( { column | * } , 'freetext_string' )   
    | expression [ NOT ] IN ( subquery | expression [ ,...n ] )   
    | expression { = | < > | ! = | > | > = | ! > | < | < = | ! < }   
  { ALL | SOME | ANY} ( subquery )   
    | EXISTS ( subquery )     }   
    
<graph_search_pattern> ::=
    { <node_alias> { 
                      { <-( <edge_alias> )- } 
                    | { -( <edge_alias> )-> }
                    <node_alias> 
                   } 
    }
  
<node_alias> ::=
    node_table_name | node_table_alias 

<edge_alias> ::=
    edge_table_name | edge_table_alias
```

```sql
-- 所有行FirstName字段都是'Timothy'
SELECT * FROM Person.Person
WHERE FirstName = 'Timothy'

-- Initial和FullName存在结果集中
SELECT CONCAT(FirstName ,' ' , LastName) AS FullName, 
CONCAT(SUBSTRING(FirstName,1,1),SUBSTRING(LastName,1,1)) AS Initial 
FROM Person.Person
-- WHERE语句只能筛选数据源，筛选原数据
WHERE Initial = 'Timothy' -- -> error here

-- 第一种解决方法：copy and paste,重复
-- -> 是一种解决方法但是非常不好，写代码时重复是错误之源，需要避免这种写法
SELECT CONCAT(FirstName ,' ' , LastName) AS FullName, 
CONCAT(SUBSTRING(FirstName,1,1),SUBSTRING(LastName,1,1)) AS Initial 
FROM Person.Person
WHERE CONCAT(SUBSTRING(FirstName,1,1),SUBSTRING(LastName,1,1))= 'TL'

-- 第二种解决方法：把结果集当作封装一另一个数据源
-- -> 这种方法用到了嵌套的子查询，在外层SELECT语句中嵌套了内层SELECT语句，这个内存的SELECT语句又是外层FROM的一部分。如果更复杂的情况会有更深的SELECT嵌套语句，会导致之后代码难懂和难改
SELECT * FROM
(SELECT CONCAT(FirstName ,' ' , LastName) AS FullName, 
CONCAT(SUBSTRING(FirstName,1,1),SUBSTRING(LastName,1,1)) AS Initial 
FROM Person.Person) AS PersonName
WHERE Initial='TL'

-- 第三种解决方法：Common Table Expression (CTE)
-- -> 把要嵌套在里面的SQL语句拿出来，提前表达出来，在后面直接使用这个提前表达出来的表。展开层层套娃的SELECT语句
WITH PersonName AS
(SELECT CONCAT(FirstName ,' ' , LastName) AS FullName, 
CONCAT(SUBSTRING(FirstName,1,1),SUBSTRING(LastName,1,1)) AS Initial 
FROM Person.Person)

SELECT * FROM PersonName
WHERE Initial='TL'
```

##### Reference:

- https://www.bilibili.com/video/BV1NG411b7Me?spm_id_from=333.999.list.card_archive.click&vd_source=25ad87d81d08dd31ae67543201e6d536