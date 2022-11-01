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
run docker-compose up
```
and run this command to ckeck your running containers
```
docker ps
```

# Mini project

This project is a simple application to list student with a webserver (PHP) and API (Flask)
the steps is to:
- build a docker image for the Flask application using a dockerfile
the first module is a REST API (with basic authentication needed) who send the desire list of the student based on JSON file
The second module is a web app written in HTML + PHP who enable end-user to get a list of students
- deploy 2 services using IAC with Docker-compose 
the first service will expose the frontend app lauching a apache container using php:apache image
the second service will expose the API to the frontend using the API Flask image built 
