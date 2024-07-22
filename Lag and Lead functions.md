# Lag and Lead windows functions in SQL

Practice using Lag and Lead.

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

# Basic Lead() and Lag() usage




### Using Lead(), let's create a table which ranks players in order of total points (total goals + total assists), and shows the total points of the next two players.
 

    SELECT
		player,
		GP,
		PTS,
		Lead(PTS, 1, 0) OVER(order by PTS desc) Pts1_Behind,
        Lead(PTS, 2, 0) OVER(order by PTS desc) Pts2_Behind
	FROM cbjhockey.scoring
	LIMIT 5;
	
| player           | GP | PTS | Pts1_Behind | Pts2_Behind |
|------------------|----|-----|-------------|-------------|
| Johnny Gaudreau  | 81 | 60  | 57          | 42          |
| Zach Werenski    | 70 | 57  | 42          | 35          |
| Kirill Marchenko | 78 | 42  | 35          | 34          |
| Boone Jenner     | 58 | 35  | 34          | 32          |
| Dmitri Voronkov  | 75 | 34  | 32          | 32          |

### Now to reverse it and show players ranked by points with the point totals of the 1 and 2 players above using Lag()

    SELECT
		player,
		GP,
		Pts,
		Lag(PTS, 1, 0) OVER(order by PTS desc) Pts1_Ahead,
        Lag(PTS, 2, 0) OVER(order by PTS desc) Pts2_Ahead
	FROM cbjhockey.scoring
    LIMIT 5;

| player           | GP | PTS | Pts1_Ahead | Pts2_Ahead |
|------------------|----|-----|------------|------------|
| Johnny Gaudreau  | 81 | 60  | 0          | 0          |
| Zach Werenski    | 70 | 57  | 60         | 0          |
| Kirill Marchenko | 78 | 42  | 57         | 60         |
| Boone Jenner     | 58 | 35  | 42         | 57         |
| Dmitri Voronkov  | 75 | 34  | 35         | 42         |




## Going a step further and partitioning by player position.

   

    SELECT
		player,
        Pos,
		GP,
		Pts,
		Lead(Pts, 1, 0) over (Partition by Pos order by Pts desc) Pts1,
		Lead(Pts, 2, 0) over (Partition by Pos order by Pts desc) Pts2
	FROM cbjhockey.scoring;

| player              | Pos | GP | Pts | Pts1 | Pts2 |
|---------------------|-----|----|-----|------|------|
| Boone Jenner        | C   | 58 | 35  | 32   | 30   |
| Cole Sillinger      | C   | 77 | 32  | 30   | 27   |
| Alexandre Texier    | C   | 78 | 30  | 27   | 23   |
| Adam Fantilli       | C   | 49 | 27  | 23   | 18   |
| Jack Roslovic       | C   | 40 | 23  | 18   | 16   |
| Sean Kuraly         | C   | 62 | 18  | 16   | 11   |
| Kent Johnson        | C   | 42 | 16  | 11   | 4    |
| Emil Bemström       | C   | 32 | 11  | 4    | 1    |
| Brendan Gaunce      | C   | 24 | 4   | 1    | 0    |
| Luca Del Bel Belluz | C   | 1  | 1   | 0    | 0    |
| Zach Werenski       | D   | 70 | 57  | 32   | 28   |
| Ivan Provorov       | D   | 82 | 32  | 28   | 26   |
| Damon Severson      | D   | 67 | 28  | 26   | 13   |
| Erik Gudbranson     | D   | 78 | 26  | 13   | 10   |
| Jake Bean           | D   | 72 | 13  | 10   | 10   |
| Adam Boqvist        | D   | 35 | 10  | 10   | 8    |
| David Ji?í?ek       | D   | 43 | 10  | 8    | 2    |
| Andrew Peeke        | D   | 23 | 8   | 2    | 1    |
| Jake Christiansen   | D   | 12 | 2   | 1    | 0    |
| Nick Blankenburg    | D   | 12 | 1   | 0    | 0    |
| Mathieu Olivier     | F   | 54 | 12  | 0    | 0    |
| Johnny Gaudreau     | LW  | 81 | 60  | 34   | 15   |
| Dmitri Voronkov     | LW  | 75 | 34  | 15   | 4    |
| Alexander Nylander  | LW  | 23 | 15  | 4    | 2    |
| James Malatesta     | LW  | 11 | 4   | 2    | 1    |
| Mikael Pyyhtia      | LW  | 17 | 2   | 1    | 0    |
| Eric Robinson       | LW  | 7  | 1   | 0    | 0    |
| Kirill Marchenko    | RW  | 78 | 42  | 29   | 26   |
| Yegor Chinakhov     | RW  | 53 | 29  | 26   | 9    |
| Justin Danforth     | RW  | 71 | 26  | 9    | 3    |


