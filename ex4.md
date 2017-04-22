# 1. Create "bad" table
```
CREATE TABLE bad_videos_by_tag_year_table (
  tag text,
  added_year int,
  added_date timestamp,
  title text,
  description text,
  user_id uuid,
  video_id timeuuid,
  PRIMARY KEY ((video_id))
);
```
# 2. Check which order has this table
```
DESCRIBE TABLE bad_videos_by_tag_year_table;
```
And see that order is different from what we create before

Because first PARTITION KEYS, then CLUSTERING COLS, then other columns
```
describe table videos_by_tag_year 

CREATE TABLE killvideo.videos_by_tag_year (
    video_id uuid PRIMARY KEY,
    added_date timestamp,
    added_year int,
    description text,
    tag text,
    title text,
    user_id uuid ...
```

# 3. Use COPY and specify order
```
COPY bad_videos_by_tag_year_table (tag, added_year, video_id, added_date, description, title, user_id) 
FROM 'videos_by_tag_year.csv' WITH HEADER=true;
```

# 4. Check count (422 rows, we want all rows 797)
```
SELECT COUNT(*)
FROM bad_videos_by_tag_year_table;
```
It is different from videos table => bad_videos_by_tag_year_table does not match the number of rows 
imported from videos_by_tag_year.csv. Since videos_by_tag_year.csv duplicates 
video_id for each unique tag and year per video, Cassandra upserted several records during the COPY. 
video_id is not a proper partition key for this scenario.

# 5. Drop table
```
DROP TABLE bad_videos_by_tag_year_table;
```

# 6. Create table that allow users to query on tag and possible year ranges while avoiding upserts on import.
=> tag and year must be a part of primary key (because we want to query by them)

year must be a range => must be a clustering key + 

+ want to store data in descending order of added year (most recent videos first)
```
CREATE TABLE videos_by_tag_year (
  tag text, #must be partition key (to be in where clause)
  added_year int, #must be clustering key for allowing ranges + desc order
  video_id timeuuid, #must be clustering key to avoid situtaion when 2 videos have same tag and added_year (need to include uniqueness in primary key)
  # unique id (video_id) must be last to ensure uniqueness! (and avoid upserts)
  added_date timestamp,
  description text,
  title text,
  user_id uuid,
  PRIMARY KEY ((tag), added_year, video_id)
) WITH CLUSTERING ORDER BY ( added_year DESC, video_id ASC );
```

# 7. Check table order
```
DESCRIBE TABLE videos_by_tag_year;
```
Returns order we need:
```
cqlsh:killvideo> describe table videos_by_tag_year;

CREATE TABLE killvideo.videos_by_tag_year (
    tag text,
    added_year int,
    video_id timeuuid,
    added_date timestamp,
    description text,
    title text,
    user_id uuid,
    PRIMARY KEY (tag, added_year, video_id)
) WITH CLUSTERING ORDER BY (added_year DESC, video_id ASC)
```
So it returns order we need

# 8. Check count
```
cqlsh:killvideo> SELECT COUNT(*) FROM videos_by_tag_year ;

 count
-------
   797
```
It returns count we need

# 9. Query
querying for all videos with tag "cql" added before the year 2015.
```
SELECT * FROM videos_by_tag_year  WHERE tag = 'cql' AND added_year < 2015;
```
querying for all videos added before 2015. The query will fail.
```
SELECT * FROM videos_by_tag_year  WHERE added_year < 2015;
```
InvalidRequest: code=2200 [Invalid query] message="Cannot execute this query as it might involve data 
filtering and thus may have unpredictable performance. If you want to execute this query despite the 
performance unpredictability, use ALLOW FILTERING"

## Why bad?
Because it has to go through all nodes in cluster and ask for data. Try to avoid this.
