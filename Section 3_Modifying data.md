## Question 1: Insert some data into a table

The club is adding a new facility - a spa. We need to add it into the facilities table. Use the following values:

- facid: 9, Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.

```sql
INSERT INTO cd.facilities VALUES (9, 'Spa', 20, 30, 100000, 800);
```

## Question 2: Insert multiple rows of data into a table

In the previous exercise, you learned how to add a facility. Now you're going to add multiple facilities in one command. Use the following values:

- facid: 9, Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.
- facid: 10, Name: 'Squash Court 2', membercost: 3.5, guestcost: 17.5, initialoutlay: 5000, monthlymaintenance: 80.

```sql
INSERT INTO cd.facilities(facid, name, membercost, guestcost, initialoutlay, monthlymaintenance) VALUES
(9, 'Spa', 20, 30, 100000, 800), 
(10, 'Squash Court 2', 3.5, 17.5, 5000, 80);
```

## Question 3: Insert calculated data into a table

Let's try adding the spa to the facilities table again. This time, though, we want to automatically generate the value for the next facid, rather than specifying it as a constant. Use the following values for everything else:

- Name: 'Spa', membercost: 20, guestcost: 30, initialoutlay: 100000, monthlymaintenance: 800.

```sql
INSERT INTO cd.facilities (facid, name, membercost, guestcost, initialoutlay, monthlymaintenance)
(SELECT (SELECT MAX(facid) FROM cd.facilities) + 1, 'Spa', 20, 30, 100000, 800);
```

## Question 4: Update some existing data

We made a mistake when entering the data for the second tennis court. The initial outlay was 10000 rather than 8000: you need to alter the data to fix the error.

```sql
UPDATE cd.facilities
SET initialoutlay = 10000
WHERE name = 'Tennis Court 2';
```

## Question 5: Update multiple rows and columns at the same time

We want to increase the price of the tennis courts for both members and guests. Update the costs to be 6 for members, and 30 for guests.

```sql
UPDATE cd.facilities
SET membercost = 6, guestcost = 30
WHERE name LIKE '%Tennis Court%';
```

## Question 6: Update a row based on the contents of another row

We want to alter the price of the second tennis court so that it costs 10% more than the first one. Try to do this without using constant values for the prices, so that we can reuse the statement if we want to.

**Method #1**

```sql
UPDATE cd.facilities
SET guestcost = (SELECT guestcost * 1.1 FROM cd.facilities WHERE name = 'Tennis Court 1'),  
    membercost = (SELECT membercost * 1.1 FROM cd.facilities WHERE name = 'Tennis Court 1')
WHERE name = 'Tennis Court 2';
```

**Method #2:** Using the FROM clause with the UPDATE statement.

```sql
UPDATE cd.facilities fclt1
SET guestcost = fclt2.guestcost * 1.1,  
    membercost = fclt2.membercost * 1.1
FROM (SELECT * FROM cd.facilities WHERE name = 'Tennis Court 1') AS fclt2
WHERE fclt1.name = 'Tennis Court 2';
```

## Question 7: Delete all bookings

As part of a clearout of our database, we want to delete all bookings from the cd.bookings table. How can we accomplish this?

```sql
DELETE FROM cd.bookings;
```

## Question 8: Delete a member from the cd.members table

We want to remove member 37, who has never made a booking, from our database. How can we achieve that?

```sql
DELETE FROM cd.members
WHERE memid = 37;
```

## Question 9: Delete based on a subquery

In our previous exercises, we deleted a specific member who had never made a booking. How can we make that more general, to delete all members who have never made a booking?

```sql
DELETE FROM cd.members
WHERE memid IN (
    SELECT memid FROM cd.members
    EXCEPT
    SELECT memid FROM cd.bookings
    );
```
