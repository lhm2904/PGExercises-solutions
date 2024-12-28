# PGExercises-solutions
My solutions to the SQL practice problems on [pgexercises.com](pgexercises.com)

# Exercises structure:
## Section 1: Basic

- Retrieve everything from a table
- Retrieve specific columns from a table
- Control which rows are retrieved
- Control which rows are retrieved - part 2
- Basic string searches
- Matching against multiple possible values
- Classify results into buckets
- Working with dates
- Removing duplicates, and ordering results
- Combining results from multiple queries
- Simple aggregation
- More aggregation

## Section 2: Joins and Subqueries

- Retrieve the start times of members' bookings
- Work out the start times of bookings for tennis courts
- Produce a list of all members who have recommended another member
- Produce a list of all members, along with their recommender
- Produce a list of all members who have used a tennis court
- Produce a list of costly bookings
- Produce a list of all members, along with their recommender, using no joins.
- Produce a list of costly bookings, using a subquery
## Section 3: Modifying data

- Insert some data into a table
- Insert multiple rows of data into a table
- Insert calculated data into a table
- Update some existing data
- Update multiple rows and columns at the same time
- Update a row based on the contents of another row
- Delete all bookings
- Delete a member from the cd.members table
- Delete based on a subquery

## Section 4: Aggregation

- Count the number of facilities
- Count the number of expensive facilities
- Count the number of recommendations each member makes.
- List the total slots booked per facility
- List the total slots booked per facility in a given month
- List the total slots booked per facility per month
- Find the count of members who have made at least one booking
- List facilities with more than 1000 slots booked
- Find the total revenue of each facility
- Find facilities with a total revenue less than 1000
- Output the facility id that has the highest number of slots booked
- List the total slots booked per facility per month, part 2
- List the total hours booked per named facility
- List each member's first booking after September 1st 2012
- Produce a list of member names, with each row containing the total member count
- Produce a numbered list of members
- Output the facility id that has the highest number of slots booked, again
- Rank members by (rounded) hours used
- Find the top three revenue generating facilities
- Classify facilities by value
- Calculate the payback time for each facility
- Calculate a rolling average of total revenue

## Section 5: Date

- Produce a timestamp for 1 a.m. on the 31st of August 2012
- Subtract timestamps from each other
- Generate a list of all the dates in October 2012
- Get the day of the month from a timestamp
- Work out the number of seconds between timestamps
- Work out the number of days in each month of 2012
- Work out the number of days remaining in the month
- Work out the end time of bookings
- Return a count of bookings for each month
- Work out the utilisation percentage for each facility by month

## Section 6: String

- Format the names of members
- Find facilities by a name prefix
- Perform a case-insensitive search
- Find telephone numbers with parentheses
- Pad zip codes with leading zeroes
- Count the number of members whose surname starts with each letter of the alphabet
- Clean up telephone numbers

## Section 7: Recursive

- Find the upward recommendation chain for member ID 27
- Find the downward recommendation chain for member ID 1
- Produce a CTE that can return the upward recommendation chain for any member


# Creating the Database
**Table schemas**:

```sql
CREATE TABLE cd.members
   (  
      memid integer NOT NULL,   
      surname character varying(200) NOT NULL,   
      firstname character varying(200) NOT NULL,   
      address character varying(300) NOT NULL,   
      zipcode integer NOT NULL,   
      telephone character varying(20) NOT NULL,   
      recommendedby integer,  
      joindate timestamp NOT NULL,
      CONSTRAINT members_pk PRIMARY KEY (memid),
      CONSTRAINT fk_members_recommendedby FOREIGN KEY (recommendedby)
           REFERENCES cd.members(memid) ON DELETE SET NULL
   );
```

```sql
CREATE TABLE cd.bookings
    (  
       bookid integer NOT NULL,   
       facid integer NOT NULL,   
       memid integer NOT NULL,   
       starttime timestamp NOT NULL,  
       slots integer NOT NULL,
       CONSTRAINT bookings_pk PRIMARY KEY (bookid),
       CONSTRAINT fk_bookings_facid FOREIGN KEY (facid) REFERENCES cd.facilities(facid),
       CONSTRAINT fk_bookings_memid FOREIGN KEY (memid) REFERENCES cd.members(memid)  
    );
```

```sql
CREATE TABLE cd.bookings
    (  
       bookid integer NOT NULL,   
       facid integer NOT NULL,   
       memid integer NOT NULL,   
       starttime timestamp NOT NULL,  
       slots integer NOT NULL,
       CONSTRAINT bookings_pk PRIMARY KEY (bookid),
       CONSTRAINT fk_bookings_facid FOREIGN KEY (facid) REFERENCES cd.facilities(facid),
       CONSTRAINT fk_bookings_memid FOREIGN KEY (memid) REFERENCES cd.members(memid)  
    );
```
![image](https://github.com/user-attachments/assets/7994cb25-f129-41cd-9cee-0e5c3e15a92a)

