# Week 1 — App Containerization

## Docker Containerization

###

#### Set Up nootifications for the Cruddur App and added a sample notification
![Notifications](assets/Notifications.png)

### Containerized both the backend and frontend images 
Through docker compose yml file ran and made sure the app ran

```
docker compose up
or
docker compose-up
```
![Photos](assets/dockerImages.png)
![Photos](assets/dockerContainerID.png)

### SET up DynamoDB and Postgress

Set up the Dynamodb and postgress database and made sure it worked

![Photos](assets/dynamoDB.png)

![Photos](assets/postgress.png)

![Photos](assets/postgresWorking.png)

## DockerHUb

#### Ran the docker push command and uploaded the docker images to docker hub

Pushed the docker image at dockerhub

back-end docker hub images 
https://hub.docker.com/repository/docker/lewisawe/backend/general
![Photos](assets/backend.png)

front-end docker hub images 
https://hub.docker.com/repository/docker/lewisawe/cruddur-app/general
![Photos](assets/dockerHubfrontend.png)

In My local machine I pulled the images and did the Docker Run for both apps

```
docker pull lewisawe/cruddur-app:latest
docker pull lewisawe/backend:latest
docker run -p 3000:3000 lewisawe/cruddur-app:latest
docker run -p 4567:4567 lewisawe/backend:latest
```
Managed to make the frontend work, trying to make backend communicate with the frontend in order to show the data

![Photos](assets/dockerrun.png)
