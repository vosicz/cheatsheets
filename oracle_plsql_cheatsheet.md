#PL/SQL CHEATSHEET


Print boolean variable:

```plsql
   dbms_output.put_line(
       case
          when output is null then 'NULL'
          when output then 'TRUE'
          else 'FALSE'
       end
   );
```


For each every record in table:

```plsql
FOR rec IN (SELECT * FROM table)
    LOOP
        dbms_output.put_line(rec.column);
    END LOOP;
```

FOR/LOOP Example:

```plsql
FOR Lcntr IN 1..500000
LOOP
   Insert into LE_TABLE (ID,TYPE) values ('ID_' || Lcntr, 'XX');
END LOOP;

```

Random string item from array

```plsql
DECLARE
    TYPE strArray IS VARRAY(3) of VARCHAR2(10);
    v_myarray strArray;
BEGIN
    v_myarray := strArray('item1', 'item2', 'item3');
    dbms_output.put_line(DBMS_RANDOM.value(1,3));
END;
```

##SNIPPETS

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

##Oracle database

###Database version:
```sql
SELECT * FROM V$VERSION;
```

