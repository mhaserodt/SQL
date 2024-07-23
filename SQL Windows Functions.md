# SQL Window Functions.

Window functions allow use of aggregate functions while each row maintains its separate identity.  Similar to GROUP BY, but in GROUP BY the data is condensed into one row per aggregation.



    SELECT
	    function() OVER()
	FROM table

The function() clause is the function we are using.  An aggregate function, ranking function, analytic functions, etc.  The OVER() clause defines the window; PARTITION BY, ORDER BY, ROWS.

### How to define the window

    OVER( 
    	[  PARTITION BY ]
    	[ ORDER BY ]
    	[ ROW or RANGE ]
    )
Partition = how we're dividing the data up
Order by = defines the logical order of the rows in the group
Rows/Range = the starting and end points of a group.

### PARTITION BY
		fun() OVER (PARTITION BY user_id)  will group data by user ID
		fun() OVER (PARTITION BY user_id, date) will group by both user ID and date.

### ORDER BY
			fun() OVER (PARTITION BY user_id ORDER BY date)
Partitions the data by user ID, and sorts it by date.

### ROWS/RANGE
Required an ORDER BY arguement
Rows specify a fixed number of rows that are before or after the current row.
Range specifies the range of values with respect to the value of the current row.

UNBOUNDED PRECEDING indicates it starts at the beginning of the data

UNBOUNDED FOLLOWING indicates it ends at the end of the data

### Aggregate Functions
SUM, AVG, MIN, MAX, etc to compute statistics within each group.

### Ranking Functions
ROW_NUMBER, RANK, DENSE_RANK, NTILE

### Analytic Functions
Access the values of multiple rows in a window
		Compare multiple rows and calculate the differences between rows
		LAG: access to the rows before the current row
		LEAD: access to the rows after the current row

