# Idea
* Creating UDT
* Altering tables
* Collections
* Merging data when copying with existing table

# 1. Truncate videos table (removes all rows from table)
```
TRUNCATE videos;
```

# 2. Alter table by adding tags field (tags are unique)
```
ALTER TABLE videos ADD tags set<TEXT>;
```

# 3. Copy data
```
COPY videos FROM 'videos.csv' WITH HEADER=true;
```

# 4. Create a user defined type called video_encoding
```
CREATE TYPE video_encoding (
   bit_rates SET<TEXT>,
   encoding TEXT,
   height INT,
   width INT,
);
```

# 5. Alter your table to add an encoding column of the video_encoding type.
```
ALTER TABLE videos ADD encoding FROZEN<video_encoding>;
```

# 6. Copy new data
```
COPY videos (video_id, encoding) FROM 'videos_encoding.csv' WITH HEADER=true;
```

# 7. Query data (first 10 rows)
No FIRST keyword, use LIMIT instead
```
SELECT * FROM videos LIMIT 10;
```
