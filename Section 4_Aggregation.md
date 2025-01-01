## Question 1: Count the number of facilities

For our first foray into aggregates, we're going to stick to something simple. We want to know how many facilities exist - simply produce a total count.

```sql
SELECT COUNT(facid) FROM cd.facilities;
```

## Question 2: Count the number of expensive facilities

Produce a count of the number of facilities that have a cost to guests of 10 or more.

```sql
SELECT COUNT(facid) FROM cd.facilities WHERE guestcost >= 10;
```

## Question 3: Count the number of recommendations each member makes

Produce a count of the number of recommendations each member has made. Order by member ID.

```sql
SELECT recommendedby, COUNT(recommendedby)
FROM cd.members
WHERE recommendedby IS NOT NULL
GROUP BY recommendedby
ORDER BY recommendedby;
```

## Question 4: List the total slots booked per facility

Produce a list of the total number of slots booked per facility. For now, just produce an output table consisting of facility id and slots, sorted by facility id.

```sql
SELECT facid, SUM(slots)
FROM cd.bookings
GROUP BY facid
ORDER BY facid;
```

## Question 5: List the total slots booked per facility in a given month

Produce a list of the total number of slots booked per facility in the month of September 2012. Produce an output table consisting of facility id and slots, sorted by the number of slots.

```sql
SELECT facid, SUM(slots) AS "Total Slots"
FROM cd.bookings
WHERE DATE_PART('year', starttime) = 2012 AND DATE_PART('month', starttime) = 9
GROUP BY facid
ORDER BY "Total Slots";
```

## Question 6: List the total slots booked per facility per month

Produce a list of the total number of slots booked per facility per month in the year of 2012. Produce an output table consisting of facility id and slots, sorted by the id and month.

**Method #1:** Using a range of dates.

```sql
SELECT facid, DATE_PART('month', starttime) AS month, SUM(slots) AS "Total Slots"
FROM cd.bookings
WHERE DATE_PART('year', starttime) = 2012
GROUP BY facid, month
ORDER BY facid, month;
```

**Method #2:** Set fixed conditions for the year and month.

```sql
SELECT facid, SUM(slots) AS "Total Slots"
FROM cd.bookings
WHERE DATE_PART('year', starttime) = 2012 AND DATE_PART('month', starttime) = 9
GROUP BY facid
ORDER BY "Total Slots";
```

## Question 7: Find the count of members who have made at least one booking

Find the total number of members (including guests) who have made at least one booking.

```sql
SELECT COUNT(DISTINCT memid) FROM cd.bookings;
```

## Question 8: List facilities with more than 1000 slots booked

Produce a list of facilities with more than 1000 slots booked. Produce an output table consisting of facility id and slots, sorted by facility id.

```sql
SELECT facid, SUM(slots) AS "Total Slots"
FROM cd.bookings
GROUP BY facid
HAVING SUM(slots) > 1000
ORDER BY facid;
```

## Question 9: Find the total revenue of each facility

Produce a list of facilities along with their total revenue. The output table should consist of facility name and revenue, sorted by revenue. Remember that there's a different cost for guests and members!

```sql
SELECT fclt.name,  
       SUM(  
           CASE  
           WHEN mbrs.memid = 0 THEN guestcost * slots  
           ELSE membercost * slots  
           END) AS revenue  
FROM cd.members mbrs  
         JOIN cd.bookings bkgs ON mbrs.memid = bkgs.memid  
         JOIN cd.facilities fclt ON bkgs.facid = fclt.facid  
GROUP BY fclt.name  
ORDER BY revenue;
```

## Question 10: Find facilities with a total revenue less than 1000

Produce a list of facilities with a total revenue less than 1000. Produce an output table consisting of facility name and revenue, sorted by revenue. Remember that there's a different cost for guests and members!

```sql
SELECT name, revenue  
FROM (SELECT fclt.name,  
             SUM(CASE  
                     WHEN bkgs.memid = 0 THEN guestcost * slots  
                     ELSE membercost * slots  
                 END) AS revenue  
      FROM cd.bookings bkgs  
               JOIN cd.facilities fclt ON bkgs.facid = fclt.facid  
      GROUP BY fclt.name) AS temp  
WHERE revenue < 1000  
ORDER BY revenue;
```

## Question 11: Output the facility id that has the highest number of slots booked

Output the facility id that has the highest number of slots booked. For bonus points, try a version without a LIMIT clause. This version will probably look messy!

```sql
SELECT facid, SUM(slots) AS "Total Slots"
FROM cd.bookings
GROUP BY facid
ORDER BY "Total Slots" DESC
LIMIT 1;
```

## Question 12: List the total slots booked per facility per month, part 2

Produce a list of the total number of slots booked per facility per month in the year of 2012. In this version, include output rows containing totals for all months per facility, and a total for all months for all facilities. The output table should consist of facility id, month and slots, sorted by the id and month. When calculating the aggregated values for all months and all facids, return null values in the month and facid columns.

**Method #1**

```sql
--Using UNION ALL to combine columns from 3 separate tables  
--Total slots per facility per month in 2012:
SELECT facid, DATE_PART('month', starttime) AS month, SUM(slots)
FROM cd.bookings
WHERE DATE_PART('year', starttime) = 2012
GROUP BY facid, month
	UNION ALL
--Total slots from all facilities in 2012:
SELECT NULL, NULL, SUM(slots) AS slots
FROM cd.bookings
WHERE DATE_PART('year', starttime) = 2012
	UNION ALL
--Total slots per facility in 2012:
SELECT facid, NULL, SUM(slots) AS slots
FROM cd.bookings
WHERE DATE_PART('year', starttime) = 2012
GROUP BY facid
ORDER BY facid, month;
```

**Method #2**: Using CTE

```sql
WITH slot_table AS (
    SELECT facid, DATE_PART('month', starttime) AS month, slots
    FROM cd.bookings
    WHERE DATE_PART('year', starttime) = 2012
)
SELECT facid, month, SUM(slots)
FROM slot_table
GROUP BY facid, month
	UNION ALL  
SELECT facid, NULL, SUM(slots)
FROM slot_table
GROUP BY facid
	UNION ALL  
SELECT NULL, NULL, SUM(slots)
FROM slot_table
ORDER BY facid, month;
```

## Question 13: List the total hours booked per named facility

Produce a list of the total number of _hours_ booked per facility, remembering that a slot lasts half an hour. The output table should consist of the facility id, name, and hours booked, sorted by facility id. Try formatting the hours to two decimal places.

```sql
SELECT fclt.facid, name, ROUND((SUM(slots) / 2.00), 2) AS "Total Hours"
FROM cd.facilities fclt JOIN cd.bookings bkgs USING (facid)
GROUP BY fclt.facid
ORDER BY facid;
```

## Question 14: List each member's first booking after September 1st 2012

Produce a list of each member name, id, and their first booking after September 1st 2012. Order by member ID.

```sql
SELECT surname, firstname, memid, MIN(starttime)
FROM cd.bookings bkgs JOIN cd.members mbrs USING (memid)
WHERE starttime >= '2012-09-01'
GROUP BY surname, firstname, memid
ORDER BY memid;
```

## Question 15: Produce a list of member names, with each row containing the total member count

Produce a list of member names, with each row containing the total member count. Order by join date, and include guest members.

```sql
SELECT COUNT(memid) OVER() AS count, firstname, surname
FROM cd.members
ORDER BY joindate;
```

## Question 16: Produce a numbered list of members

Produce a monotonically increasing numbered list of members (including guests), ordered by their date of joining. Remember that member IDs are not guaranteed to be sequential.

```sql
SELECT DENSE_RANK() OVER(ORDER BY joindate), firstname, surname
FROM cd.members
ORDER BY joindate;
```

## Question 17: Output the facility id that has the highest number of slots booked, again

Output the facility id that has the highest number of slots booked. Ensure that in the event of a tie, all tieing results get output.

**Method #1**: Using CTE (Calculate the total slots booked for each facility → find the highest number of slots booked by a facility → return only the rows where the slots booked are the same as for the highest).

```sql
--Calculating total slots booked for each facid
WITH temp AS (
SELECT bookings.facid, SUM(slots) AS total FROM cd.bookings
GROUP BY facid
ORDER BY total DESC
)
--Selecting the facid with the total slots = the maximum
SELECT facid, total FROM temp
WHERE total = (SELECT MAX(total) FROM temp);
```

**Method #2**: Using windows functions with the RANK() function (calculate the number of slots booked for each facility, rank them, and pick out any at rank \#1)

```sql
SELECT facid, total FROM (
    SELECT facid, SUM(slots) AS total, RANK() OVER(
        ORDER BY SUM(slots) DESC) AS rank
    FROM cd.bookings
    GROUP BY facid  
    ) AS sub
WHERE rank = 1;
```

This works because the ranking of the window function is processed after the GROUP BY aggregation.

## Question 18: Rank members by (rounded) hours used

Produce a list of members (including guests), along with the number of hours they've booked in facilities, rounded to the nearest ten hours. Rank them by this rounded figure, producing output of first name, surname, rounded hours, rank. Sort by rank, surname, and first name.

```sql
SELECT firstname, surname, hours, RANK() OVER(
    ORDER BY hours DESC) AS rank
FROM(
SELECT firstname, surname, ((SUM(slots) + 10)/20 * 10) AS hours
FROM cd.members JOIN cd.bookings
    USING (memid)
GROUP BY memid) AS temp
ORDER BY rank, surname, firstname;
```

## Question 19: Find the top three revenue generating facilities

Produce a list of the top three revenue generating facilities (including ties). Output facility name and rank, sorted by rank and facility name.

```sql
SELECT name, rank FROM(
--The second layer applies the ranking by a window function
SELECT name, RANK() OVER(ORDER BY revenue DESC) AS rank
FROM
--This innermost query calculates the total revenue per facility
    (SELECT fclt.name AS name, SUM(
        CASE  
            WHEN memid = 0 THEN guestcost * slots
            ELSE membercost * slots
        END) AS revenue
    FROM cd.bookings bkgs JOIN cd.facilities fclt
    USING (facid)
    GROUP BY fclt.name
    ORDER BY revenue DESC) AS temp) AS temp2
WHERE rank <= 3;
```

## Question 20: Classify facilities by value

Classify facilities into equally sized groups of high, average, and low based on their revenue. Order by classification and facility name.

```sql
--The first cte is used for calculating total revenue per facility
WITH total_revenue AS(
SELECT fclt.name AS name, SUM(
        CASE  
            WHEN memid = 0 THEN guestcost * slots
            ELSE membercost * slots
        END) AS revenue
FROM cd.bookings bkgs JOIN cd.facilities fclt
USING (facid)
GROUP BY fclt.name
ORDER BY revenue DESC),
--We can create another cte for ranking using NTILE()
    ranking AS(
SELECT *, NTILE(3) OVER() rank
FROM total_revenue
    )

SELECT name,
CASE  
    WHEN rank = 1 THEN 'high'
    WHEN rank = 2 THEN 'average'
    ELSE 'low'
END  
FROM ranking;
```

Here we create 2 CTEs for calculating the total revenue and then dividing the facilities into 3 segments using NTILE().

## Question 21: Calculate the payback time for each facility

Based on the 3 complete months of data so far, calculate the amount of time each facility will take to repay its cost of ownership. Remember to take into account ongoing monthly maintenance. Output facility name and payback time in months, order by facility name. Don't worry about differences in month lengths, we're only looking for a rough value here!

```sql
--Calculate the average profit of the 3 months and minus the monthly maintenance
WITH monthly_income AS(
SELECT facilities.facid, name,
SUM(CASE  
    WHEN memid = 0 THEN guestcost * slots
    ELSE membercost * slots
END) / 3 - facilities.monthlymaintenance  AS avg_monthly_profit
FROM cd.bookings JOIN cd.facilities
ON bookings.facid = facilities.facid
GROUP BY facilities.facid, name
ORDER BY name)

SELECT facilities.name, initialoutlay / avg_monthly_profit
FROM cd.facilities JOIN monthly_income
USING (facid);
```

## Question 22: Calculate a rolling average of total revenue

For each day in August 2012, calculate a rolling average of total revenue over the previous 15 days. Output should contain date and revenue columns, sorted by the date. Remember to account for the possibility of a day having zero revenue. This one's a bit tough, so don't be afraid to check out the hint!

```sql
--1st CTE: Calculates total revenue for each day
WITH daily_rev AS(
SELECT starttime::date, SUM(
CASE  
    WHEN memid = 0 THEN guestcost * slots
    ELSE membercost * slots
END) AS total_rev
FROM cd.bookings JOIN cd.facilities
USING (facid)
GROUP BY starttime::date  
ORDER BY starttime::date),
--2nd CTE: Calculate the running avg for the preceding 15 days  
running_avg AS(
SELECT starttime AS date, AVG(total_rev) OVER(ROWS 14 PRECEDING)
FROM daily_rev)

SELECT *
FROM running_avg
WHERE EXTRACT(MONTH FROM date) = 8
AND EXTRACT(YEAR FROM date) = 2012;
```

