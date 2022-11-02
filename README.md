# devops_docker

This repository gathers all my docker works done during my DevOps training at EazyTraining
3 projects done:
# 1. how to deploy a static website using docker

![enter image description here](https://raw.githubusercontent.com/hnkoy/devops_docker/master/webapp_infra.jpg)
First, create a Dockerfile and in the file :
- install  Nginx
- install git
- and clone the project on GitHub 
 # Dockerfile
 ```
** start image from ubuntu os image **
 FROM ubuntu
 
 ** specify the image author **
MAINTAINER henock (henocknkoy9@gmail.com)

** update os before install **
RUN apt-get update

** disable interactivity and install git and nginx **
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y nginx git

** remove the default page nginx **
RUN rm -Rf /var/www/html/*

** clone project from GitHub in /var/www/html**
RUN git clone https://github.com/diranetafen/static-website-example.git /var/www/html/

** expose the app on port 80 **
EXPOSE 80
** Start Nginx server **
CMD [“nginx”, “-g”, “daemon off;”]
 ```      
# Build image
make sure you are in the Dockerfile directory and run this command to build and tag your image
```   
docker build -t webapp:v1 .
 ```
# Run container
Tape this command to run your container
```
docker run -d --name webapp -p 80:80
```
```
-d :run the container on background |--name : name your containe|-p : specify the listen port
```
# 2. How to deploy odoo using ifrastructure as code with docker compose

![enter image description here](https://raw.githubusercontent.com/hnkoy/devops_docker/master/odoo_iac_infra.jpg)
First, create a docker-compose.yml file and in the file :
create 2 services :
- service "web" for odoo:14.0 frontend
- service "DB" for Postgres

create a Network :
- odoo_network

create 2 volumes for our 2 containers
- odoo-web-data
- odoo-db-data

# Docker-compose file
 ```
 version: '3.1'
 
 ** start creating my 2 services web and DB **
 
services:
** start service web **

web:
** Get the official odoo image ***
image: odoo:14.0
**                              
** service web will start after service db
depends_on:
- db
**    
** expose the service on an external port **
ports:
- "8069:8069"
**                                

** mount data in the volume odoo-web-data
volumes:
- odoo-web-data:/var/lib/odoo
- ./config:/etc/odoo
- ./addons:/mnt/extra-addons
**                                             

** specify the service network
networks:
- odoo_network
**                                                 

** start service db

db:
** Get the official postgres image ***
image: postgres:13
**                                       
** specify the environment variables
environment:
- POSTGRES_DB=postgres
- POSTGRES_PASSWORD=odoo
- POSTGRES_USER=odoo
- PGDATA=/var/lib/postgresql/data/pgdata
**                                                   

** mount data in the volume odoo-db-data
volumes:
- odoo-db-data:/var/lib/postgresql/data/pgdata
**                                                    

** specify the service network
networks:
- odoo_network
**                                      

** declare the 2 volumes
volumes:
odoo-web-data:
odoo-db-data:
**                                

** declare the network
networks:
odoo_network:
**                              
  ```
make sure you are in the Docker-compose directory and run this command to run and the containers
```
docker-compose up -d
```
and run this command to check your running containers
```
docker ps
```

# Mini project

This project is a simple application to list students with a webserver (PHP) and API (Flask)
the steps are to:
- Build a docker image for the Flask application using a docker file
the first module is a REST API (with basic authentication needed) that send the desired student list based on a JSON file
The second module is a web app written in HTML + PHP that enables end-user to get the student list
- Deploy 2 services using IAC with Docker-compose 
the first service will expose the frontend app launching an apache container using php: apache image
the second service will expose the API to the front using the API Flask image built 
- Deploy a private registry and store the built images
So we need to deploy :

-   a docker  [registry](https://docs.docker.com/registry/ "registry")
-   a web  [interface](https://hub.docker.com/r/joxit/docker-registry-ui/ "interface")  to see the pushed image as a container

![enter image description here](https://raw.githubusercontent.com/hnkoy/devops_docker/master/mini_pro_infra.jpg)

# Dockerfile

```
** based image 
FROM python:2.7-stretch
** 
** Specify the image maintainer                
LABEL maintener.email=henocknkoy9@gmail.com maintener.name=henock
**
** copy the python script
COPY student_age.py /
**
** install flask and some python libraries 
RUN apt-get update -y && \
apt-get install python-dev python3-dev libsasl2-dev python-dev libldap2-dev libssl-dev -y && \
pip install flask==1.1.2 flask_httpauth==4.1.0 flask_simpleldap python-dotenv==0.14.0
**
** specify a volume directory
VOLUME /data
**
** internal listen port 
EXPOSE 5000
**
** run the python script
CMD [ "python", "./student_age.py" ]
**
```
# Build image
make sure you are in the Dockerfile directory and run this command to build and tag your image
```   
docker build -t student-list:v1 .
 ```

# Docker-compose

 ```
 version: '3.4'
** declare 2 services
services:
************** start service backend
 backend:
** specify the container name
  container_name: backend-api
**
** mount a volume
  volumes:
   -'$PWD/simple_api/student_age.json:/data/student_age.json'
**
** specify the image
  image: 'student-list:v1'
**
** specify the service network
  networks:
   - student
********************** end service backend

*************** start service frontend
 frontend:
** declare 2 environment variables  
  environment:
    - 'USERNAME=toto'
    - 'PASSWORD=python'
**
** specify the container name
  container_name: front-web
**
** mount a volume
  volumes:
    - './website:/var/www/html'
**
** specify the image
  image: 'php:apache'
**
** this service will start after backend service
  depends_on:
    - backend
**
** ** expose the service on an external port **
  ports:
    - '8000:80'
**
** specify the service network
  networks:
    - student
********************** end service frontend
** declare the network used
networks:
  student:
  ```

make sure you are in the Docker-compose directory and run this command to run and the containers
```
docker-compose up -d
```
and run this command to check your running containers
```
docker ps
```
# Private Registry
Start the registry and run this command

```
docker run -d -p 5000:5000 --name registry registry:2
```
Tag the image so that it points to the registry
```
docker image tag student-list:v1 localhost:5000/student-list:v1
```
Push the image to the registry
```
docker push localhost:5000/student-list:v1
```
# Docker Registry UI
 It's a user interface for  private docker registry. 
 Pull the image into your local machine using this command
 ```
 docker pull joxit/docker-registry-ui:static
 ```
and run it as a container using this command
```
docker run -d -p 8080:80 --name registry-ui -e REGISTRY_URL="http://host_IP:5000" joxit/docker-registry-ui:static
```
