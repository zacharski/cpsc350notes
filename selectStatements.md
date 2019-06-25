# Querying an SQL database

## before we start let's load in a database file into our postgres server



show the start of the file   -- tomatoes and world



```
psql -U postgres -h localhost
\i world.sql
```



use \d to see the tables and \d actors to see the structure of the table

already saw the select statement

```
SELECT * from actors;
```



## returning rows that match a certain condition

let's say we are interested in finding out all the actors in the movie Iron Man 2



#### we use the where clause

```
SELECT * from actors WHERE movie = 'Iron Man 2';
```

That is way cool. But the movie name and the year columns are sort of reduntant. Suppose we want only the first name, lastname, and role 



The asterisk meant all the roles. so 



```
SELECT * from actors
```

in English meant something like "display all the columns from actors"  if we only want some columns we list them



```
SELECT firstname, lastname, role FROM actors WHERE movie = 'Iron Man 2';


```

If we want the role to appear first followed by firstname followed by lastname we would do ...



And keep in mind that in postgres (not all database servers) everything is case insensitive

```
tomatoes=# sELect FirstName, LastName, Role from Actors wHERe MoViE = 'Iron Man 2';

```

### what is the first and last name of the actor who played Master Shifu?

pause video ...



```
select firstname, lastname FROM actors WHERE role = 'Master Shifu'; 
```

## switch to world database

do \d and \d city



suppose we want the city name and population of all cities with a population over 8 million people?



```
select name, population FROM city WHERE population > 8000000;
```

The comparison operators are

* =
* \<
* \>
* \<=
* \>=
* \<\>



### ERRORS WERE MADE

```
SELECT anme, population from city;
select name, population from cities;
select name, population from city where countrycode = USA;
mising semi-colon
```

# now AND and OR

suppose we want all American cities (name district and population) that are over one million people

suppose we want all cities in California (name and population)

suppose we want all cities (name, and population) of cities in Arizona and New Mexico (pause video)

#### Did you use the OR? 



## Let's do a hard one

cities in Norway (NOR) or Sweden (SWE) whose population is over 200,000

select city, countrycode, population from city where (countrycode = 'NOR' OR countrycode = 'SWE') AND population > 200000;

**here is one for you What role and movie title was Angelina Jolie in?**

#### back to world db

let's look at the structure of the country table. we have headofstate and name. We want the headofstate and country name where the head of state's name starts with Mohammad

Using %

whose name ends with II  -- the second



names that have a z in them

## How Insensitive ILIKE  searching for al  (and matching Al and al)

## NULL





# Congratulations -- you now mastered the white level and have advanced.



