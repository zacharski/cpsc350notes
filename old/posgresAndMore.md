# Introduction to PostgreSQL
How to install on a variety of platforms
## Installing on a Mac
We used [Postgres.app](http://postgresapp.com). The following instructions are from that site:

1. Download ➜   Move to Applications folder   ➜   Double Click
2. Click "Initialize" to create a new server
3. Configure your $PATH to use the included command line tools (optional):

	sudo mkdir -p /etc/paths.d &&
	echo /Applications/Postgres.app/Contents/Versions/latest/bin | sudo tee /etc/paths.d/postgresapp

## Installing on Linux on Google Cloud or Elsewhere

	sudo apt-get update
	sudo apt-get -y install postgresql postgresql-client postgresql-contrib
	sudo -u postgres psql postgres
	
then set password

## Only for Cloud9  - starting the server (no need to install)

          sudo service postgresql start
     
### creating a password

     sudo sudo -u postgres psql
     # setting the password
     \password postgres
     # then quit
     \q
     
### logging into the client with your new password

     psql -U postgres -h localhost
     
Or, let's say you want to login as user `swift` to the `pets` database

     psql -U swift -d pets -h localhost
     
# creating a simple database and table

	create database pets;
	\c pets
	create table dogs (breed text, name text);
	insert into dogs (breed, name) values ('lab', 'Anna');a
     
### useful commands

| command | description |
|:---:|:---|
|`\q` | exit the postgres client |
|`\l` or `\list` | list all the databases |
| `\c pets` | change to the pets database |
| `\d` or `\dt` | show a list of all the tables in the current database |
| `\d dogs` | describe the format of the table `dogs` |
| `\d+ dogs` | describe  *in more detail* the format of the table `dogs` |
| `\i charts.sql` | import sql commands from the file `charts.sql` |
| `DROP DATABASE charts` | delete the database `charts` |
| `SELECT * FROM topSongs` | show all the data in the `topSongs` table



# Video 6: PostgreSQL and the Principle of Least Privilege

### Principle of Least Privilege
A user (or process) should have the lowest level of privilege required in order to perform his/her assigned task.

Microsoft says:

> running in standard user mode gives customers increased protection against inadvertent system-level damage caused by "shatter attacks" and malware such as root kits, spyware, and undetectable viruses.


Similar to:

* compartmentalization (need to know)
* encapsulation (restrict access to object's components)

which leads to better security and better stability


### True for database servers
1. Your Flask app needs to login to the database server.
2. If your Flask app uses the root account that means the root username and password are stored in your code.
3. If you screw up: 
    1. your code itself can destroy all the databases on the server.
    2.  a malicious attack on your code could compromise all the databases on the server and possibly compromise the server itself.
4.  The solution is to create an account with the least privilege required to do the task.

### Incentive
Any Flask PostgreSQL code that uses the root postgres account will get 0xp.

## creating user accounts
| command | description |
|:---:|:---|
| `CREATE ROLE petman;` | will create a user named `petman` |
| `CREATE ROLE swift;` with login | will create a user named `swift` with login privileges (probably what you want) |
| `DROP ROLE swift;` | delete the user named `swift` |
|`\du` | list all the users |
|  `ALTER ROLE petman with login;` | give login privileges to `petman` |
| `\password swift` | set a password for the user `swift` (you will be prompted) |
| `GRANT ALL ON topsongs TO SWIFT;` | grant all permissions to the table `topsongs` to the user `swift`  
| `GRANT SELECT ON topsongs TO petman;` | only grant `select` privileges to the `topsongs` table to `petman`. (For all options see [https://www.postgresql.org/docs/9.0/static/sql-grant.html](https://www.postgresql.org/docs/9.0/static/sql-grant.html)) |
| `REVOKE ALL ON topsongs FROM petman;` | revoke all permissions on the table `topsongs` from user `petman` |
| `\z` | show what permissions users have on the current database |






creating a user account (or role) 

     CREATE ROLE <username>;
     #for example
     CREATE ROLE swift;  	
     
