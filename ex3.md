# 0. Intro
if we do querying using 
```
SELECT * FROM videos WHERE title = 'The Original Grumpy Cat';
or
SELECT * FROM videos WHERE added_date < '2015-05-01';
```
We will get error (no secondary index on column), because our videos table has only one primary key - video_id

Therefore we need to create secondary index to enable querying on title and added_year

# 1. Create table for querying title and added_year
```
CREATE TABLE videos_by_title_year ( 
   title TEXT, 
   added_year INT, 
   added_date TIMESTAMP, 
   description TEXT, 
   user_id UUID, 
   video_id UUID, 
   PRIMARY KEY ((title, added_year)) #for querying by both title and added_year need composite key
);
```
# 2. Copy data


# 3. Query
```
SELECT * FROM videos_by_title_year WHERE title = 'Introduction To Apache Cassandra' AND added_year = 2014;
SELECT * FROM videos_by_title_year WHERE title = 'Sleepy Grumpy Cat' AND added_year = 2015;
SELECT * FROM videos_by_title_year WHERE title = 'Grumpy Cat: Slow Motion' AND added_year = 2015;
SELECT * FROM videos_by_title_year WHERE title = 'AzureDev' AND added_year = 2015;
```
## 3.1. Trying to query on just title or just year
Gives error: InvalidRequest: code=2200 [Invalid query] message="Partition key part added_year must be restricted since preceding part is"

Because hash function requires whole partition key columns, otherwise it returns incorrect hash => not possible to find node.
