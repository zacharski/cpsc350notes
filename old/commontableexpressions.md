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

## SQL Injection

So we have a list that looks like:

	[ 'long walks in woods',  'classical music', 'romantic candlelight dinners', 'puppies' ]
	
and we want to convert that to a series of insert statements:

    	((select id from inserted), 'long walks in woods' ), 
       	((select id from inserted), 'classical music'),
       	((select id from inserted), 'romantic candlelight dinners');
       	((select id from inserted), 'puppies');
and we might be tempted to use a map to accomplish this:


	let u = interest_list.map(function( inte) {
    	return ('((select id from inserted), \'' + inte  + '\')')
	    }).join();
	
unfortunately, this leads us susceptible to SQL injection. 

A correct approach would be something like this:



	  app.post('/addclient', async (req, res) => {
	    console.log(req.body);
	      
	      let name = req.body.name;
	      let city = req.body.city;
	      let state = req.body.state;
	      let interests = req.body.interests;
		  // First let's generate all the inserts into the interests table
		  let interest_list = interests.split(/\,[\s]+/)
		  let i = 4;
		  let interest_inserts = interest_list.map(function( inte) {
		     return ('((select id from inserted), $' + i++  + ')')
		  }).join();
	      // now let's combine that with the main insert:

		  let query = 'WITH inserted AS ( INSERT INTO clients (name, city, state)\n';
		  query +=    '  VALUES ($1, $2, $3) RETURNING id )\n';
	      query +=    'INSERT INTO interests VALUES ' + interest_inserts
	      // now that we have a complete query string, let's execute it:
	      try {
	        const response = await pool.query(query, [name, city, state].concat(interest_list));
	        res.json({status: 'client added'});
	            }
	      catch(err){
	        console.error("Error running query " + err);
	           }
	  });

	
Here we are missing some error checking but the function works!


# Another use for Common Expression Tables.
Suppose we have a table called `people`:

	core=# select * from people;
	 id | firstname | lastname |    hometown     |     status      
	----+-----------+----------+-----------------+-----------------
	  1 | Ann       | Mulkern  | Fargo, ND       | on vacation
	  3 | Ben       | Mansk    | Mesilla, NM     | on site working
	  4 | Clara     | Wieck    | Albuquerque, NM | on vacation
	  5 | Fiona     | Helborn  | Albuquerque, NM | on vacation
	  6 | Rachel    | Young    | Austin, TX      | working
	  7 | Dan       | Vanner   | Austin, TX      | working
	  8 | Liz       | Phillips | Austin, TX      | traveling to job site
	(7 rows)

and a table called `friends` indicating who is friends with whom


	core=# select * from friends;
	 person | friends_with 
	--------+--------------
	      1 |            4
	      1 |            5
	      1 |            6
	      3 |            7
	      4 |            5
	      4 |            1
	      5 |            1
	      1 |            8
	      4 |            8
	      5 |            8
	(10 rows)

and suppose we want to retrieve the information about people ordered by the number of friends they have.

To get a person's friend count we can do a simple select statement:


	core=# SELECT person, COUNT(friends_with) FROM friends GROUP BY person;
	 person | count 
	--------+-------
	      4 |     3
	      1 |     4
	      5 |     2
	      3 |     1
	(4 rows)
Now we are going to use that select in a WITH query to get our results:


	with friend_count AS (
	   SELECT person, COUNT(friends_with) FROM friends GROUP BY person
		)
	select people.firstname, people.lastname, people.hometown, friend_count.count friends 
	    from friend_count join people on people.id = friend_count.person order by friends desc;

which gives us the result we wanted:

      result:
 	 firstname | lastname |    hometown     | friends 
 	-----------+----------+-----------------+---------
	 Ann       | Mulkern  | Fargo, ND       |       4
	 Clara     | Wieck    | Albuquerque, NM |       3
	 Fiona     | Helborn  | Albuquerque, NM |       2
	 Ben       | Mansk    | Mesilla, NM     |       1
	 (4 rows)


So Ann has the most friends (4) and is listed first.