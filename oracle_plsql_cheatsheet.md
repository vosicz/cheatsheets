#PL/SQL CHEATSHEET

##SQL

###Selects

####Values with no-brake space (Alt+255)
```sql
Select * from service where service_code like '% %'
```

###Inserts
```sql
INSERT INTO table_one SELECT * FROM table_two
```

###Date manipulation
```sql
--SYSDATE + 3 days
SELECT SYSDATE, SYSDATE + 3 FROM dual;

--SYSDATE + 3 hour
SELECT SYSDATE, SYSDATE + 3/24 FROM dual;

--SYSDATE + 3 minutes (24*60)
SELECT SYSDATE, SYSDATE + 3/1440 FROM dual;

--SYSDATE + 3 seconds (24*60*60)
SELECT SYSDATE, SYSDATE + 3/86400 FROM dual;
```

###Current date +- x minutes

```sql

SELECT CAST(to_char(SYSDATE - (1/24/60)*10, 'DD.MM.RR HH24:MI:SS') AS DATE) FROM dual;

```
###Disabling contraint on the table
```sql
SELECT * FROM all_constraints WHERE table_name = 'le_table';

EXECUTE IMMEDIATE 'ALTER TABLE le_table DISABLE CONSTRAINT PK_LE_TABLE;
```

###Select records from date and time range
```sql
SELECT * FROM le_table 
WHERE record_date 
BETWEEN TO_DATE ('24/02/2016 00:00:01', 'DD.MM.RR HH24:MI:SS') 
AND TO_DATE ('25/02/2016 12:12:12', 'DD.MM.RR HH24:MI:SS');
```
###EXPLAIN PLAN EXAMPLE
```sql
EXPLAIN PLAN FOR SELECT * FROM le_table;
```
Display PLAN_TABLE:
```sql
SELECT PLAN_TABLE_OUTPUT FROM TABLE(DBMS_XPLAN.DISPLAY());
```

###Hints

####Parallel
```
SELECT /*+ parallel(emp,4) */ empno, ename FROM emp;
```
####Append
```
INSERT /*+ APPEND */ INTO t1 SELECT * FROM all_objects;
```

##PL-SQL



###Print boolean variable:

```plsql
dbms_output.put_line(
    case
       when output is null then 'NULL'
       when output then 'TRUE'
       else 'FALSE'
    end
);
```

###Performance/tuning
```plsql
LOOP
  FETCH ref_cursor BULK COLLECT INTO ret_table LIMIT 100;
  EXIT WHEN ref_cursor%NOTFOUND;
END LOOP;   
```


###For each every record in table:

```plsql
FOR rec IN (SELECT * FROM table)
    LOOP
        dbms_output.put_line(rec.column);
    END LOOP;
```

###FOR/LOOP Example:

```plsql
FOR Lcntr IN 1..500000
LOOP
   Insert into LE_TABLE (ID,TYPE) values ('ID_' || Lcntr, 'XX');
END LOOP;

```
###Random string item from array

```plsql
DECLARE
    TYPE strArray IS VARRAY(3) of VARCHAR2(10);
    v_myarray strArray;
BEGIN
    v_myarray := strArray('item1', 'item2', 'item3');
    dbms_output.put_line(DBMS_RANDOM.value(1,3));
END;
```

##ORACLE DATABASE

###Database version:
```sql
SELECT * FROM V$VERSION;
```

##SNIPPETS

### Free space on tablespace
```sql
SELECT (sum(bytes) /1024) free_space_mb
FROM (
	SELECT bytes bytes
	FROM user_free_space
)
```

##INSERT - Array processing

[https://asktom.oracle.com/pls/asktom/f?p=100:11:::::P11_QUESTION_ID:455220177497](https://asktom.oracle.com/pls/asktom/f?p=100:11:::::P11_QUESTION_ID:455220177497)
```plsql
create or replace procedure p3
as
      l_cursor    int;
      l_status    int;
      l_x            dbms_sql.number_table;
      l_y            dbms_sql.date_table;
      l_z            dbms_sql.varchar2_table;
  begin
      for i in 1 .. 1000
      loop
          l_x(i) := i;
          l_y(i) := sysdate+i;
          l_z(i) := 'this is row ' || i;
      end loop;
      l_cursor := dbms_sql.open_cursor;
      dbms_sql.parse( l_cursor,
                     'insert into t (x,y,z)
                      values (:x,:y,:z)',
                      dbms_sql.native );
      dbms_sql.bind_array( l_cursor, ':x', l_x );
      dbms_sql.bind_array( l_cursor, ':y', l_y );
      dbms_sql.bind_array( l_cursor, ':z', l_z );
      l_status := dbms_sql.execute( l_cursor );
      dbms_sql.close_cursor(l_cursor);
   end;
/

```

## TABLES AND VIEWS

```sql
--------------------------------------------------------------------------------
--------------------------------------------------------------------------------
--  Viewing Information About Partitioned Tables and Indexes
--
--  https://docs.oracle.com/cd/B19306_01/server.102/b14231/partiti.htm#i1008364
--------------------------------------------------------------------------------
--------------------------------------------------------------------------------

SELECT * FROM dba_part_tables;
SELECT * FROM all_part_tables;
SELECT * FROM USER_PART_TABLES;

SELECT * FROM dba_tab_partitions;
SELECT * FROM all_tab_partitions;
SELECT * FROM USER_TAB_PARTITIONS;

SELECT * FROM all_tab_subpartitions;
SELECT * FROM USER_TAB_SUBPARTITIONS;

SELECT * FROM dba_part_key_columns;
SELECT * FROM all_part_key_columns;
SELECT * FROM USER_PART_KEY_COLUMNS;

SELECT * FROM dba_subpart_key_columns;
SELECT * FROM all_subpart_key_columns;
SELECT * FROM USER_SUBPART_KEY_COLUMNS;

SELECT * FROM dba_part_col_statistics;
SELECT * FROM all_part_col_statistics;
SELECT * FROM USER_PART_COL_STATISTICS;

SELECT * FROM dba_subpart_col_statistics;
SELECT * FROM all_subpart_col_statistics;
SELECT * FROM USER_SUBPART_COL_STATISTICS;

SELECT * FROM dba_part_histograms;
SELECT * FROM all_part_histograms;
SELECT * FROM USER_PART_HISTOGRAMS;

SELECT * FROM dba_subpart_histograms;
SELECT * FROM all_subpart_histograms;
SELECT * FROM USER_SUBPART_HISTOGRAMS;

SELECT * FROM dba_part_indexes;
SELECT * FROM all_part_indexes;
SELECT * FROM USER_PART_INDEXES;

SELECT * FROM dba_ind_partitions;
SELECT * FROM all_ind_partitions;
SELECT * FROM USER_IND_PARTITIONS;

SELECT * FROM dba_ind_subpartitions;
SELECT * FROM all_ind_subpartitions;
SELECT * FROM USER_IND_SUBPARTITIONS;

SELECT * FROM dba_subpartition_templates;
SELECT * FROM all_subpartition_templates;
SELECT * FROM USER_SUBPARTITION_TEMPLATES;
```

###LAST MODIFICATION OF THE TABLE

```sql
SELECT * 
FROM  user_tab_modifications
WHERE table_name = 'TURISTA_CONTRACT_MIG'
```

##MY FUNCTIONS

###BOOL_TO_STRING FUNCTION
```plsql
FUNCTION BOOL_TO_STRING(
bool      IN    boolean,
s_true    IN    VARCHAR2    DEFAULT   'true',
s_false   IN    VARCHAR2    DEFAULT   'false',
s_null    IN    VARCHAR2    DEFAULT   'null'
) RETURN VARCHAR2 IS
output VARCHAR2(50);
BEGIN

  output := CASE
              WHEN bool IS NULL THEN  s_null
              WHEN bool THEN  s_true
              ELSE  s_false
            END;

  RETURN output;

END;
```

##Examples
###Rowtype
```sql
declare
	v_row t_diagram%rowtype;
begin
	select * INTO v_row FROM t_diagram WHERE diagram_id  = 6850 AND package_id =972;
	dbms_output.put_line(v_row.version);
end;
```
