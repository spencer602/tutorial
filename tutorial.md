# Views in SQL Tutorial

## Assumed Prerequisites:
The language of this tutorial assumes that you have a firm grasp of the fundamentals of relational database systems. The assumed prerequisite includes understanding how to create tables and querying tables using `SELECT` statements and `JOINS`. If any of the example problems (including syntax provided for the tables to be used for those examples) are unclear, it is strongly recommended to brush up on these topics before progressing through the material in this tutorial. 

## Introduction:
A view in SQL is a way to create a virtual table using specific information from one or many tables.  Tables from which the view is being derived can be traditional tables or even views themselves. Once created, a view can be queried just like a traditional table using the `SELECT` statement.

## Why bother learning about views?
There are many benefits to be gained from understanding when, where and how to use views properly. We will discuss three main benefits of using views in SQL: Efficiency, Security, and Abstraction.

Assume we have the following tables in our database:

    motorcycle(vin int, year int, make text);
    rider(id int, name text, age int);
    rides(id int, vin int);

### Productivity:
In many cases, we can use views to simplify queries. A common situation arises where we need to frequently reference a common join of desired attributes from several tables. Creating a view allows us to simply reference the view repeatedly implementing the query. For example, assume we are frequently interested in retrieving only the age of the rider and year of the motorcycle they ride. We find ourself constantly typing the same query. With views, we can create a view of the data of interest. Then, we can simply reference the view we created instead of typing the query repeatedly. 

### Security:
Security can be another benefit of using views. Views have very limited `UPDATE` capabilities (since they are better described as virtual tables, the concept of updating views can be ambiguous). Views can offer an interface for a user to have read-only access to data in the tables. Suppose we are on the marketing team for a brand of motorcycle. We would like to make our rider table available to the public for them to pull queries to see recent race results. For privacy purposes, we don’t want to make the id of the riders available to the public. We can quickly and easily create a view with the id of the riders absent to make available to the public. 

### Abstraction:
Discussing of the security benefits leads in to the last (discussed) benefit of using views: abstraction. When using a view, it is not necessary to understand the organization of the model or what each underlying relation (table) contains. Instead, we can simply query the view containing the data of interest. This benefit of abstraction gives us great flexibility with the number of different uses we can achieve from a relational database system. 

## How to create your first view:

The syntax for creating a view is very simple. Say we want to create a view titled `age_make_view` that shows us only the age of the rider and make of the motorcycle for every `rides` instance. We will use the following code:

    CREATE VIEW age_make_view 
    AS SELECT age, make
    FROM motorcycle, rider, rides
    WHERE rides.id = rider.id AND rides.vin = motorcycle.vin

Notice in this syntax, we didn’t provide explicit attribute names for the fields in the view. When the attributes assigned to the view are all unique and NOT derived, we don’t have to specify attribute names (they will inherit the attribute names from the owning table). If the attribute names are NOT all unique, or the values will be derived (thus don’t have names to inherit), we must include attribute names using the syntax as follows:

    CREATE VIEW age_make_view(rider_age, motorcycle_make) 
    AS SELECT age, make
    FROM motorcycle, rider, rides
    WHERE rides.id = rider.id AND rides.vin = motorcycle.vin

Note that in the first `CREATE VIEW` example, we could have chosen to give the view explicit attribute names even though we didn’t have to. 

## Example Using A View:
Lets illustrate the previous two code blocks with an example. Assume we have a table 

    motorcycle(vin int, year int, make text)

and want to create a view of only the new motorcycles called `new_motorcycle`. We can accomplish this with the following code:

    CREATE VIEW new_motorcycle AS 
    SELECT * FROM motorcycle WHERE year > 2015;

Now, say we are still only interested in new motorcycles, but we don’t have a need for viewing the vin number. Also, we want the year attribute to be titled `motorcycle_year` and make to be titled `motorcycle_make`. We can accomplish this with the following code:

    CREATE VIEW new_motorcycle(motorcycle_year, motorcycle_make) AS 
    SELECT year, make FROM motorcycle WHERE year > 2015;

When we need to issue a retrieval query against a view, we use the same syntax as we would for querying a table. 

    SELECT * 
    FROM view_name
    WHERE condition1;

When we no longer need to reference the view, we can drop it with the `DROP VIEW` command as follows:

    DROP VIEW view_name;

It is important to remember that the virtual table associated with a view is strongly dependent on its owning table. If data used in the view is changed in the owning table, the data is also changed in the view. This includes adding, modifying, or removing data from the tables. The data in the view represents the data derived from the owning tables at the time of referencing the view. In that sense, it is safe to think of the view as being updated every time the data in the owning table is updated. Try the following example to illustrate this concept.

    create table motorcycle(vin int, year int, make text);

    insert into motorcycle(1510, 2017, ‘husqvarna’);
    insert into motorcycle(1201, 2017, ‘ktm’);
    insert into motorcycle(1368, 2018, ‘husqvarna’);
    insert into motorcycle(1911, 2017, ‘honda’);
    insert into motorcycle(1228, 2008, ‘honda’);
    insert into motorcycle(1114, 2019, ‘ktm’);
    insert into motorcycle(1575, 2019, ‘husqvarna’);
    insert into motorcycle(1603, 2007, ‘yamaha’);

    create view temp as select * from motorcycle;

    select * from motorcycle
    select * from temp;

    delete from motorcycle where year = 2017;

    select * from motorcycle;
    select * from temp;

As expected, the first two `SELECT` statements reveal identical data. However, it might not be intuitive that the final two select statements reveal identical data. This illustrates the fact that a view gathers its data given its specific query whenever it is referenced. 

Now let's introduce a few examples to illustrate the topics we have covered in the tutorial. Below is the syntax to create a database of motorcycles, riders, and which riders ride which bikes. Note that some riders ride several bikes and some bikes are ridden by several riders. 

    drop table if exists motorcycle;
    drop table if exists rider;
    drop table if exists rides;

    create table motorcycle(vin int, year int, make text);
    create table rider(id int, name text, age int);
    create table rides(id int, vin int);

    insert into motorcycle values(1510, 2017, 'husqvarna');
    insert into motorcycle values(1201, 2017, 'ktm');
    insert into motorcycle values(1368, 2018, 'husqvarna');
    insert into motorcycle values(1911, 2017, 'honda');
    insert into motorcycle values(1228, 2008, 'honda');
    insert into motorcycle values(1114, 2019, 'ktm');
    insert into motorcycle values(1575, 2019, 'husqvarna');
    insert into motorcycle values(1603, 2007, 'yamaha');
    insert into motorcycle values(1519, 2018, 'kawasaki');

    insert into rider values(575, 'Todd', 35);
    insert into rider values(602, 'Spencer', 32);
    insert into rider values(557, 'Mike', 35);
    insert into rider values(519, 'DJ', 36);
    insert into rider values(603, 'Nathan', 27);
    insert into rider values(331, 'Jon', 31);
    insert into rider values(93, 'Evan', 36);

    insert into rides values(575, 1510);
    insert into rides values(575, 1368);
    insert into rides values(575, 1575);
    insert into rides values(602, 1114);
    insert into rides values(557, 1228);
    insert into rides values(519, 1519);
    insert into rides values(603, 1603);
    insert into rides values(331, 1911);
    insert into rides values(93, 1368);
    insert into rides values(93, 1575);

## Example 1:
Let’s work through this example together. Then you can work through some problems on your own. This example should realize the benefit of efficiency as discussed in the beginning of the tutorial. Suppose we are frequently interested in listing the names of the rider and the make of the motorcycle for every `rides` instance. Instead of scripting a `SELECT` statement to achieve this every time we need it, we can create a view to reference as follows:

    CREATE VIEW rides_view AS 
    SELECT rider.name, motorcycle.make 
    FROM motorcycle, rider, rides 
    WHERE motorcycle.vin = rides.vin AND rider.id = rides.id;

This should be very straight forward. We are using the familiar `SELECT` statement to gather relevant attributes from the join of the three tables. The next example should be a bit trickier. Try it on your own. 

## Example 2:
Suppose Husqvarna wants to brag about the result of their race bikes at last weekends race. They want to offer the motorcycles table to be queried along with race results and riders. However, they are concerned with releasing specific VIN numbers of the racer’s bikes. Explain how we can solve this problem, and then give the necessary code to solve the problem. (Hint: see the above paragraph discussing using views as a security measure)

## Example 3:
As mentioned earlier, some bikes are ridden by multiple riders. Create a list of bikes ridden by only one rider, where that one rider also rides a bike that is ridden by multiple riders. (Hint: see the above paragraph discussing using views as a tool for abstraction to make this compound problem less complex)

## References: 

techtud: Understanding VIEWs in SQL
https://www.techtud.com/video-lecture/understanding-views-sql
Monday, November 26, 2018

Elmasri, R; Navathe, S. B  (2016) Fundamentals of Database Systems, Seventh Edition
Hoboken, New Jersey: Pearson
