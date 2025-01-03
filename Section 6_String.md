## Question 1: Format the names of members

Output the names of all members, formatted as 'Surname, Firstname'

```sql
SELECT surname || ', ' || firstname  
FROM cd.members;
```

## Question 2: Find facilities by a name prefix

Find all facilities whose name begins with 'Tennis'. Retrieve all columns.

```sql
SELECT *  
FROM cd.facilities  
WHERE name LIKE 'Tennis%';
```

## Question 3: Perform a case-insensitive search

Perform a case-insensitive search to find all facilities whose name begins with 'tennis'. Retrieve all columns.

```sql
SELECT *  
FROM cd.facilities  
WHERE name ILIKE 'Tennis%';
```

## Question 4: Find telephone numbers with parentheses

You've noticed that the club's member table has telephone numbers with very inconsistent formatting. You'd like to find all the telephone numbers that contain parentheses, returning the member ID and telephone number sorted by member ID.

```sql
--Using LIKE
SELECT memid, telephone  
FROM cd.members  
WHERE telephone LIKE '(%)%';

--Using regex
SELECT memid, telephone  
FROM cd.members  
WHERE telephone ~ '^\(';
```

## Question 5: Pad zip codes with leading zeroes

The zip codes in our example dataset have had leading zeroes removed from them by virtue of being stored as a numeric type. Retrieve all zip codes from the members table, padding any zip codes less than 5 characters long with leading zeroes. Order by the new zip code.

```sql
SELECT LPAD(zipcode::text, 5, '0') AS zip  
FROM cd.members;
```

## Question 6: Count the number of members whose surname starts with each letter of the alphabet

You'd like to produce a count of how many members you have whose surname starts with each letter of the alphabet. Sort by the letter, and don't worry about printing out a letter if the count is 0.

```sql
SELECT LEFT(surname, 1) AS initial, COUNT(memid)  
FROM cd.members  
GROUP BY initial  
ORDER BY initial;
```

## Question 7: Clean up telephone numbers

The telephone numbers in the database are very inconsistently formatted. You'd like to print a list of member ids and numbers that have had '-','(',')', and ' ' characters removed. Order by member id.

```sql
SELECT memid, REGEXP_REPLACE(telephone, '^\(*([0-9]+)\)*[ -]([0-9]+)[ -]([0-9]+)$', '\1\2\3') AS telephone  
FROM cd.members;
```

