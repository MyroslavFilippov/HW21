**In this Homework we used master-slave replication functionality in MySQL with master and 2 slaves.**

To run it, please follow these steps:

1. Run `docker compose up` to set up mysql containers.
2. Run `bash setup_db.sh` to set up all needed settings for replication.
3. Run these scripts to make sure that replication is working correctly.

`docker exec   database_master sh -c "mysql -u root -pS3cret my_db -e 'create table code(code int); insert into code values (100), (200)'"
docker exec database_slave sh -c "mysql -u root -pS3cret my_db -e 'select * from code \G'"
docker exec database_slave_2 sh -c "mysql -u root -pS3cret my_db -e 'select * from code \G'"`


**Point "Try to remove a column in database on slave node (try to delete last column and column 
from the middle)":**

To demonstrate what's happening there, let's check this example and steps below: 

1. To create a table **code** with these metadata and data we can use command below:

**column names**: code,val,id

**values:** 100, 200, 1

`docker exec   database_master sh -c "mysql -u root -pS3cret my_db -e 'create table code2(code int, val int, id int); insert into code2 values (100, 200, 1)'"
`
2. To delete in slave_2 server(replica) last or middle columns we can use one of the commands:

`docker exec   database_slave_2 sh -c "mysql -u root -pS3cret my_db -e 'ALTER TABLE code DROP COLUMN id'"
docker exec   database_slave_2 sh -c "mysql -u root -pS3cret my_db -e 'ALTER TABLE code DROP COLUMN val'"
`

3. To insert a new row with data we can use script:

`docker exec   database_master sh -c "mysql -u root -pS3cret my_db -e 'insert into code values (200,300,2)'"
`
4. In case 1 (deleted last column) we got: (200, 300). So code and val column values were 
replicated correctly. But column id didn't replicate again.
In case 2 (deleted middle column) we got: (200, 300). So code column was replicated correctly, 
id - no. Instead of it we got val column's value.

Both cases showed to us that ddl replication didn't happen.