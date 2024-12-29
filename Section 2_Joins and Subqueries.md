## Question 1: Retrieve the start times of members' bookings

How can you produce a list of the start times for bookings by members named 'David Farrell'?

```sql
SELECT bookings.starttime
FROM cd.bookings JOIN cd.members
ON cd.bookings.memid = cd.members.memid
WHERE cd.members.surname = 'Farrell' AND cd.members.firstname = 'David';
```

## Question 2: Work out the start times of bookings for tennis courts

How can you produce a list of the start times for bookings for tennis courts, for the date '2012-09-21'? Return a list of start time and facility name pairings, ordered by the time.

```sql
SELECT bkgs.starttime, fclt.name
FROM cd.bookings bkgs INNER JOIN cd.facilities fclt
ON bkgs.facid = fclt.facid
WHERE fclt.name LIKE '%Tennis Court%' AND DATE(bkgs.starttime) = '2012-09-21'
ORDER BY bkgs.starttime;
```

## Question 3: Produce a list of all members who have recommended another member

How can you output a list of all members who have recommended another member? Ensure that there are no duplicates in the list, and that results are ordered by (surname, firstname).

```sql
SELECT DISTINCT mem2.firstname, mem2.surname
FROM cd.members mem1 INNER JOIN cd.members mem2
ON mem1.recommendedby = mem2.memid
ORDER BY mem2.surname, mem2.firstname;
```
## Question 4: Produce a list of all members, along with their recommender

How can you output a list of all members, including the individual who recommended them (if any)? Ensure that results are ordered by (surname, firstname).

```sql
SELECT mem1.firstname AS memfname, mem1.surname AS memsname, mem2.firstname AS recfname, mem2.surname AS recsname
FROM cd.members mem1 LEFT JOIN cd.members mem2
ON mem1.recommendedby = mem2.memid
ORDER BY mem1.surname, mem1.firstname;
```

## Question 5: Produce a list of all members who have used a tennis court

How can you produce a list of all members who have used a tennis court? Include in your output the name of the court, and the name of the member formatted as a single column. Ensure no duplicate data, and order by the member name followed by the facility name.

```sql
SELECT DISTINCT mbrs.firstname || ' ' || mbrs.surname AS member, fclt.name AS facility
FROM cd.members mbrs JOIN cd.bookings bkgs ON mbrs.memid = bkgs.memid
JOIN cd.facilities fclt ON bkgs.facid = fclt.facid
WHERE fclt.name LIKE '%Tennis Court%'
ORDER BY member, facility;
```

## Question 6: Produce a list of costly bookings

How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost, and do not use any subqueries.

```sql
SELECT mbrs.firstname || ' ' || mbrs.surname AS member,  
       fclt.name AS facility,  
       CASE  
           WHEN mbrs.memid = 0 THEN fclt.guestcost * bkgs.slots  
           ELSE fclt.membercost * bkgs.slots  
        END AS cost  
FROM cd.members mbrs  
         JOIN cd.bookings bkgs ON mbrs.memid = bkgs.memid  
         JOIN cd.facilities fclt ON bkgs.facid = fclt.facid  
WHERE DATE(bkgs.starttime) = '2012-09-14'  
  AND (  
    (mbrs.memid = 0 AND fclt.guestcost * bkgs.slots > 30) OR (mbrs.memid != 0 AND fclt.membercost * bkgs.slots > 30)  
    )  
ORDER BY cost DESC;
```

## Question 7: Produce a list of all members, along with their recommender, using no joins.

How can you output a list of all members, including the individual who recommended them (if any), without using any joins? Ensure that there are no duplicates in the list, and that each firstname + surname pairing is formatted as a column and ordered.

```sql
SELECT DISTINCT m.firstname || ' ' || m.surname AS member,
       (SELECT m2.firstname || ' ' || m2.surname AS recommender
            FROM cd.members m2
            WHERE m2.memid = m.recommendedby)
FROM cd.members m
ORDER BY member;
```

## Question 8: Produce a list of costly bookings, using a subquery

The Produce a list of costly bookings exercise contained some messy logic: we had to calculate the booking cost in both the WHERE clause and the CASE statement. Try to simplify this calculation using subqueries. For reference, the question was:

How can you produce a list of bookings on the day of 2012-09-14 which will cost the member (or guest) more than $30? Remember that guests have different costs to members (the listed costs are per half-hour 'slot'), and the guest user is always ID 0. Include in your output the name of the facility, the name of the member formatted as a single column, and the cost. Order by descending cost. 

```sql
SELECT member, facility, cost  
FROM (SELECT mbrs.firstname || ' ' || mbrs.surname AS member,  
             fclt.name AS facility,  
             CASE  
                 WHEN mbrs.memid = 0 THEN fclt.guestcost * bkgs.slots  
                 ELSE fclt.membercost * bkgs.slots  
             END AS cost  
      FROM cd.members mbrs  
               JOIN cd.bookings bkgs ON mbrs.memid = bkgs.memid  
               JOIN cd.facilities fclt ON bkgs.facid = fclt.facid  
      WHERE DATE(bkgs.starttime) = '2012-09-14'  
      ORDER BY cost DESC) sub  
WHERE cost > 30;
```