# 1. Create keyspace (table container) for videos
```
CREATE KEYSPACE killvideo WITH REPLICATION = {
    'class': 'SimpleStrategy', 
    'replication_factor': 1 };
```
# 2. Create table
Before creating switch to newly created namespace
```
USE killvideo;
```
Then create a table with primary key as video_id
```
CREATE TABLE videos (
     video_id TIMEUUID, #sortable uuid in time fashion. can generate using now()
     added_date TIMESTAMP, #timestamp=64int in cassandra
     description TEXT, #utf8
     title TEXT, #signed 32 bits
     user_id UUID, #uuid - unique identifier. can generate using uuid()
     PRIMARY KEY(video_id) #primary key must be always unique
);
```
Load from csv file data (need to start cqlsh from folder where file is). IMPORTANT! ORDER OF COLUMNS FROM VIDEOS TABLE MUST MATCH ORDER OF CSV FILE COLS.
```
COPY videos FROM 'videos.csv' WITH HEADER=true; #HEADER=true means skip header when reading
```
# 3. Check table characteristics
```
DESCRIBE TABLE videos;
```
