# Fancy select

not really.

Just a few odds and ends.



## distinct

Suppose we are in our tomatoes db and we want to see a list of the movies in the actors table



```
select movie from actors;
```

and we get repeats.

We get the repeats because we have a row for each actor in each movie. Suppose we just want to see the unique movie titles. For that we can use the distinct keyword. it works like this



```
select distinct movie from actors;
```

**select distinct** removes duplicate rows from the results. Suppose we want a unique list of actors -- firstnames and lastnames. Without the distinct we get duplicate Angelina Jolie's and others. so we add **distinct**



```
select distinct firstname, lastname from actors;

We don't get distinct firstnames -- we get multiple Katies -- we get distinct rows. In this case distinct firstname, lastname combos.

So, if we have something like 

​```
select distinct firstname, lastname, birthyear, hometownCity, hometownState from actorlist;

we might have entries for

Jack Black 1969, Santa Monica, CA
Jack Black 1921 Chicago, IL
```

The distinct applies to the whole row

## order by



Let's say we want those in alphabetical order.  Here we use the **order by** key phrase.

```
select distinct movie from actors order by movie;
```



#### try with city



maybe we want the results ordered by some column in our database table. For example, maybe we want to order by population.  



```
select name, population from city order by population;
```

We want to see the largest population first

```
select name, population from city order by population desc;
```

and for completeness



``` 
select name, population from city order by population asc;
```

​	

#### back to actors

ordering first on last names and then by first -- so Ann Smith would come after Sara Adams and Ann Smith would come before Mary Smith

```
select  distinct firstname, lastname from actors order by lastname, firstname;
```



Suppose we want the top 10 cities in terms of population  -- here we can use `limit`





```
select name, population from city order by population desc limit 10;

```

or limit 5



### recap



* order by
* distinct
* limit

### Other useful stuff

Summary statistics



* AVG
* SUM
* COUNT
* MIN
* MAX
* others like correlation



How many US cities are in the db?



``` 
select count(*) from city where countrycode = 'USA';
```



What is the average population of US cities?



```
select avg(population) from city where countrycode = 'USA';
```

What is the total population of all the US cities?

```
select sum(population) from city where countrycode = 'USA';
```



78 million

What is the largest population of a US city?



```
SELECT max(population) from city where countrycode = 'USA';
```



### show min and max

## group by

suppose we are interested in knowing how many cities are in each state in the US?

Let's do this step by step



first, get all the cities in the US



```
select * from city where countrycode = 'USA';
```



Now we want

```
 select district, count(*) from city where countrycode = 'USA' group by district;

```

Then finally



```
 select district, count(*) from city where countrycode = 'USA' group by district order by count(*) desc;

```



## between, not,  and in

Let's say we want an alphabetical list of cities and their populations that are in Texas, Nevada, and Arizona.

```
select name, population from city  where district = 'Texas' or district = 'Arizona' or district = 'Nevada' order by name;
```



an alternative



```
select name, population from city where district in ('Arizona', 'Texas', 'Nevada') order by name;
```

suppose we want the cities in Texas whose populations are between 100 and 200,000

```
select name, population from city where district = 'Texas' and population < 200000 and population > 100000 order by name;
```

or we can use the `between` keyword

```
select name, population from city where district = 'Texas' and population between 100000 and 200000 order by name;
```

