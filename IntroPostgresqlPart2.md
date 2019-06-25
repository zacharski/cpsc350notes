# Intro to Postgresql 

# Part 2 - Enhancements

Make sure to keep notes. 

## Handling I Don't Know.



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



In our books table we had an id number. Numbers used as identifiers are everywhere:

* Amazon order numbers
* FedEx tracking numbers
* Student ID number
* Drivers license
* ISBN number for books
* everywhere

So far we've been keeping track of them in our head. If we are incrementing an id number we have to remember the id number of the last thing we entered. 





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

let's say we have a library patron table.



id, firstname, lastname, address, city, state



over 90% of the time the city is Balsam Lake and state is WI









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











