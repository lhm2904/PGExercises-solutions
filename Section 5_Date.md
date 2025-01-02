## Question 1: Produce a timestamp for 1 a.m. on the 31st of August 2012

Produce a timestamp for 1 a.m. on the 31st of August 2012.

```sql
SELECT timestamp '2012-08-31 01:00:00';
```

## Question 2: Subtract timestamps from each other

Find the result of subtracting the timestamp '2012-07-30 01:00:00' from the timestamp '2012-08-31 01:00:00'

```sql
SELECT ('2012-08-31 01:00:00'::timestamp - '2012-07-30 01:00:00'::timestamp)::interval;
```

## Question 3: Generate a list of all the dates in October 2012

Produce a list of all the dates in October 2012. They can be output as a timestamp (with time set to midnight) or a date.

```sql
SELECT generate_series('2012-10-01'::date, '2012-10-31'::date,'1 day'::interval)::date
```

## Question 4: Get the day of the month from a timestamp

Get the day of the month from the timestamp '2012-08-31' as an integer.

```sql
SELECT EXTRACT(day FROM '2012-08-31'::date);
```

## Question 5: Work out the number of seconds between timestamps

Work out the number of seconds between the timestamps '2012-08-31 01:00:00' and '2012-09-02 00:00:00'

```sql
SELECT EXTRACT(EPOCH FROM (timestamp '2012-09-02 00:00:00' - '2012-08-31 01:00:00'))::int;
```

## Question 6: Work out the number of days in each month of 2012

For each month of the year in 2012, output the number of days in that month. Format the output as an integer column containing the month of the year, and a second column containing an interval data type.

```sql
--Method 1: Take the first date of the next month and minus 1.
--What we get is the final date of the current month, which is also the length of the month  

SELECT EXTRACT(MONTH FROM date_list) AS month,  
       DATE_PART('days', date_list + INTERVAL '1 month' - INTERVAL '1 day') AS days 
FROM  
(SELECT GENERATE_SERIES('2012-01-01'::date, '2012-12-01'::date, '1 month'::interval)::date AS date_list) t1;
```

## Question 7: Work out the number of days remaining in the month

For any given timestamp, work out the number of days remaining in the month. The current day should count as a whole day, regardless of the time. Use '2012-02-11 01:00:00' as an example timestamp for the purposes of making the answer. Format the output as a single interval value.

```sql
SELECT DATE_TRUNC('month', '2012-02-11 01:00:00'::timestamp) + INTERVAL '1 month - 1 day'  
        - DATE_TRUNC('day', '2012-02-11 01:00:00'::timestamp) + INTERVAL '1 day';
```

## Question 8: Work out the end time of bookings

Return a list of the start and end time of the last 10 bookings (ordered by the time at which they end, followed by the time at which they start) in the system.

```sql
SELECT starttime, starttime + (slots * INTERVAL '30 minutes') AS endtime  
FROM cd.bookings  
ORDER BY endtime DESC, starttime DESC  
LIMIT 10;
```

## Question 9: Return a count of bookings for each month

Return a count of bookings for each month, sorted by month

```sql
SELECT DATE_TRUNC('month', starttime) AS month, COUNT(bookid) AS bookings  
FROM cd.bookings  
GROUP BY month  
ORDER BY month;
```

## Question 10: Work out the utilisation percentage for each facility by month

Work out the utilisation percentage for each facility by month, sorted by name and month, rounded to 1 decimal place. Opening time is 8am, closing time is 8.30pm. You can treat every month as a full month, regardless of if there were some dates the club was not open.

```sql
SELECT name,  
       month,  
       ROUND((slots::numeric / (25 * days_in_month)::numeric) * 100, 1)  
           AS utilisation  
FROM (SELECT name,  
             DATE_TRUNC('month', starttime) AS month,  
             CAST(DATE_PART('days', DATE_TRUNC('month', starttime) + INTERVAL '1 month - 1 day') AS int)  
                 AS days_in_month,  
             SUM(slots) AS slots  
      FROM cd.bookings  
               JOIN cd.facilities  
                    USING (facid)  
      GROUP BY name, month, days_in_month  
      ORDER BY name, month) info;
```

> [!Note]
> **Utilization percentage = Total slots booked per facility per month / Total slots available.**
> 
> We can calculate the slots available per month: **25 * days in month** (8AM to 8:30PM working time → 12.5 hours → 25 slots). A slot is half an hour long.



