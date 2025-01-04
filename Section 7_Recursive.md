## Question 1: Find the upward recommendation chain for member ID 27

Find the upward recommendation chain for member ID 27: that is, the member who recommended them, and the member who recommended that member, and so on. Return member ID, first name, and surname. Order by descending member id.

```sql
WITH RECURSIVE recommend AS (  
    SELECT mem2.memid AS recommender, mem2.firstname, mem2.surname  
    FROM cd.members mem1 JOIN cd.members mem2  
        ON mem1.recommendedby = mem2.memid  
    WHERE mem1.memid = 27  
    UNION  
    SELECT mem2.memid, mem2.firstname, mem2.surname  
    FROM cd.members mem1 JOIN recommend  
        ON recommend.recommender = mem1.memid  
                         JOIN cd.members mem2  
        ON mem1.recommendedby = mem2.memid)  
  
SELECT *  
FROM recommend;
```

To clean up the recursive CTE so it looks neater, we can move the retrieval of firstname and surname to the outer query and use the CTE to only get the IDs:

```sql
WITH RECURSIVE recommend AS (  
    SELECT recommendedby AS recommender  
    FROM cd.members  
    WHERE memid = 27  
    UNION ALL  
    SELECT recommendedby  
    FROM recommend INNER JOIN cd.members  
        ON recommend.recommender = members.memid)  
  
SELECT recommender, firstname, surname  
FROM recommend JOIN cd.members  
ON recommender = memid;
```

This query is now much cleaner.

## Question 2: Find the downward recommendation chain for member ID 1

Find the downward recommendation chain for member ID 1: that is, the members they recommended, the members those members recommended, and so on. Return member ID and name, and order by ascending member id.

```sql
WITH RECURSIVE recommend AS (  
    SELECT memid  
    FROM cd.members  
    WHERE recommendedby = 1  
    UNION ALL  
    SELECT members.memid  
    FROM recommend JOIN cd.members  
        ON recommend.memid = members.recommendedby)  
  
SELECT memid, firstname, surname  
FROM recommend INNER JOIN cd.members  
USING (memid)  
ORDER BY memid;
```
## Question 3: Produce a CTE that can return the upward recommendation chain for any member

Produce a CTE that can return the upward recommendation chain for any member. You should be able to select recommender from recommenders where member=x. Demonstrate it by getting the chains for members 12 and 22. Results table should have member and recommender, ordered by member ascending, recommender descending.

```sql
WITH RECURSIVE  
    recommend AS (  
        SELECT memid, recommendedby AS recommender  
        FROM cd.members  
        UNION ALL  
        SELECT recommend.memid, members.recommendedby  
        FROM recommend INNER JOIN cd.members  
            ON recommend.recommender = members.memid)  
  
SELECT rec.memid AS member, recommender, firstname, surname  
FROM recommend rec JOIN cd.members mem  
    ON rec.recommender = mem.memid  
WHERE rec.memid = 12  
   OR rec.memid = 22  
ORDER BY member, recommender DESC;
```
