# 0. Idea: 
Need to store number of videos for each tag.

Typically in SQL; have transaction that can write to the database this number. Read number, increment, save it.

But in Cassandra it is susceptible to race condition (concurrency) => need to use COUNTER feature

# 0. Requirement of querying
retrieve the number of videos for a particular category, querying on a tag, and optionally, on a range for the added year. 

# 1. Create table with counter
```
CREATE TABLE videos_count_by_tag (
   tag TEXT,
   added_year INT,
   video_count counter,
   PRIMARY KEY ((tag), added_year)
);
```

# 2. Load number of counts from cql file
```
SOURCE 'videos_count_by_tag.cql' ;
```

# 3. Run query
```
SELECT * FROM videos_count_by_tag ;
```

# 4. Incrementing (adding another a tag for another video by incrementing the video count for a category, and then querying the new count )
```
cqlsh:killvideo> UPDATE videos_count_by_tag SET video_count = video_count + 1
             ... WHERE tag = 'robocop' AND added_year = 2015;
cqlsh:killvideo> SELECT * FROM videos_count_by_tag WHERE tag = 'robocop' AND added_year = 2015;

 tag     | added_year | video_count
---------+------------+-------------
 robocop |       2015 |           2

```
