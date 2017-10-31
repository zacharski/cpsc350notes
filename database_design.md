# Database Design Comments

## To be in 3NF, each table must have a primary key
This is one of the two requirements of 1NF:

* each row of the table must contain atomic values
* each row of the table must have a unique identifier known as the primary key.

It is not enough to *know* that a particular column is the primary key. You must denote it when you create the table:


	CREATE TABLE clients (
   	    id serial PRIMARY KEY,
   	    name text,
        city text,
        state text
    );

    CREATE TABLE interests (
        client_id integer REFERENCES clients (id),
        interest text,
        PRIMARY KEY (client_id, interest)
    );
    
A primary key can consist of one column as the `clients` table shows where the column `id` contains values that are unique to each row (no two rows share the same value for `id`).  A primary key  can also be a combination of columns and this is called a **composite key**. The table `interests` has a composite primary key consisting of both columns of the table.

## Foreign keys
The above example also shows how to use a foreign key (the `references` keyword in the `interests` table). This example specified that the value for the `interests client_id` column must match a value in the `id` column of the `clients` table. If you try to insert a row where that is not true, you get an error:

	core=# insert into interests values (7, 'long walks');
	ERROR:  insert or update on table "interests" violates foreign key constraint "interests_client_id_fkey"
	DETAIL:  Key (client_id)=(7) is not present in table "clients".
	
Foreign keys are used to enforce data integrity. One other plus is that it makes reading an SQL file easier because you can see the relationships between tables.

## Avoid duplicating data.

Suppose we want to store information about schools:

school | grade | district | city | state | rating
:---: |  :---: |  :---: |  :---: |  :---: |  :---: 
Doña Ana  | Elementary  School | LCPS | Las Cruces | NM | C+
Algonkian | Elementary School |LCPS | Loudoun  | VA | B-
Piñon | Elementary School| LAPS| Los Alamos |NM | B+
Piñon | Elementary School| SFPS| Santa Fe  |NM | B+	

Here no single column is a unique identifier. Schools can have the same name. Different districts can have the same abbreviations and there can be multiple school districts in a city. Suppose we determine that the combination school, district, city, and state make each row unique and we can use them as a composite primary key.

Now imagine another table in the database which contains information about students including what school they are attending. If we went with the composite key we mentioned above for the school table, each row in the student table would also have columns for `school`, `district`, and `'city`.


 

firstname | lastname | dob |  ... | school | district | city 
:---: |  :---: | :---: |  :---: |  :---: |  :---: |  :---: |  :---: 
Mary | Harris | 2008-06-11 | .. |Doña Ana   | LCPS | Las Cruces
Andy | Quinn | 2008-03-07 | .. |Doña Ana   | LCPS | Las Cruces
Jane | Grider | 2008-07-12 | .. |Doña Ana   | LCPS | Las Cruces
... | ...| ... | .. |Doña Ana   | LCPS | Las Cruces
Kathy | Ogden | 2008-07-03 | .. |Desert Hills  | LCPS | Las Cruces

That is a lot of repeated information. And if we change the name of a school -- say an alumna becomes famous -- we need to change multiple occurrences in multiple tables (the schools table, the students table, the teachers table, etc). There is a chance we might forget to change it somewhere and our data becomes inconsistent. Here is a good place to create a single column synthetic key for our initial school table:

id | school | grade | district | city | state | rating
:---: |:---: |  :---: |  :---: |  :---: |  :---: |  :---: 
1 | Doña Ana  | Elementary  School | LCPS | Las Cruces | NM | C+
2 | Algonkian | Elementary School |LCPS | Loudoun  | VA | B-
3 | Piñon | Elementary School| LAPS| Los Alamos |NM | B+
4 | Piñon | Elementary School| SFPS| Santa Fe  |NM | B+
Now, instead of three columns added columns to the student table, we just need one, the school id.


## Indexing and searching the database

When you specify a column as a primary key, the database server indexes that column, meaning it create a B-tree index on those values. When you search for a value in a primary key column the search is very fast. By default when you search for values in other columns, the search is a slow linear one. The good news is that we can instruct the database server to index any column we want:

	CREATE INDEX ON schools (school);
	
So this creates a B-tree index on the school column of the table schools.

The benefits are that it speeds up search. The negative is that these B-trees take up storage space. So we need to use a bit of common sense. Suppose we have 2 million users and we have a users table with `username` as the primary key. But we frequently search on a person's lastname so we would index the column `lastname`.  If we don't frequently search on the column `favorite_quote` we wouldn't index it.