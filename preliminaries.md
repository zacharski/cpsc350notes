# preliminaries

## installing node v8

	curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
	sudo apt-get install -y nodejs

# promises - async - stuff like that.

Let's look at some code

	setTimeout(function() { console.log("Goodbye!"); }, 2000); 

explain that. then...


	// Say "Hello." 
	console.log("Hello."); 
	// Say "Goodbye" two seconds from now. 
	setTimeout(function() { console.log("Goodbye!"); }, 2000); 
	// Say "Hello again!" 
	console.log("Hello again!");
	
before running it -- what do you think will happen?

run it.  

## synchronous code
In synchronous programs, if you have two lines of code (L1 followed by L2), then L2 cannot begin running until L1 has finished executing.



## Asynchronous code

In asynchronous programs, you can have two lines of code (L1 followed by L2), where L1 schedules some task to be run in the future, but L2 runs before that task completes.

### why?
* web server - I am on my laptop in New Mexico and my server is in California. 
* retrieving from db

## Promises
Promises are objects that represent values that will exist in the future.

Like the restaurant.

Let's take a look at this code: (and I am ignoring most of the code and explaining just 2 lines)



	var Pool = require('pg').Pool;
	var config = {
	  host: 'localhost',
	  user: 'todomanager',
	  password: 'xyz123',
	  database: 'workshop',
	};


	var pool = new Pool(config);
	var response = "demo"
	response = pool.query("select * from enrolled where workshop = 'React Fundamentals'");
	console.log(response);


### detour
http://random-ize.com/how-long-to-hack-pass/

when we run this:

	Lungta:serverDemo raz$ node db1.js 
	Promise { <pending> }
	
you can see (picture) with web app code where we do need to use asynchronous code.   just like a waiter moving from table to table. but eventually when the browser does get the info it needs to know what code to execute.

(back) so instead of this design pattern we need to specify 'hey execute this code when we get a value' 

## one method - covered in the React video course 
use .then
It's good to know but it is covered adequately in that course so I will tell you another method which is a bit simpler.

	var Pool = require('pg').Pool;
	var config = {
	  host: 'localhost',
	  user: 'todomanager',
	  password: 'xyz123',
	  database: 'workshop',
	};


	var pool = new Pool(config);

	async function  get_enrollees(){ 
		response = await pool.query("select * from enrolled where workshop = 'React Fundamentals'");
		console.log(response.rows);
	}


	get_enrollees();

That's the basics on this topic. To learn more feel free to Google.

# PostgreSQL quickstart



# basics of using Postgres
	mkdir ../posty
	cd ../posty
	npm init
	npm install --save pg
	

# using postgres



### explain connection pools
takes awhile to make a connection
Web-based and enterprise applications use an application server to handle connection pooling. Dynamic web pages without connection pooling open connections to database services as required and close them when the page is done servicing a particular request. Pages that use connection pooling, on the other hand, maintain open connections in a pool. When the page requires access to the database, it simply uses an existing connection from the pool, and establishes a new connection only if no pooled connections are available. This reduces the overhead associated with connecting to the database to service individual requests.
**http://www.madhur.co.in/blog/2016/09/05/nodejs-connection-pooling.html**
#### one.js

try catch

#### two.js

### imagine user input.

## Don't use concatenation
## hashing passwords

	npm install --save bcrypt
	

 The second argument is the number of rounds to use when generating a salt.