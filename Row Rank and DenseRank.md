# Row_Number(), Rank() and Dense_Rank in SQL


Data is that of the 2023-24 Columbus Blue Jackets NHL team.   It includes goals, assists, games played, and other relevant player data.  

### A sample of all data

    SELECT *
	FROM cbjhockey.scoring
    LIMIT 5;
| Rk | Player           | Age | Pos | GP | G_Scored | Assist | PTS | +/- | PIM | EV_goals | PP_goals | SH_goals | GW_goals | EV_asst | PP_asst | SH_asst | S   | S%   | TOI  | ATOI     | OPS | DPS | PS  | BLK | HIT | FOW | FOL | FO%  |
|----|------------------|-----|-----|----|----------|--------|-----|-----|-----|----------|----------|----------|----------|---------|---------|---------|-----|------|------|----------|-----|-----|-----|-----|-----|-----|-----|------|
| 1  | Johnny Gaudreau  | 30  | LW  | 81 | 12       | 48     | 60  | -27 | 22  | 11       | 1        | 0        | 3        | 31      | 17      | 0       | 160 | 7.5  | 1524 | 18:49    | 2.8 | 0.6 | 3.5 | 23  | 2   | 0   | 1   | 0    |
| 2  | Zach Werenski    | 26  | D   | 70 | 11       | 46     | 57  | 0   | 22  | 9        | 2        | 0        | 0        | 36      | 10      | 0       | 202 | 5.4  | 1712 | 24:27:00 | 4.5 | 3.3 | 7.8 | 130 | 23  | 0   | 0   |      |
| 3  | Kirill Marchenko | 23  | RW  | 78 | 23       | 19     | 42  | -5  | 14  | 14       | 9        | 0        | 1        | 15      | 4       | 0       | 193 | 11.9 | 1280 | 16:25    | 2.8 | 1.2 | 4   | 39  | 55  | 26  | 53  | 32.9 |
| 4  | Boone Jenner     | 30  | C   | 58 | 22       | 13     | 35  | -8  | 28  | 16       | 5        | 1        | 1        | 10      | 3       | 0       | 155 | 14.2 | 1166 | 20:06    | 2.3 | 1   | 3.3 | 74  | 119 | 604 | 509 | 54.3 |
| 5  | Dmitri Voronkov  | 23  | LW  | 75 | 18       | 16     | 34  | -6  | 52  | 12       | 6        | 0        | 2        | 14      | 2       | 0       | 142 | 12.7 | 1009 | 13:27    | 2.3 | 0.9 | 3.1 | 48  | 83  | 277 | 380 | 42.2 |

# Basic usage




### Row_Number() assigns a number to a row in order.  
Rank() will rank data in order.  If there is a tie it will assign the same rank to each then skip ahead to the next rank.
Dense_Rank() is similar to Rank(), but if there are ties it will assign each the same rank and go to the next number without skipping.
 

    SELECT
		player,
		GP,
		PTS,
		row_number() over (order by PTS desc) RowNumPTS,
        rank() over (order by PTS desc) RankPTS,
        dense_rank() over (order by PTS desc) DenseRankPTS
	FROM cbjhockey.scoring;
	
| player              | GP | PTS | RowNumPts | RankPTS | DenseRankPTS |
|---------------------|----|-----|-----------|---------|--------------|
| Johnny Gaudreau     | 81 | 60  | 1         | 1       | 1            |
| Zach Werenski       | 70 | 57  | 2         | 2       | 2            |
| Kirill Marchenko    | 78 | 42  | 3         | 3       | 3            |
| Boone Jenner        | 58 | 35  | 4         | 4       | 4            |
| Dmitri Voronkov     | 75 | 34  | 5         | 5       | 5            |
| Cole Sillinger      | 77 | 32  | 6         | 6       | 6            |
| Ivan Provorov       | 82 | 32  | 7         | 6       | 6            |
| Alexandre Texier    | 78 | 30  | 8         | 8       | 7            |
| Yegor Chinakhov     | 53 | 29  | 9         | 9       | 8            |
| Damon Severson      | 67 | 28  | 10        | 10      | 9            |
| Adam Fantilli       | 49 | 27  | 11        | 11      | 10           |
| Justin Danforth     | 71 | 26  | 12        | 12      | 11           |
| Erik Gudbranson     | 78 | 26  | 13        | 12      | 11           |
| Jack Roslovic       | 40 | 23  | 14        | 14      | 12           |
| Sean Kuraly         | 62 | 18  | 15        | 15      | 13           |
| Kent Johnson        | 42 | 16  | 16        | 16      | 14           |
| Alexander Nylander  | 23 | 15  | 17        | 17      | 15           |
| Jake Bean           | 72 | 13  | 18        | 18      | 16           |
| Mathieu Olivier     | 54 | 12  | 19        | 19      | 17           |
| Emil Bemström       | 32 | 11  | 20        | 20      | 18           |
| Adam Boqvist        | 35 | 10  | 21        | 21      | 19           |
| David Jiříček       | 43 | 10  | 22        | 21      | 19           |
| Patrik Laine        | 18 | 9   | 23        | 23      | 20           |
| Andrew Peeke        | 23 | 8   | 24        | 24      | 21           |
| Brendan Gaunce      | 24 | 4   | 25        | 25      | 22           |
| James Malatesta     | 11 | 4   | 26        | 25      | 22           |
| Trey Fix-Wolansky   | 11 | 3   | 27        | 27      | 23           |
| Carson Meyer        | 14 | 2   | 28        | 28      | 24           |
| Jake Christiansen   | 12 | 2   | 29        | 28      | 24           |
| Mikael Pyyhtia      | 17 | 2   | 30        | 28      | 24           |
| Nick Blankenburg    | 12 | 1   | 31        | 31      | 25           |
| Luca Del Bel Belluz | 1  | 1   | 32        | 31      | 25           |
| Eric Robinson       | 7  | 1   | 33        | 31      | 25           |


As we can see, Cole Sillinger and Ivan Provorov both had a total of 32 points.  Cole was assigned row number 6 and Ivan row 7 based on alphabetical order.  

They were both assigned Rank of 6.  Alexandre Texier was then assigned Rank of 8, because Cole and Ivan occupy the 6 and 7 ranks.

Cole and Ivan were both assigned Dense Ranks of 6. Alexandre Texier was then assigned Dense Rank of 7, because Cole and Ivan are both considered to occupy the 6th rank.  

Each method has its own place depending on how you want to analyze and display the data.


## Going a step further and partitioning by player position

    SELECT
		player,
        POS,
		GP,
		PTS,
		row_number() over (Partition by POS order by PTS desc) RowNumPTS,
        rank() over (Partition by POS order by PTS desc) RankPTS,
        dense_rank() over (Partition by POS order by PTS desc) DenseRankPTS
	FROM cbjhockey.scoring;
| player              | POS | GP | PTS | RowNumPts | RankPTS | DenseRankPTS |
|---------------------|-----|----|-----|-----------|---------|--------------|
| Boone Jenner        | C   | 58 | 35  | 1         | 1       | 1            |
| Cole Sillinger      | C   | 77 | 32  | 2         | 2       | 2            |
| Alexandre Texier    | C   | 78 | 30  | 3         | 3       | 3            |
| Adam Fantilli       | C   | 49 | 27  | 4         | 4       | 4            |
| Jack Roslovic       | C   | 40 | 23  | 5         | 5       | 5            |
| Sean Kuraly         | C   | 62 | 18  | 6         | 6       | 6            |
| Kent Johnson        | C   | 42 | 16  | 7         | 7       | 7            |
| Emil Bemström       | C   | 32 | 11  | 8         | 8       | 8            |
| Brendan Gaunce      | C   | 24 | 4   | 9         | 9       | 9            |
| Luca Del Bel Belluz | C   | 1  | 1   | 10        | 10      | 10           |
| Zach Werenski       | D   | 70 | 57  | 1         | 1       | 1            |
| Ivan Provorov       | D   | 82 | 32  | 2         | 2       | 2            |
| Damon Severson      | D   | 67 | 28  | 3         | 3       | 3            |
| Erik Gudbranson     | D   | 78 | 26  | 4         | 4       | 4            |
| Jake Bean           | D   | 72 | 13  | 5         | 5       | 5            |
| Adam Boqvist        | D   | 35 | 10  | 6         | 6       | 6            |
| David Jiříček       | D   | 43 | 10  | 7         | 6       | 6            |
| Andrew Peeke        | D   | 23 | 8   | 8         | 8       | 7            |


Similar to the above rankings we see Adam Boqvist and David Jiříček tied for 6th in both Rank and DenseRank among defensemen with 10 points each.  Andrew Peeke is then assigned Rank 8 and DenseRank 7.

## Just for fun let's find the players who were 2nd and 3rd in number of games played by position.

This can be accomplished with a quick subquery.  We will create a SELECT statement that will rank all of the players by position in order of games played (GP).  From that we will query out those players who have been ranked 2 or 3.

  

    SELECT *
    FROM(
    
    SELECT
    	player,
    	POS,
    	GP,
    	rank() over (Partition by POS order by GP desc) RankGP
    FROM cbjhockey.scoring
    ) a
    where a.RankGP = 2 or a.RankGP = 3

| player             | POS | GP | RankGP |
|--------------------|-----|----|--------|
| Cole Sillinger     | C   | 77 | 2      |
| Sean Kuraly        | C   | 62 | 3      |
| Erik Gudbranson    | D   | 78 | 2      |
| Jake Bean          | D   | 72 | 3      |
| Dmitri Voronkov    | LW  | 75 | 2      |
| Alexander Nylander | LW  | 23 | 3      |
| Justin Danforth    | RW  | 71 | 2      |
| Yegor Chinakhov    | RW  | 53 | 3      |
