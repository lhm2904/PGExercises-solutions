# PGExercises-solutions
My solutions to the SQL practice problems on pgexercises.com

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

![image](https://github.com/user-attachments/assets/9428076e-a09f-40eb-ad0a-b2af5a485460)
