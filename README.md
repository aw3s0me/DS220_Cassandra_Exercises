# DS220_Cassandra_Exercises
## 1. Always guarantee UNIQUENESS of primary keys
If key is not unique, add additional clustering keys, etc

So we could avoid upserts problem

* see exercise 7
## 2. Even if there is no entry in table, update can add new entry
## 3. Clustering keys
Roughly speaking they automatically add GROUP BY + ORDER BY
## 4. Cassandra model
Cells consisting of key-values
## 5. Can alter only by add/removing columns
## 6. Cannot alter Primary key
## 7. Counters have own type.
Need them to avoid concurrency problem when reading and writing counter value.
## 8. To execute cql - use SOURCE command
## 9. Order of primary key columns MATTER
```
PRIMARY KEY ((col1), col2, col3)
```
is not the same with
```
PRIMARY KEY ((col1), col3, col2)
```
## 10. Order of columns is important for COPY command
Need to check if order of columns in csv is the same with order of column
### Check order column by using DESCRIBE TABLE command
