{% include "./includes/header.md" %}

# SQL Workshop

## What is it

SQL (Structured Query Language) is a querying language, plain and simple.
If you've ever used formulas in Excel or Google Sheets, you'll feel some
familiarity here. The reason why SQL exists is at some point
the spreadsheet gets too large that it slows down, has too many sheets,
or the formulas get too complex to maintain.

## Why learn it

SQL has a place in every industry:

* software development
* business intelligence
* marketing analysis
* algorithmic trading
* data visualization
* etc

It is undoubtedly [one of the most valuable skills you can have](http://www.craigkerstiens.com/2019/02/12/sql-most-valuable-skill/).

## What does it look like

First, let's contrast it to what we may already know about spreadsheets

Excel Spreadsheet => SQL Database

Sheet => Table

The jargon is not too bad. Let's take a look at this overgrown spreadsheet, made up of many sheets.

<iframe src="https://docs.google.com/spreadsheets/d/e/2PACX-1vQvn5_JSY-1dgQwrsf3cSPmJ_HthuPLlmrLC-GbesIARErxPtGM-dyFRwUAmsziwXxXZeMo8ya8do_J/pubhtml?widget=true&amp;headers=false" width="100%" height="600" frameborder="0"></iframe>

We have a perfect representation of the database we'll be playing with. Let's download
and upload the database version of the same data.

1. Download the database [chinook.db](./chinook.db)
2. In the right panel, (or <a href="https://austincodingacademy.github.io/sqljs.org/" target="blank">in a new window</a>) load the database

<iframe src="https://austincodingacademy.github.io/sqljs.org/" width="50%" height="700" frameborder="0" style="float:right"></iframe>

### Querying

The most basic query is to just list everything from a single table. Let's look
at the `albums` table.

1. First, we **always** `SELECT` the fields that we want: `SELECT albums.Title`
2. The we **always** write `FROM` where the data is coming: `FROM albums`

These are the absolutely necessary part of every query

```sql
-- You can write comments like this
SELECT albums.Title
FROM albums; -- End your query with a semicolon
```

#### `ORDER BY`

So maybe not too impressive. Let's try some magic tricks. Maybe we can start by
`ORDER`ing them `BY` title.

```sql
SELECT albums.Title
FROM albums
ORDER BY albums.Title;
```

This will order by _ascending_ order (small to big)
You can also flip the order by using `DESC` (big to small)

```sql
SELECT albums.Title
FROM albums
ORDER BY albums.Title DESC;
```

#### `WHERE`

And we only want the ones that start with `Q`

```sql
SELECT albums.Title
FROM albums
WHERE albums.Title LIKE 'Q%'
ORDER BY albums.Title;
```

Notice the order of the `WHERE` and the `ORDER BY` statements. The order of these
statements are the first "gotcha". A quick cheatsheet for the order of the most
popular statements is

1. `SELECT`
2. `FROM`
3. `JOIN`
4. `WHERE`
5. `GROUP BY`
6. `ORDER BY`
7. `LIMIT`

#### `JOIN`

`JOIN` is one of the most powerful features of a relational database. In Excel,
you might have used something similar like `VLOOKUP`, but SQL `JOIN`s are much
more powerful.

If you like puzzles, you'll love joining. It's all about finding a column on
one table that matches values on another table. For instance, look up at the
`artists` table, and notice it has a `ArtistId` column. This is called the
**Primary Key**, meaning that every artist can be associated with this id, and
there are no duplicates.

Now look at the `albums` table. It also has an `ArtistId` column. Each one of these
numbers are referring to an id on the artists table. This is called a **Foreign Key**.
You can have duplicates in this column because multiple `albums` can belong to the
same artist. Let's join these tables together and view the artist for every album!

```sql
SELECT artists.Name, albums.Title
FROM artists
JOIN albums ON artists.ArtistId = albums.ArtistId;
```

Let's join `tracks` on `albums`!

```sql
SELECT albums.Title, tracks.Name
FROM albums
JOIN tracks ON tracks.AlbumId = albums.AlbumId;
```

Let's join `artists`, `albums`, and `tracks`!

```sql
SELECT artists.Name, albums.Title, tracks.Name
FROM artists
JOIN albums ON artists.ArtistId = albums.ArtistId
JOIN tracks ON tracks.AlbumId = albums.AlbumId;
```

Here is a map of our database to help solve our "mazes"

![sql-map](./chinook-map.jpg)

#### `GROUP BY`

That's a real nice query, but that's a lot of duplicate data, and what if I want
to know how many albums each artist had? Or how many tracks each album had? We
can run another query to `GROUP BY` the columns with duplicates together, and
count how many got squeezed.

```sql
SELECT artists.Name, albums.Title
FROM artists
JOIN albums ON artists.ArtistId = albums.ArtistId
GROUP BY artists.Name;
```

So this squeezed the `artists.Name`s together so there are now no duplicate, but
it just gives the first album, not very helpful. What we can't see here is that
all the albums are squished together also, even though it is only showing the first.
We can count those albums using the `COUNT` function

```sql
SELECT artists.Name, COUNT(albums.Title)
FROM artists
JOIN albums ON artists.ArtistId = albums.ArtistId
GROUP BY artists.Name;
```

So now let's `ORDER BY` the counts to see who has the most `albums`!

```sql
SELECT artists.Name, COUNT(albums.Title)
FROM artists
JOIN albums ON artists.ArtistId = albums.ArtistId
GROUP BY artists.Name
ORDER BY COUNT(albums.Title) DESC;
```

#### `LIMIT`

`LIMIT` is pretty straightforward. It returns only the number of rows you need.
Let's return the top 10 `artists` with the most `albums`.

```sql
SELECT artists.Name, COUNT(albums.Title)
FROM artists
JOIN albums ON artists.ArtistId = albums.ArtistId
GROUP BY artists.Name
ORDER BY COUNT(albums.Title) DESC
LIMIT 10;
```

### Pivot Tables

You'll notice some tables looking a little slim on details, such as the `playlist_track`
table. This is called a _Pivot_ table, or _Join_ table. It's only purpose is to
join _many_ rows from one table to _many_ rows of another table. This creates a
_many-to-many_ relationship, meaning many `tracks` can belong to many `playlist`, or
a track can appear on more than one playlist and a playlist can have more than one
track. You join them just the same, and can be very useful when trying to find a link
between multiple tables, like a maze.

```sql
SELECT playlists.name, tracks.Name
FROM playlists
JOIN playlist_track ON playlists.PlaylistId = playlist_track.PlaylistId
JOIN tracks ON tracks.TrackId = playlist_track.TrackId;
```

## Challenges

1. Which albums have the most tracks?
2. Which albums have the most track time?

   * HINT: You can use the `SUM()` function on `tracks.milliseconds`

3. Which `artists` have the most track time?
4. Which `playlists` have the most `tracks`?
5. Which `tracks` appears on the most `playlists`?
6. Which `playlists` have the most track time?

{% include "./includes/footer.md" %}
