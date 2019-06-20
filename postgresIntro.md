# Intro to Postgresql 

Hi everybody. In this video we are going to get started with postgresql. Here is the website. But we are going to install it from the linux command line. Here I am in Linux. Let's get started. 

## installing

first show the postgresql webpage

installing on Linux  -- update -- sort of like the library card catalog. 

```

sudo apt-get update

sudo apt-get install -y install postgresql postgresql-client postgresql-contrib

```

Once you do this the postgresql server automatically starts and everytime you boot your machine it will automatically restart. This type of program is called a **service**



A service is simply a program that auto-starts and runs in the background.  For example, a database server like postgresql or Mysql. or a web server like apache or nginx. 

We can see what services we have running by typing the command



```
service --status-all
```

ok back to postgres

The first thing we are going to do is add a password to the root user whose username is `postgres`

```javascript
sudo -u postgres psql
\password postgres
## add a password
\q
## now login
psql -U postgres -h localhost

```



This looks great!



## 2. Creating databases and tables

We've installed a database server -- Postgresql. A database server can contain multiple databases and each database can contain one or more tables -- more precisely, zero or more.  Let me give you an example,   

A database is a collection of logically related tables.   -- logically related information.



Suppose we are the IT department of a small town in New Mexico. We have a variety of stuff we want to keep track of. Let's say we have a town library. So we might have a database associated with that.  In that database we might have tables representing things like

1. the books we have
2. the patrons
3. maybe a table keeping track of the books each patron checked out.
4. books we ordered
5. maybe a separate table for ebook holdings

The town has property tax so we might have a database for that



1. properties  (yr. built, lot size, house size, etc., address, location)
2. owners   (names, social security number, home address )
3. propertyOwners (who owns what)



The town has a number of employees so we might have a separate db representing that information. Information about employees, departments, hrs worked, vacation time, etc.

Maybe a db having to do with property in that town. or citizens of the town. So we don't glom all the information together in one huge database. Rather we divide it up into logical units. By **logical** I just mean in a way that makes sense. 

So a database server has multiple databases and each database has multiple tables. Tables are nothing fancy. Just like a table you might imagine with rows and columns.

### creating, listing, deleting, and using databases.

```
CREATE DATABASE library;
# show error without semi colon
\l -- list databases
\u -- use a database
DROP DATABASE library;


```

The SQL standard -- SQL:2016  -- backslash commands postgres specific.



### creating a table and inserting data

The syntax for creating a table is



```
CREATE TABLE table_name
(
	column_name_1  DATATYPE,
	column_name_2  DATATYPE
);
```



All keywords and identifiers are case insensitive so

```
CREATE TABLE books
Create TaBLE BoOkS
```

are equivalent. Digits are allowed in table names in Postgres but not in the SQL standard so to make your database as portable as possible do not use digits like `CREATE TABLE table_1` 

#### built in datatypes

Let's look at some basic datatypes



| type        | comment                                                      |
| ----------- | ------------------------------------------------------------ |
| INTEGER     |                                                              |
| NUMERIC     | contains decimal                                             |
| TEXT        | obvious  --somebody's name -- a movie title  -- have as many characters as you need |
| CHAR(2)     | no advantage --                                              |
| VARCHAR(30) | no advantage                                                 |
| DATE        | 1990-09-11                                                   |

With that in mind let's create our books table



```
CREATE TABLE books
(
	id INTEGER,
	author TEXT,
	title TEXT,
	acquired DATE
);
```



sweet. now to see what tables we have in our database and the structure of each table.



```
\d
\d books
```



### deleting a table

```
DROP TABLE books;
```

a drag typing this all in -- wouldn't it be nice if I could create a file of these commands and read that in. We could also share this database design with others. Well we can. 



### Reading in a file

two ways

First create a file in sublime

```
CREATE DATABASE library;
\c library

CREATE TABLE books
(
  id INTEGER,
  author TEXT,
  title  TEXT,
  acquired DATE
);
```



method 1:

```
psql -U postgres -h localhost -f library.sql
```

method 2:

```
\i library.sql
```





## Inserting data

Great. now we have a database table but it is empty -- we don't have any data in it. how do we add information to our table? With the insert command:

```
INSERT INTO books (id, author, title, acquired) VALUES (1, 'Ann Smith', 'Murder in the Javascript Lab', '2019-03-12');
```



How do we see what we inserted?

#### the simplified SELECT command

```
select * from books;
```

we can jumble up the order of the columns in an insert



```
INSERT INTO books (id, title, acquired, author) VALUES (2, 'Fibonacci Murders', '2019-03-12', 'Ron Zacharski');
```

and we can leave information out. say we don't know when something was acquired.



```
INSERT INTO books (title, author) VALUES ('Recursive W', 'Ann Mulkern');
```

### finally you can leave the column list off

```
INSERT INTO books VALUES (10, 'Gusty Healy', 'Death by SQL Injection', '2019-05-06');
```

**this is considered bad practice -- don't do it **

### inserting multiple rows

```
INSERT INTO books (id, author, title, acquired) VALUES
   (12, 'Ann Mulkern', 'Gusty Cooper and Raspberry Pi', '2018-09-12'),
   (13, 'Ann Mulkern', 'Gusty Cooper and his Electric Bike', '2018-09-12'),
   (14, 'Ann Mulkern', 'Gusty Cooper and his Repelotron', '2018-09-12');
```

## you try

come up with something creative.



# Part 2 - Enhancements

Make sure to keep notes. 

## Handlng I Don't Know.



Suppose we want to add the data:

```
INSERT INTO books (id, author, title, acquired) VALUES
   (12, 'Ann Mulkern', 'Gusty Cooper and Raspberry Pi', '2018-09-12'),
   (13, 'Ann Mulkern', 'Gusty Cooper and his Electric Bike', '2018-09-12'),
   (14, 'Ann Mulkern', 'Gusty Cooper and his Repelotron', '???');
```



but we don't know that last date. How do we add it in. we could always add it by itself by doing:



```
INSERT INTO books (id, author, title) VALUES (15, 'Dr. D', 'Romance in the OR');
```

but we could also add a NULL to our insert. (demo)



## not null

when we are designing our database table we might think that it doesn't make sense to have one column null. For example, it doesn't make sense for a book not to have a name. We can specify this buy doing:



```
CREATE TABLE books_revised (
    id INTEGER NOT NULL,
    author TEXT NOT NULL,
    title TEXT NOT NULL,
    acquired DATE);
```



let's see what happens when we try to add something that has a null in one of those columns.



### SERIAL

We might be getting tired of keeping track of that id number. Once we start writing our Javascript code, we could have our program keep track but that would be the wrong way to go. Postgres itself can auto increment this number. We do this by using the keyword SERIAL. 

Let me give you an example.

show in sublime



```

CREATE TABLE books_new
(
  id SERIAL,
  author TEXT NOT NULL,
  title  TEXT NOT NULL,
  acquired DATE
);
```

and show insertion



sweet



### default values



## ONE MORE THING: First normal form 1NF

Sublime editor walkthrough



A guy named Edgar Codd, one of the big names in databases, came up with some guidelines for how to construct database tables. This is called normalization, so database normalization. 

In this video we are going to talk about first normal form or 1NF. There are two rules for 1NF. Here they are

1. Each row of data must contain atomic values
2. Each row of data must have a unique identifier called the primary key

Let's go through each one by one. 



## Atomic values

There are 2 facets to this. One is a hard and fast rule and the other is a wishy washy rule. Kind of where you might say "it depends"

### Wishy Washy rule. can you break the data down further into parts and does it matter for the current database?

Example 1:

programmers

| Name              | location          |
| ----------------- | ----------------- |
| Ann Mulkern       | Virginia Beach VA |
| Ben Rhodes        | Washington DC     |
| Dr. Matt Carriker | San Antonio TX    |



| firstname | lastname | city           | state |
| --------- | -------- | -------------- | ----- |
| Ann       | Mulkern  | Virginia Beach | VA    |
| Ben       | Rhodes   | Reston         | VA    |
| Matt      | Carriker | San Antonio    | TX    |

The first table we could argue wasn't atomic because we could divide the columns into parts. And the second table, again we could argue, is better. We might want to search by state for example. 

Let's look at a slightly different table



| firstname | lastname | address             | city           | state |
| --------- | -------- | ------------------- | -------------- | ----- |
| Ann       | Mulkern  | 1290 Washington Ave | Virginia Beach | VA    |
| Ben       | Rhodes   | 107 Canyon Vista    | Reston         | VA    |
| Matt      | Carriker | 227 FM 207          | San Antonio    | TX    |



we might be tempted to divide that address into the number and street name but for most applications this doesn't matter. At some point you will have to say good enough. and dividing further would be silly. **it's subjective**

Now the hard and fast rule about atomic data.

1. you can't have several values of the same type in a single column
2. you can't have multiple columns with the same type of thing.

an example will clarify this. 



Here is some data we want to represent in a database table



Ann works out of our Boston MA office and knows Python and Javascript

Ben works out of our Boston MA office and knows Golang and Java

Clara works out of our Virginia Beach VA office and knows Python and R.



One way to represent this is 

| programmer | city           | state | languages         |
| ---------- | -------------- | ----- | ----------------- |
| Ann        | Boston         | MA    | Python Javascript |
| Ben        | Boston         | MA    | Golang Java       |
| Clara      | Virginia Beach | VA    | Python R          |

This violates condition 1 "A column cannot have several values of the same type in one column"  (not boston boston)

Option 2

| programmer | city           | state | language_one | language_twoJava |
| ---------- | -------------- | ----- | ------------ | ---------------- |
| Ann        | Boston         | MA    | Python       | Javascript       |
| Ben        | Boston         | MA    | Golang       | Java             |
| Clara      | Virginia Beach | VA    | Python       | R                |

This violates condition 2 "multiple columns cannot have the same type of data."

Later on we will learn how to design databases that have multiple tables and handle this beautifully. For now we can make this table meet the atomic rule by doing:



| programmer | city           | state | languages  |
| ---------- | -------------- | ----- | ---------- |
| Ann        | Boston         | MA    | Python     |
| Ann        | Boston         | MA    | Javascript |
| Ben        | Boston         | MA    | Golang     |
| Ben        | Boston         | MA    | Java       |
| Clara      | Virginia Beach | VA    | Python     |
| Clara      | Virginia Beach | VA    | R          |

This table meets the atomic criteria. 

### first normal form reminder

1. Each row of data must contain atomic values
2. Each row of data must have a unique identifier called the primary key

In this database we don't have a column that contains a unique identifier -- meaning that makes each row unique. Later we will learn other options for primary keys but for now we will make an id column.





| id   | programmer | city           | state | languages  |
| ---- | ---------- | -------------- | ----- | ---------- |
| 1    | Ann        | Boston         | MA    | Python     |
| 2    | Ann        | Boston         | MA    | Javascript |
| 3    | Ben        | Boston         | MA    | Golang     |
| 4    | Ben        | Boston         | MA    | Java       |
| 5    | Clara      | Virginia Beach | VA    | Python     |
| 6    | Clara      | Virginia Beach | VA    | R          |

Now we have a table in 1NF or first normal form.  

**synthetic key**

### declaring a primary key

there are two ways to declare a primary key in SQL



The first is on the line itself



```
CREATE DATABASE library;
\c library

CREATE TABLE books_new
(
  id SERIAL PRIMARY KEY,
  author TEXT NOT NULL,
  title  TEXT NOT NULL,
  acquired DATE
);
```



the second is at the end



```
CREATE TABLE users
(
  id SERIAL,
  name text,
  PRIMARY KEY(id)
);
```



use \d to show the results.



### summary

so far we

1. learned how to install postgresql on our machine
2. learned how to initially create a password for the default user postgres
3. learned how to create a database
4. learned how to show all databases and to delete dbs.
5. learned how to create and delete tables.
6. learned the basic data types (text, integer, numeric, date)
7. learned how to insert data into a table. 
8. learned about NULL
9. learned about SERIAL
10. learned about first normal form or 1NF.
11. learned how to declare a column to be the primary key -- must be unique
12. learned how to see the structure of a database table \d
13. learned how to see the entries in a table using SELECT.



### work through an example - 

tee shirt company  -- show off the ranch

bunker branding

stamp   sex, style color size  influencer price, quantity

happy, womens, scoop neck, rose, Medium, Annie Rose, $25.00, 30

Be kind, womens, pocket tee, white, small, Mere Carriker, $19.50, 0

Vet Ranch, unisex, hoodie, blue heather, medium, Vet Ranch, $43.99, 12





**is it in 1NF?**

#### another example











