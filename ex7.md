# IDEA
Tables are denormalized, need just to add right primary key

PRIMARY KEYS must be UNIQUE!!

# videos by actor table
```
CREATE TYPE IF NOT EXISTS video_encoding (
   encoding TEXT,
   height INT,
   width INT,
   bit_rates SET<TEXT>
);

CREATE TABLE videos_by_actor (
  actor TEXT,
  added_date TIMESTAMP,
  video_id TIMEUUID,
  character_name TEXT,
  description TEXT,
  encoding FROZEN<video_encoding>,
  tags SET<TEXT>,
  title TEXT,
  user_id UUID,
  PRIMARY KEY ((actor), added_date, video_id, character_name)
) WITH CLUSTERING ORDER BY (added_date DESC, video_id ASC, character_name ASC);
```
## Workflow
Actor for video can be added on the same date (added_date)

=> need to add video_id

But actor can play different characters for the same file 

=> need to add character_name
# copy
```
COPY videos_by_actor FROM 'videos_by_actor.csv' WITH HEADER = true;
```

# query
```
SELECT * FROM videos_by_actor WHERE actor = 'Leonardo DiCaprio' LIMIT 10;

SELECT actor, added_date FROM videos_by_actor WHERE actor = 'Leonardo DiCaprio' LIMIT 10;
```

# videos by genre
```
CREATE TYPE IF NOT EXISTS video_encoding (
   encoding TEXT,
   height INT,
   width INT,
   bit_rates SET<TEXT>
);

CREATE TABLE videos_by_genre (
  genre TEXT,
  added_date TIMESTAMP,
  video_id TIMEUUID,
  description TEXT,
  encoding FROZEN<video_encoding>,
  tags SET<TEXT>,
  title TEXT,
  user_id UUID,
  PRIMARY KEY ((genre), added_date, video_id)
) WITH CLUSTERING ORDER BY (added_date DESC, video_id ASC);
```
genre can be added for the same added_date

=> add video_id

# copy
```
COPY videos_by_genre FROM 'videos_by_genre.csv' WITH HEADER = true;
```

# query
```
SELECT * FROM videos_by_genre WHERE genre = 'Musical' LIMIT 10;
```
