# Week 4 — Postgres and RDS

RDS stands for Amazon Relational Database Service, which is a managed database service provided by Amazon Web Services (AWS). It allows users to easily create, operate, and scale relational databases in the cloud, without having to manage the underlying infrastructure. RDS supports several database engines, including PostgreSQL, MySQL, MariaDB, Oracle, and Microsoft SQL Server.

PostgreSQL, often referred to simply as "Postgres," is an open-source relational database management system (RDBMS). It is known for its reliability, scalability, and advanced features such as support for JSON, XML, and spatial data. Postgres is widely used in web applications, data analytics, and geospatial applications, among others. It is also supported by RDS, meaning that users can deploy and manage PostgreSQL databases on the RDS service.

## IMPLEMENTATION

Run the command in terminal

```
aws rds create-db-instance \
  --db-instance-identifier cruddur-db-instance \
  --db-instance-class db.t3.micro \
  --engine postgres \
  --engine-version  14.6 \
  --master-username root \
  --master-user-password \
  --allocated-storage 20 \
  --availability-zone  \
  --backup-retention-period 0 \
  --port 5432 \
  --no-multi-az \
  --db-name cruddur \
  --storage-type gp2 \
  --publicly-accessible \
  --storage-encrypted \
  --enable-performance-insights \
  --performance-insights-retention-period 7 \
  --no-deletion-protection
```
Comment out dynamodb local in docker-compose

Docker compose up

Stop the RDS Instance in the console menu

at terminal in the postgress tab

```
psql -Upostgress --host localhost

```
 common commands
 
```
\x on -- expanded display when looking at data
\q -- Quit PSQL
\l -- List all databases
\c database_name -- Connect to a specific database
\dt -- List all tables in the current database
\d table_name -- Describe a specific table
\du -- List all users and their roles
\dn -- List all schemas in the current database
CREATE DATABASE database_name; -- Create a new database
DROP DATABASE database_name; -- Delete a database
CREATE TABLE table_name (column1 datatype1, column2 datatype2, ...); -- Create a new table
DROP TABLE table_name; -- Delete a table
SELECT column1, column2, ... FROM table_name WHERE condition; -- Select data from a table
INSERT INTO table_name (column1, column2, ...) VALUES (value1, value2, ...); -- Insert data into a table
UPDATE table_name SET column1 = value1, column2 = value2, ... WHERE condition; -- Update data in a table
DELETE FROM table_name WHERE condition; -- Delete data from a table

```

Create a new folder in backend folder named 'db' inside the folder create a file name 'schema.sql'
Add the following

```
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
```

quit postgress

```
\q
```

in the backend directory of cruddur cli

```
psql cruddur < db/schema.sql -h localhost -U postgres
```
enter password

To persist postgress password and username

```
export CONNECTION_URL="postgresql://postgres:Pass@localhost:5432/cruddur"
gp env CONNECTION_URL="postgresql://postgres:Pass@localhost:5432/cruddur"
```
Also try to connect with

```
psql $CONNECTION_URL
```

Prod Connection URL and from the console copy the RDS DB endpoint and insert

```
export PROD_CONNECTION_URL="postgresql://cruddurroot:Pass@endpoint:5432/cruddur"
gp env PROD_CONNECTION_URL="postgresql://cruddurroot:Pass@endpoint:5432/cruddur"
```

Mkdir in backend a folder named bin and 3 new files
- db-create
- db-drop
- db-schema-load

in all the files add in the top line 

```
#! /usr/bin/bash
```

in 'db-drop'
```
psql $NO_DB_CONNECTION_URL -c "DROP database cruddur;"
```

change permissions of the files using
```
chmod u+x bin/db-create
chmod u+x bin/db-drop
chmod u+x bin/db-schema-load
```

run db-create
```
./bin/db-drop
```

edit 'db-drop'

```
echo "db-drop"

NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTON_URL")
psql $NO_DB_CONNECTION_URL -c "drop database cruddur;"
```
 re-run in CLI
 
```
./bin/db-drop
```

in 'db-create'

```
echo "db-create
NO_DB_CONNECTION_URL=$(sed 's/\/cruddur//g' <<<"$CONNECTION_URL")
psql $NO_DB_CONNECTION_URL -c "create database cruddur;"
```

create a database

```
./bin/db-create
```

in 'db-schema-load

```
echo "db-schema-load"

schema_path="$(realpath .)/db/schema.sql"
echo $schema_path


psql $CONNECTION_URL cruddur < $schema_path
```
execute

```
./bin/db-schema-load
```

edit 'db-schema-load'

```

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

psql $URL cruddur < $schema_path
```

execute
```
./bin/db-schema-load
```

add color scheme to 'db-schema-load'

```
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-schema-load"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

```

execute the schema
```
./bin/db-schema-load
```

in the db folder schema.sql file

```
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
DROP TABLE IF EXISTS public.users;
DROP TABLE IF EXISTS public.activities;


CREATE TABLE public.users (
  uuid UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  display_name text NOT NULL,
  handle text NOT NULL,
  email text NOT NULL,
  cognito_user_id text NOT NULL,
  created_at TIMESTAMP default current_timestamp NOT NULL
);

CREATE TABLE public.activities (
  uuid UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
  user_uuid UUID NOT NULL,
  message text NOT NULL,
  replies_count integer DEFAULT 0,
  reposts_count integer DEFAULT 0,
  likes_count integer DEFAULT 0,
  reply_to_activity_uuid integer,
  expires_at TIMESTAMP,
  created_at TIMESTAMP default current_timestamp NOT NULL
);
```

execute db-schema-load

```
./bin/db-schema-load
```

create a new file in bin named db-connect
add the following
and chmod u+x ./bin/db-connect
```
#! /usr/bin/bash

psql $CONNECTION_URL
```

execute db-connect and you should be able to see 2 tables

```
./bin/db-schema-connect
\l
\dt
```

create a new file in bin named db-seed 
add the following
and chmod u+x ./bin/db-seed
```

CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-seed"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"

seed_path="$(realpath .)/db/seed.sql"
echo $seed_path

if [ "$1" = "prod" ]; then
  echo "Running in production mode"
  URL=$PROD_CONNECTION_URL
else
  URL=$CONNECTION_URL
fi

psql $URL cruddur < $seed_path
```

ensure the color scheme is in all the files make sure to change label 
```
CYAN='\033[1;36m'
NO_COLOR='\033[0m'
LABEL="db-seed"
printf "${CYAN}== ${LABEL}${NO_COLOR}\n"
```

create a new file in db named seed.sql
add the following

```
-- this file was manually created
INSERT INTO public.users (display_name, handle, cognito_user_id)
VALUES
  ('Andrew Brown', 'andrewbrown' ,'MOCK'),
  ('Andrew Bayko', 'bayko' ,'MOCK');

INSERT INTO public.activities (user_uuid, message, expires_at)
VALUES
  (
    (SELECT uuid from public.users WHERE users.handle = 'andrewbrown' LIMIT 1),
    'This was imported as seed data!',
    current_timestamp + interval '10 day'
  )

```

execute
```
./bin/db-schema-load
./bin/db-seed
```


![Photos](assets/psqlData.png)

![Photos](assets/psqlTables.png)

![Photos](assets/seed_data.png)

![Photos](assets/workingpsql.png)

![Photos](assets/lambdaCloudWatchLogs.png)

![Photos](assets/succesfulProdDBEntry.png)

![Photos](assets/week-4-done.png)
