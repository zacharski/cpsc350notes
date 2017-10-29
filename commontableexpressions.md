# Common Table Expressions

Let's say we are software developers for an online dating service and we want to maintain the following information about users:


name | city | state | interests
:---: |  :---: |  :---: |  :---: 
Dan | Frostbite Falls | MN | long walks in the woods, classical music,  candlelight dinners
Ann | Mesilla | NM | hiking, microbreweries, dogs

And just to be a bit more concrete suppose users select those interests from a long survey webpage containing checkboxes.

We might represent the information with 2 SQL tables, clients, and interests:

	
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
    
This is a pretty good design but it presents a problem when we want to insert something. Maybe you have already encountered this in your code. Let's say we want to add Ann's information. First we need to add her to the `clients` table:

	INSERT INTO clients (name, city, state) VALUES ('Ann', 'Mesilla', 'NM');
	
Then we would need to query the database to find the id number for Ann:

	select id from clients where name = 'Ann' and city = 'Mesilla' and state = 'NM';
	
And once we get that `id` value we can use it in the inserts into the `interests` table:

	INSERT INTO interests VALUES 
	   (1, 'hiking'), 
	   (1, 'microbreweries'), 
	   (1, 'dogs');
	   
This works fine but is sort of a hassle. Fortunately, there is an easier way. We can bundle all those statements up into one larger query by using a `WITH` query which is commonly called a **Common Table Expression** A `WITH` query creates a temporary table that exists just for this one query.

I think it is easier to explain with a specific example, so let me show you how to add the information for Dan:


	WITH inserted AS (     
 	   INSERT INTO clients (name, city, state)
 	       VALUES ('Dan', 'Frostbite Falls', 'MN') RETURNING id
    ) 
    INSERT INTO interests values 
     	((select id from inserted), 'long walks in woods' ), 
       	((select id from inserted), 'classical music'),
       	((select id from inserted), 'candlelight dinners');


The `inserted` is just the name of the temporary table we create. We could have called it anything. Inside the `AS` clause we have our usual `INSERT` followed by the keyword `RETURNING` listing what columns to create in the new table. Here we specified the `id` from the `INSERT` but we could have specified any number of columns including all the columns `*`.

After the `AS` clause which is in parentheses, we have a statement that uses this temporary table. In this example you can see we used subqueries that references the temporary table we created.

This is way cool because our node code can do this with only one call to the postgresql server.

## split and regular expressions to the rescue.

Now suppose we have those interests lumped together as one argument in the body of a post request:

key. | value
:---: | :---: 
interests | 'long walks in woods,  classical music,      romantic candlelight dinners,            puppies'


And on the node side we would like to split that long string into an array of separate values. We can do this using the javascript split command which can take a regular expression as an argument:

	> interests = 'long walks in woods,  classical music,      romantic candlelight dinners,            puppies'
     'long walks in woods,  classical music,      romantic candlelight dinners,            puppies'
    >interest_list = interests.split(/\,[\s]+/)
    [ 'long walks in woods',
      'classical music',
      'romantic candlelight dinners',
      'puppies' ]
    > 


So we split the list on a comma followed by zero or more spaces.

  