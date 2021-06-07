
#### Altering schema

    ALTER TABLE fav DROP COLUMN oops;
    ALTER TABLE fav ALTER COLUMN content TYPE TEXT;
    ALTER TABLE fav ADD COLUMN howmuch INTEGER;

    \i 03-Techniques.sql (same folder you command psql)


##### Dates

    Historical dates

    DATE - 'YYYY-MM-DD'
    TIME - 'HH:MM:SS'
    TIMESTAMP - 'YYYY-MM-DD HH:MM:SS'
    TIMESTAMPZ - 'TIMESTAMP WITH TZ'

    SELECT NOW()::DATE, CAST(NOW() AS DATE),CAST(NOW() AS TIME);
    date_trunc()
    where created_at >= DATE_TRUNC('day',NOW());

##### DISTINCT/GROUP 

    Distinct only returns unique rows in a resultset
    Distinct ON limits duplicate removal to a set of columns
    Group by used by average.

    WHERE GROUP HAVING 

    GROUP IS SEMANTICALLY EQUALS TO DISTINCT

    
##### Subqueries

    It take the results from one query and set it to another query.

##### Transactions

    INSER INTO fav(post_id,account_id,howmuch)
    VALUES(1,1,1)
    RETURNING *;


    INSERT INTO fav(post_id,account_id,howmuch)
    VALUES(1,1,1)
    ON CONFLICT (post_id,account_id)
    DO UPDATE SET howmuch= fav.howmuch+1
    RETURNING *;

    BEGIN;
    SELECT howmuch FROM fav WHERE account_id= 1 and post_id = 1 FOR
    --time passes
    updae of FAV;
    update SET howmuch=999 where account_id = 1 and post_id=1
    ROLLBACK;


#### STORE PROCEDURES

    Isa bit of reusable code that runs inside the postgres.
    Perfomant 
    non-portable
    
    CREATE or REPLACE function trigger_Set_timestamps()
    RETURNS TRIGGER AS $$
    BEGIN
    NEW.updated_at = NOW(); 
    RETURN NEW;
    END;
    $$ LANGUAGE plpgsql;

    CREATE TRIGGER set_timestamp
    BEFORE UPDATE ON fav
    FOR EACH ROW
    EXECUTE PROCEDURE trigger_set_timestamps();

    UPDATE fav set howmuch=howmuch+1
    where post_id=1 AND account_id=1

    

    
