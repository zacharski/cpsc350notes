# Querying an SQL database

## before we start let's load in a database file into our postgres server



show the start of the file   -- tomatoes and world



```
psql -U postgres -h localhost
\i world.sql
```



### NOW RETURN TO SLIDES



use \d to see the tables and \d actors to see the structure of the table

already saw the select statement

```
SELECT * from actors;
```



## returning rows that match a certain condition

let's say we are interested in finding out all the actors in the movie Iron Man 2

# Basic Query Patterns

Examine tomatoes



1. Interested  in the movie Iron Man 2
2. We are interested in movies released in 2010
3. cities over 8 million people

The comparison operators are

- =
- \<
- \>
- \<=
- \>=
- \<\>

## RETURN TO THE SLIDE

and explain the columns feature



1. The title of movies over 2 hrs long   
2. UNIQUE introduce distinct    (SLIDES)
3. all the movies released in 2010
4. The firstname, lastname, and roles for actors in iron man 2
5. The first and last names of the actor that played the role of Ancient One
6. The city name and population of cities over 8 million people



## THE WHERE CLAUSE IS OPTIONAL

1. The names of the cities in the database.
2.  names and populations of cities in the database

# AND OR -- show slide

1. The role and the movie of all the movies Brie Larson was in
2. All the movies that are under 2 hours and have a tomato rating greater than 90
3. who (firstname, lastname)  of the actor that played Hippolyta in Wonder Woman 
4. Hippolyta in the 2017 movie
5. actors (firstname, lastname) who played either the role Ancient One or Supreme Intelligence
6. All the cities and state names, of cities in colorado and new mexico. 
7. cities in Norway (NOR) or Sweden (SWE) whose population is over 200,000

## LIKE



let's move on to another pattern  -- suppose I am in cities and I want all the city names that start with the word New.  We do this using like

### show slides

1. all cities that start with New (city, state)
2. end with the word city
3. useful if we have a dynamic search bar on a web site -- a person types in p and we see words that start with N



### ILIKE 



1. actors whose firstname has a z in it
2. any movie with the word avenger in the title

# FINALLY NULL

SHOW SLIDES

1. I am interested in movies that haven't been released (year)
2. I am interested in movies that don't have a tomato rating
3. I am interested in movies with Gal Gadot that have been released (date not null)



## SLIDE - FINALE

This is what we covered. It is a lot of information



The only way to learn it is to practice.  You yourself can invent queries but here are a few to get you started. Good luck!



1.  What movies was Anne Hathaway in?

2. What year was the movie Hackers released?

3. Who (actor's name and role) was in the 2016 movie 'Doctor Strange'?

4. What movies were either Patrick Stewart or Kristen Stewart in?

5. what about for Samuel Jackson or Scarlet Johansson?

6. What are the cities and their populations of cities in the US with over one million population?

7. What are all the districts in the US?

   

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



