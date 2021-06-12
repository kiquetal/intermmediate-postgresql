##### IMPORTING FILES INTO POSTGRESQL


CREATE TABLE album (
id SERIAL,
title VARCHAR(128) UNIQUE,
PRIMARY KEY(id)
);

CREATE TABLE track (
id SERIAL,
title VARCHAR(128),
len INTEGER, rating INTEGER, count INTEGER,
album_id INTEGER REFERENCES album(id) ON DELETE CASCADE,
UNIQUE(title, album_id),
PRIMARY KEY(id)
);

DROP TABLE IF EXISTS track_raw;
CREATE TABLE track_raw
(title TEXT, artist TEXT, album TEXT, album_id INTEGER,
count INTEGER, rating INTEGER, len INTEGER);


#### Command \copy

    \copy xy_raw(x,y) FROM 'file.csv' WITH Delimiter ',';

    psql -h pg.pg4e.com -p 5432 -U pg4e_a61622bae1 -c "\COPY track_raw(title,artist,album,count,rating,len) FROM 'library.csv' WITH DELIMITER ',' CSV " -d pg4e_a61622bae1

    psql -h pg.pg4e.com -p 5432 -U pg4e_a61622bae1 -c "\copy unesco_raw(name,description,justification,year,longitude,latitude,area_hectares,category,state,region,iso) FROM 'whc-sites-2018-small.csv' WITH DELIMITER ',' CSV HEADER;" -d pg4e_a61622bae1


    
#### Exercises

    If you run the program multiple times in testing or with different files, make sure to empty out the data before each run.
    
    Load this CSV data file into the track_raw table using the \copy command. 
    Then write SQL commands 
    to insert all of the distinct albums into the album table 
    (creating their primary keys)
    and then set the album_id in the track_raw table using an SQL query like:
     UPDATE track_raw SET album_id = (SELECT album.id FROM album WHERE album.title = track_raw.album);
      
 
    1) INSERT INTO album(title) SELECT DISTINCT(album) from track_raw;

    2) UPDATE track_raw SET album_id = ( SELECT album.id from album where album.title = track_raw.album);

    Then use a INSERT ... SELECT statement to copy the corresponding data 
    from the track_raw table to the track table, 
    effectively dropping the artist and album text fields.
    
    3) INSERT INTO track(title,album_id,len) SELECT title,album_id,len from track_raw;
    To grade this assignment, the auto-grader will run a query like this on your database and look for the data it expects to see:
    
    SELECT track.title, album.title
    FROM track
    JOIN album ON track.album_id = album.id
    ORDER BY track.title LIMIT 3;

The expected result of this query on your database is:

        track	album
        A Boy Named Sue (live)	The Legend Of Johnny Cash
        A Brief History of Packets	Computing Conversations
        Aguas De Marco	Natural Wonders Music Sampler 1999


##### Exercises 2

    DROP TABLE unesco_raw;
    CREATE TABLE unesco_raw
    (name TEXT, description TEXT, justification TEXT, year INTEGER,
    longitude FLOAT, latitude FLOAT, area_hectares FLOAT,
    category TEXT, category_id INTEGER, state TEXT, state_id INTEGER,
    region TEXT, region_id INTEGER, iso TEXT, iso_id INTEGER);
    
    CREATE TABLE category (
    id SERIAL,
    name VARCHAR(128) UNIQUE,
    PRIMARY KEY(id)
    );

... More tables needed
To load the CSV data for this assignment use the following copy command. Adding HEADER causes the CSV loader to skip the first line in the CSV file. The \copy command must be one long line.
\copy unesco_raw(name,description,justification,year,longitude,latitude,area_hectares,category,state,region,iso) FROM 'whc-sites-2018-small.csv' WITH DELIMITER ',' CSV HEADER;
Normalize the data in the unesco_raw table by adding the entries to each of the lookup tables (category, etc.) and then adding the foreign key columns to the unesco_raw table. Then make a new table called unesco that removes all of the un-normalized redundant text columns like category.
If you run the program multiple times in testing or with different files, make sure to empty out the data before each run.

The autograder will look at the unesco table.

To grade this assignment, the program will run a query like this on your database and look for the data it expects to see:

        SELECT unesco.name, year, category.name, state.name, region.name, iso.name
        FROM unesco
        JOIN category ON unesco.category_id = category.id
        JOIN iso ON unesco.iso_id = iso.id
        JOIN state ON unesco.state_id = state.id
        JOIN region ON unesco.region_id = region.id
        ORDER BY category.name, unesco.name
        LIMIT 3;

   The expected result of this query on your database is:
   Name	Year	Category	State	Region	iso

        Khomani Cultural Landscape	2017	Cultural	South Africa	Africa	za
        al Saflieni Hypogeum	1980	Cultural	Malta	Europe and North America	mt
        ingvellir National Park	2004	Cultural	Iceland	Europe and North America	is


    Solution 1

    CREATE TABLE category (id SERIAL, name VARCHAR(128), primary key(id))
    CREATE TABLE iso (id SERIAL, name VARCHAR(128), primary key(id))
    CREATE TABLE region (id SERIAL, name VARCHAR(128), primary key(id))
