# Common Table Expressions (CTEs), Subqueries, and Views.




CTEs, Subqueries, and Views store "transformations" not actual data.  This means they get executed every time you call them.

CTEs - only exist within one instance.  If you open a new instance, or close your existing instance you will need to recreate your CTE.

Views - are named queries that can be shared. 

Subqueries - are similar to CTEs though they're slower.  They may be necessary if using older database engines.



### A sample of all data

We have two tables of data from the 2023-24 Columbus Blue Jackets season.  One table includes scoring data such as number of goals, number of assists, games played, penalty minutes, etc.   The other table includes more personal data such as player date of birth, handedness, home country, age, weight, etc.

There wasn't a great primary key in either table, so I added a last name column to both to serve as that key.

    SELECT *
	FROM cbjhockey.scoring
    LIMIT 5;
| Rk | Player           | Last      | Age | Pos | GP | G_Scored | Assist | PTS | +/- | PIM | EV_goals | PP_goals | SH_goals | GW_goals | EV_asst | PP_asst | SH_asst | S   | S%   | TOI  | ATOI     |
|----|------------------|-----------|-----|-----|----|----------|--------|-----|-----|-----|----------|----------|----------|----------|---------|---------|---------|-----|------|------|----------|
| 1  | Johnny Gaudreau  | Gaudreau  | 30  | LW  | 81 | 12       | 48     | 60  | -27 | 22  | 11       | 1        | 0        | 3        | 31      | 17      | 0       | 160 | 7.5  | 1524 | 18:49    |
| 2  | Zach Werenski    | Werenski  | 26  | D   | 70 | 11       | 46     | 57  | 0   | 22  | 9        | 2        | 0        | 0        | 36      | 10      | 0       | 202 | 5.4  | 1712 | 24:27:00 |
| 3  | Kirill Marchenko | Marchenko | 23  | RW  | 78 | 23       | 19     | 42  | -5  | 14  | 14       | 9        | 0        | 1        | 15      | 4       | 0       | 193 | 11.9 | 1280 | 16:25    |
| 4  | Boone Jenner     | Jenner    | 30  | C   | 58 | 22       | 13     | 35  | -8  | 28  | 16       | 5        | 1        | 1        | 10      | 3       | 0       | 155 | 14.2 | 1166 | 20:06    |
| 5  | Dmitri Voronkov  | Voronkov  | 23  | LW  | 75 | 18       | 16     | 34  | -6  | 52  | 12       | 6        | 0        | 2        | 14      | 2       | 0       | 142 | 12.7 | 1009 | 13:27    |

    SELECT * 
	FROM cbjhockey.personal
	LIMIT 5;
| No. | Player           | Last        | Birth | Pos | Age | Wt  | S/C | Exp | Birth Date        |
|-----|------------------|-------------|-------|-----|-----|-----|-----|-----|-------------------|
| 39  | Tyler Angle      | Angle       | CA    | C   | 23  | 166 | L/- | 1   | September 30 2000 |
| 22  | Jake Bean        | Bean        | CA    | D   | 25  | 191 | L/- | 4   | June 9 1998       |
| 52  | Emil BemstrÃ¶m   | BemstrÃ¶m   | SE    | C   | 24  | 195 | R/- | 4   | June 1 1999       |
| 77  | Nick Blankenburg | Blankenburg | US    | D   | 25  | 177 | R/- | 2   | May 12 1998       |
| 27  | Adam Boqvist     | Boqvist     | SE    | D   | 23  | 182 | R/- | 4   | August 15 2000    |


# Basic usage




### Of course we could do a simple join 
With this little bit of easy data simple joins can bring both tables together.

 

	SELECT 
 	    a.Player,
	    a.Last,
	    a.Age,
	    a.Wt,
	    b.Player,
	    b.Last,
	    b.G_Scored,
	    b.Assist,
	    b.PTS
	FROM cbjhockey.personal a
		JOIN cbjhockey.scoring b
			ON a.Last = b.Last;
| Player           | Last        | Age | Wt  | Player           | Last        | G_Scored | Assist | PTS |
|------------------|-------------|-----|-----|------------------|-------------|----------|--------|-----|
| Jake Bean        | Bean        | 25  | 191 | Jake Bean        | Bean        | 4        | 9      | 13  |
| Emil BemstrÃ¶m   | BemstrÃ¶m   | 24  | 195 | Emil BemstrÃ¶m   | BemstrÃ¶m   | 5        | 6      | 11  |
| Nick Blankenburg | Blankenburg | 25  | 177 | Nick Blankenburg | Blankenburg | 1        | 0      | 1   |
| Adam Boqvist     | Boqvist     | 23  | 182 | Adam Boqvist     | Boqvist     | 1        | 9      | 10  |
| Yegor Chinakhov  | Chinakhov   | 22  | 204 | Yegor Chinakhov  | Chinakhov   | 16       | 13     | 29  |



## Let's say we'd like to find all players who have 20 or more penalty minutes.  We would like to display their age, weight, penalty minutes, and number of games played.

Age and weight are in the personal table.  Penalty minutes (PIM) and games played (GP) are in the scoring table. 

We could do subqueries to get the job done such as;

	 SELECT 
	    a.Player,
	    a.Last,
	    a.Age,
	    a.Wt,
	    b.Player,
	    b.Last,
	    b.PIM,
	    b.GP
	FROM cbjhockey.personal a
		JOIN (SELECT * FROM cbjhockey.scoring WHERE PIM >= 20) b
			ON a.Last = b.Last;
| Player           | Last       | Age | Wt  | Player           | Last       | PIM | GP |
|------------------|------------|-----|-----|------------------|------------|-----|----|
| Jake Bean        | Bean       | 25  | 191 | Jake Bean        | Bean       | 32  | 72 |
| Justin Danforth  | Danforth   | 30  | 190 | Justin Danforth  | Danforth   | 26  | 71 |
| Johnny Gaudreau  | Gaudreau   | 30  | 165 | Johnny Gaudreau  | Gaudreau   | 22  | 81 |
| Erik Gudbranson  | Gudbranson | 32  | 222 | Erik Gudbranson  | Gudbranson | 74  | 78 |
| David Ji?Ã­?ek   | Ji?Ã­?ek   | 20  | 189 | David Ji?Ã­?ek   | Ji?Ã­?ek   | 22  | 43 |
| Sean Kuraly      | Kuraly     | 31  | 213 | Sean Kuraly      | Kuraly     | 40  | 62 |
| Mathieu Olivier  | Olivier    | 26  | 217 | Mathieu Olivier  | Olivier    | 70  | 54 |
| Ivan Provorov    | Provorov   | 27  | 201 | Ivan Provorov    | Provorov   | 20  | 82 |
| Damon Severson   | Severson   | 29  | 205 | Damon Severson   | Severson   | 51  | 67 |
| Cole Sillinger   | Sillinger  | 20  | 201 | Cole Sillinger   | Sillinger  | 46  | 77 |
| Alexandre Texier | Texier     | 24  | 194 | Alexandre Texier | Texier     | 38  | 78 |
| Zach Werenski    | Werenski   | 26  | 213 | Zach Werenski    | Werenski   | 22  | 70 |

That works fine, but there's a little better way to do this using CTEs

    WITH PIM_over_20 AS (
		SELECT * FROM cbjhockey.scoring WHERE PIM >= 20
	)
	SELECT 
	    a.Player,
	    a.Last,
	    a.Age,
	    a.Wt,
	    b.Player,
	    b.Last,
	    b.PIM,
	    b.GP
	FROM cbjhockey.personal a
		JOIN PIM_over_20 b
			ON a.last = b.last;

| Player           | Last       | Age | Wt  | Player           | Last       | PIM | GP |
|------------------|------------|-----|-----|------------------|------------|-----|----|
| Jake Bean        | Bean       | 25  | 191 | Jake Bean        | Bean       | 32  | 72 |
| Justin Danforth  | Danforth   | 30  | 190 | Justin Danforth  | Danforth   | 26  | 71 |
| Johnny Gaudreau  | Gaudreau   | 30  | 165 | Johnny Gaudreau  | Gaudreau   | 22  | 81 |
| Erik Gudbranson  | Gudbranson | 32  | 222 | Erik Gudbranson  | Gudbranson | 74  | 78 |
| David Ji?Ã­?ek   | Ji?Ã­?ek   | 20  | 189 | David Ji?Ã­?ek   | Ji?Ã­?ek   | 22  | 43 |

We can see we've got the same results (I truncated them just because we didn't need to see every row).  The CTE however is a little easier to read, and in many cases performs better with large data sets. 

## Let's say we'd like to use the filter of players with 20 or more penalty minutes in multiple queries.

We could continue to build out our CTE in each query

    WITH PIM_over_20 AS (
		SELECT * FROM cbjhockey.scoring WHERE PIM >= 20
	)
or we could create a temp table that will work throughout our instance.

    CREATE VIEW PIM_over_20_temp AS SELECT * FROM cbjhockey.scoring WHERE PIM >= 20;

Then we can just query that temporary table.

	SELECT 
	    a.Player,
	    a.Last,
	    a.Age,
	    a.Wt,
	    b.Player,
	    b.Last,
	    b.PIM,
	    b.GP
	FROM cbjhockey.personal a
		JOIN PIM_over_20_temp b
			ON a.last = b.last;

| Player           | Last       | Age | Wt  | Player           | Last       | PIM | GP |
|------------------|------------|-----|-----|------------------|------------|-----|----|
| Jake Bean        | Bean       | 25  | 191 | Jake Bean        | Bean       | 32  | 72 |
| Justin Danforth  | Danforth   | 30  | 190 | Justin Danforth  | Danforth   | 26  | 71 |
| Johnny Gaudreau  | Gaudreau   | 30  | 165 | Johnny Gaudreau  | Gaudreau   | 22  | 81 |
| Erik Gudbranson  | Gudbranson | 32  | 222 | Erik Gudbranson  | Gudbranson | 74  | 78 |
| David Ji?Ã­?ek   | Ji?Ã­?ek   | 20  | 189 | David Ji?Ã­?ek   | Ji?Ã­?ek   | 22  | 43 |

Maybe we want to see the players' birth place (birth), penalty minutes (PIM), and points (PTS)
Since we already have that temporary table defined, we just need to update our select statement.

	SELECT
	    a.Player,
	    a.Last,
	    a.Birth,
	    b.Player,
	    b.Last,
	    b.PIM,
	    b.PTS
	FROM cbjhockey.personal a
		JOIN PIM_over_20_temp b
			ON a.last = b.last;

| Player          | Last       | Birth | Player          | Last       | PIM | PTS |
|-----------------|------------|-------|-----------------|------------|-----|-----|
| Jake Bean       | Bean       | CA    | Jake Bean       | Bean       | 32  | 13  |
| Justin Danforth | Danforth   | CA    | Justin Danforth | Danforth   | 26  | 26  |
| Johnny Gaudreau | Gaudreau   | US    | Johnny Gaudreau | Gaudreau   | 22  | 60  |
| Erik Gudbranson | Gudbranson | CA    | Erik Gudbranson | Gudbranson | 74  | 26  |
| David Ji?Ã­?ek  | Ji?Ã­?ek   | CZ    | David Ji?Ã­?ek  | Ji?Ã­?ek   | 22  | 10  |
