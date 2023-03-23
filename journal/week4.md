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
  --master-user-password huEE33z2Qvl383 \
  --allocated-storage 20 \
  --availability-zone us-east-1a \
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
password: password

```
 common commands
 
```
\l

CREATE database cruddur;

\l

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
export CONNECTION_URL="postgresql://postgres:password@localhost:5432/cruddur"
gp env CONNECTION_URL="postgresql://postgres:password@localhost:5432/cruddur"
```
Also try to connect with

```
psql $CONNECTION_URL
```

Prod Connection URL and from the console copy the RDS DB endpoint and insert

```
export PROD_CONNECTION_URL="postgresql://cruddurroot:huEE33z2Qvl383@endpoint:5432/cruddur"
gp env PROD_CONNECTION_URL="postgresql://cruddurroot:huEE33z2Qvl383@endpoint:5432/cruddur"
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
psql $CONNECTION_URL -c "DROP database cruddur;"
```

change permissions of the files using
```
chmod u+x bin/db-create
chmod u+x bin/db-drop
chmod u+x bin/db-schema-load
```

run db-create

./bin/db-drop
