# Sparkify Database - Apache Cassandra

### Purpose and Context

The keyspace sparkify_ks has been set up to help Sparkify analyse the data they’ve been collecting on songs and user activity on their new music streaming app.  

The raw data resides in a directory of CSV files making the process of querying and analysing that information less than optimal. The new database aims to address this issue and provides an optimised solution for 3 specific questions on song play analysis.

---

### Question 1

***Give me the artist, song title and song's length in the music app history that was heard during  sessionId = 338, and itemInSession  = 4***

To answer this question we would need to run the following query:

```sql
SELECT artist, song, length <br>
FROM session_library <br>
WHERE session_id = 338 AND item_in_session = 4
```

Running this query would require a table with columns for artist, song, length, session_id and item_in_session. To allow the WHERE clause to operate on session id and item in session we can set the session id as the partition key and the item in session as the clustering column. This would also create a unique primary key for each row because a particular item in session value only occurs once for each session id.

Creating the new *session_library* table and running the SELECT statement gives us the following result:

Details of item 4 during session 338:

Artist: Faithless Music Matters <br>
Song Title: Mark Knight Dub <br>
Song Length (seconds): 495.31   

---

### Question 2

***Give me only the following: name of artist, song (sorted by itemInSession) and user (first and last name) for userid = 10, sessionid = 182***

To answer this question we would need to run the following query:

```sql
SELECT artist, song, item_in_session, first_name, last_name <br>
FROM user_session_library <br>
WHERE user_id=10 and session_id=182
```

Running this query would require a table with columns for artist, song, item in session, the user's first name and the user's last name. To allow the WHERE clause to operate on user id and session id efficiently, we can set a combination of user id and session id as a composite partition key to help spread the data across the system. Additionally we can set the item in session as a clustering column to order the songs. This combination of keys would also create a unique primary key for each row because a particular item in session value only occurs once for each session id under a user id.

Creating the new *user_session_library* table and running the SELECT statement gives us the following result:

Details of session id 338 under user id 10:

Item in Session: 0 <br>
Artist: Down To The Bone <br>
Song Title: Keep on Keepin' On <br>
User: Sylvie Cruz

Item in Session: 1 <br>
Artist: Three Drives <br>
Song Title: Greece <br>
User: Sylvie Cruz

Item in Session: 2 <br>
Artist: Sebastien Tellier <br>
Song Title: Kilometer <br>
User: Sylvie Cruz

Item in Session: 3 <br>
Artist: Lonnie Gordon <br>
Song Title: Catch You Baby (Steve Pitron & Max Sanna Radio Edit) <br>
User: Sylvie Cruz

---

### Question 3

***Give me every user name (first and last) in my music app history who listened to the song 'All Hands Against His Own'***

To answer this question we would need to run the following query:

```sql
SELECT first_name, last_name <br>
FROM songs_by_user <br>
WHERE song='All Hands Against His Own'
```

Running this query would require a table with columns for first name, last name, song and user id. To allow the WHERE clause to operate on song, we can set song as the partition key. To get all the users that listened to a particular song, we should also include user id as a clustering column. This combination of keys would create a unique primary key for each user and song. Not including the user id as a clustering column would end up storing only one user per song as Cassandra would overwrite the existing user for any given song with a new user if the primary key was simple song key.

Creating the new *songs_by_user* table and running the SELECT statement gives us the following result:

The response to the SELECT query gives us the following result:

Users who listened to All Hands Against His Own:

Jacqueline Lynch <br>
Tegan Levine <br>
Sara Johnson <br>
