# Week 6 — Deploying Containers

AWS Containers is a suite of container management services provided by AWS to help developers deploy and manage containerized applications. Containers are a type of lightweight virtualization technology that allows developers to package their applications and their dependencies in a portable and consistent way.

AWS Containers includes, Amazon Elastic Container Service (ECS), Amazon Elastic Kubernetes Service (EKS), and AWS Fargate. These services provide a range of options for deploying, scaling, and managing containers on AWS.

Amazon Elastic Container Service (ECS) is a fully-managed container orchestration service that supports Docker containers. It enables developers to easily deploy and manage applications in a highly scalable and cost-effective way. ECS can be used to run containerized applications on a cluster of EC2 instances or on AWS Fargate, a serverless compute engine for containers.

Amazon Elastic Kubernetes Service (EKS) is a fully-managed Kubernetes service that makes it easy to deploy, manage, and scale containerized applications using Kubernetes. EKS enables developers to focus on writing applications rather than managing the underlying infrastructure.

AWS Fargate is a serverless compute engine for containers that allows developers to run containers without managing servers or clusters. With Fargate, developers can focus on building and deploying their applications without worrying about the underlying infrastructure.


## IMPLEMENTATION

create a script to test RDS Connection named test
located bin/db/
and chmod u+x 
```
#!/usr/bin/env python3

import psycopg
import os
import sys

connection_url = os.getenv("CONNECTION_URL")

conn = None
try:
  print('attempting connection')
  conn = psycopg.connect(connection_url)
  print("Connection successful!")
except psycopg.Error as e:
  print("Unable to connect to the database:", e)
finally:
  conn.close()
  
```

Add a healch check in the backend app.py

```
@app.route('/api/health-check')
def health_check():
  return {'success': True}, 200
  
```
Create a new folder
backend-flask/bin/flask
a script named 'health-check'
and chmod u+x 
```
#!/usr/bin/env python3

import urllib.request

try:
  response = urllib.request.urlopen('http://localhost:4567/api/health-check')
  if response.getcode() == 200:
    print("[OK] Flask server is running")
    exit(0) # success
  else:
    print("[BAD] Flask server is not running")
    exit(1) # false
# This for some reason is not capturing the error....
#except ConnectionRefusedError as e:
# so we'll just catch on all even though this is a bad practice
except Exception as e:
  print(e)
  exit(1) # false
  
```
