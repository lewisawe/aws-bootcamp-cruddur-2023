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


