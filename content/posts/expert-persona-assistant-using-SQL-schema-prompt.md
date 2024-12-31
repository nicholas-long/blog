+++
title = 'Expert Persona Assistant Using SQL Schema Prompt'
date = 2024-12-30T15:18:03-06:00
draft = false
+++

# What is an Expert Persona?
A large language model that is provided schema information and asked to create queries to answer questions is called an "expert persona".
Such a model can be quickly integrated as an assistant into company workflow or as an API available to answer questions about the specific domain.

# Setup
- example database: We will use the [example "chinook.db" SQLite database](https://www.sqlitetutorial.net/sqlite-sample-database/).
- the schema for this database is provided at the end of this article for reference.
- ChatGPT ( CLI )

# Running Example Query
Include the full table schema after the question in a normal ChatGPT prompt or in the role text.
- example question
> which artist has the most sales?
- Example ChatGPT prompt:
```
using the following SQLite schema, answer this question: which artist has the most sales?
... insert schema here ...
```

## ChatGPT Results
To determine which artist has the most sales based on the provided SQLite schema, we need to identify the artist with the highest total sales. The SQL query to achieve this involves selecting the artist's name from the "artists" table and summing the total sales from the "invoices" and "invoice_items" tables, joined with the "tracks" and "albums" tables based on the relationships established in the schema. The query below accomplishes this task:

```sql
SELECT ar.Name AS ArtistName, SUM(ii.Quantity) AS TotalSales
FROM artists ar
INNER JOIN albums al ON ar.ArtistId = al.ArtistId
INNER JOIN tracks t ON al.AlbumId = t.AlbumId
INNER JOIN invoice_items ii ON t.TrackId = ii.TrackId
INNER JOIN invoices i ON ii.InvoiceId = i.InvoiceId
GROUP BY ar.ArtistId
ORDER BY TotalSales DESC
LIMIT 1;
```

This query retrieves the artist's name and calculates the total sales quantity by joining the necessary tables. The results are then grouped by the artist's unique identifier, ordered by total sales in descending order, and limited to obtain the artist with the highest sales volume.

## Answer from Running Query
```
Iron Maiden|140
```

# Schema Reference for Example
```sql
CREATE TABLE IF NOT EXISTS "albums"
(
    [AlbumId] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    [Title] NVARCHAR(160)  NOT NULL,
    [ArtistId] INTEGER  NOT NULL,
    FOREIGN KEY ([ArtistId]) REFERENCES "artists" ([ArtistId]) 
    ON DELETE NO ACTION ON UPDATE NO ACTION
);

CREATE TABLE IF NOT EXISTS "artists"
(
    [ArtistId] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    [Name] NVARCHAR(120)
);

CREATE TABLE IF NOT EXISTS "genres"
(
    [GenreId] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    [Name] NVARCHAR(120)
);

CREATE TABLE IF NOT EXISTS "invoices"
(
    [InvoiceId] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    [CustomerId] INTEGER  NOT NULL,
    [InvoiceDate] DATETIME  NOT NULL,
    [BillingAddress] NVARCHAR(70),
    [BillingCity] NVARCHAR(40),
    [BillingState] NVARCHAR(40),
    [BillingCountry] NVARCHAR(40),
    [BillingPostalCode] NVARCHAR(10),
    [Total] NUMERIC(10,2)  NOT NULL,
    FOREIGN KEY ([CustomerId]) REFERENCES "customers" ([CustomerId]) 
    ON DELETE NO ACTION ON UPDATE NO ACTION
);

CREATE TABLE IF NOT EXISTS "invoice_items"
(
    [InvoiceLineId] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    [InvoiceId] INTEGER  NOT NULL,
    [TrackId] INTEGER  NOT NULL,
    [UnitPrice] NUMERIC(10,2)  NOT NULL,
    [Quantity] INTEGER  NOT NULL,
    FOREIGN KEY ([InvoiceId]) REFERENCES "invoices" ([InvoiceId]) 
    ON DELETE NO ACTION ON UPDATE NO ACTION,
    FOREIGN KEY ([TrackId]) REFERENCES "tracks" ([TrackId]) 
    ON DELETE NO ACTION ON UPDATE NO ACTION
);

CREATE TABLE IF NOT EXISTS "media_types"
(
    [MediaTypeId] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    [Name] NVARCHAR(120)
);

CREATE TABLE IF NOT EXISTS "playlists"
(
    [PlaylistId] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    [Name] NVARCHAR(120)
);

CREATE TABLE IF NOT EXISTS "playlist_track"
(
    [PlaylistId] INTEGER  NOT NULL,
    [TrackId] INTEGER  NOT NULL,
    CONSTRAINT [PK_PlaylistTrack] PRIMARY KEY  ([PlaylistId], [TrackId]),
    FOREIGN KEY ([PlaylistId]) REFERENCES "playlists" ([PlaylistId]) 
    ON DELETE NO ACTION ON UPDATE NO ACTION,
    FOREIGN KEY ([TrackId]) REFERENCES "tracks" ([TrackId]) 
    ON DELETE NO ACTION ON UPDATE NO ACTION
);

CREATE TABLE IF NOT EXISTS "tracks"
(
    [TrackId] INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL,
    [Name] NVARCHAR(200)  NOT NULL,
    [AlbumId] INTEGER,
    [MediaTypeId] INTEGER  NOT NULL,
    [GenreId] INTEGER,
    [Composer] NVARCHAR(220),
    [Milliseconds] INTEGER  NOT NULL,
    [Bytes] INTEGER,
    [UnitPrice] NUMERIC(10,2)  NOT NULL,
    FOREIGN KEY ([AlbumId]) REFERENCES "albums" ([AlbumId]) 
    ON DELETE NO ACTION ON UPDATE NO ACTION,
    FOREIGN KEY ([GenreId]) REFERENCES "genres" ([GenreId]) 
    ON DELETE NO ACTION ON UPDATE NO ACTION,
    FOREIGN KEY ([MediaTypeId]) REFERENCES "media_types" ([MediaTypeId]) 
    ON DELETE NO ACTION ON UPDATE NO ACTION
);
```
