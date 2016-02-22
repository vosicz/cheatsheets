#PL/SQL CHEATSHEET


Print boolean variable:

```
   dbms_output.put_line(
       case
          when output is null then 'NULL'
          when output then 'TRUE'
          else 'FALSE'
       end
   );
```


For each every record in table:

```
FOR rec IN (SELECT * FROM table)
    LOOP
        dbms_output.put_line(rec.column);
    END LOOP;
```

FOR/LOOP Example:

```
FOR Lcntr IN 1..500000
LOOP
   Insert into LE_TABLE (ID,TYPE) values ('ID_' || Lcntr, 'XX');
END LOOP;

```

Random string item from array

```
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
```
ops$tkyte@DEV8I.WORLD> create or replace procedure p3
  2  as
  3      l_cursor    int;
  4      l_status    int;
  5      l_x            dbms_sql.number_table;
  6      l_y            dbms_sql.date_table;
  7      l_z            dbms_sql.varchar2_table;
  8  begin
  9      for i in 1 .. 1000
 10      loop
 11          l_x(i) := i;
 12          l_y(i) := sysdate+i;
 13          l_z(i) := 'this is row ' || i;
 14      end loop;
 15      l_cursor := dbms_sql.open_cursor;
 16      dbms_sql.parse( l_cursor,
 17                     'insert into t (x,y,z)
 18                      values (:x,:y,:z)',
 19                      dbms_sql.native );
 20      dbms_sql.bind_array( l_cursor, ':x', l_x );
 21      dbms_sql.bind_array( l_cursor, ':y', l_y );
 22      dbms_sql.bind_array( l_cursor, ':z', l_z );
 23      l_status := dbms_sql.execute( l_cursor );
 24      dbms_sql.close_cursor(l_cursor);
 25  end;
 26  /

Procedure created.

P3 is a complex optimization of P2.  We will use array processing to send to the kernel 
1000 rows to be inserted -- instead of calling into the kernel 1000 times to insert a 
row.
```

##MY FUNCTIONS

###BOOL_TO_STRING FUNCTION
```
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

## INSERT - BINDED ARRAY
```
DECLARE
  table_subject dbms_sql.varchar2_table ;
  table_message dbms_sql.varchar2_table;
  v_index NUMBER := 0;
    l_cursor    int;
  l_status    int;

BEGIN


   FOR Lcntr IN 1..100000
      
      LOOP
       v_index := v_index +1;
       
      table_subject(v_index) := 'subj_' || v_index;
      table_message(v_index) := 'message_' || v_index;

       
      END LOOP;
      
      
      l_cursor := dbms_sql.open_cursor;
      dbms_sql.parse( l_cursor,
                      'INSERT INTO le_table(subject, message) VALUES(:table_subject, :table_message)',
                       dbms_sql.native );
       dbms_sql.bind_array( l_cursor, ':table_subject', table_subject );
       dbms_sql.bind_array( l_cursor, ':table_message', table_message );

       l_status := dbms_sql.execute( l_cursor );
       dbms_sql.close_cursor(l_cursor);

END;

```

