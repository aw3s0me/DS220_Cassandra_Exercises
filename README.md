# DS220_Cassandra_Exercises
## 1. Always guarantee UNIQUENESS of primary keys
If key is not unique, add additional clustering keys, etc

So we could avoid upserts problem
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
