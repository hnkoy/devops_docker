# devops_docker

This repository gathers all my docker works done during my DevOps training at EazyTraining
3 projects done:
# 1. how to deploy a static website using docker

![enter image description here](https://raw.githubusercontent.com/hnkoy/devops_docker/master/webapp_infra.jpg)
First create a Dockerfile and :
- install  ngnix
- install git
- and clone the project from GitHub repository
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
the docker compose file will:
- get the official image odoo:14.0
- get the official image postgress
- create a network
- create a volume
- and create containers

# Mini project

This project is a simple application to list student with a webserver (PHP) and API (Flask)
the steps is to:
- build a docker image for the Flask application using a dockerfile
the first module is a REST API (with basic authentication needed) who send the desire list of the student based on JSON file
The second module is a web app written in HTML + PHP who enable end-user to get a list of students
- deploy 2 services using IAC with Docker-compose 
the first service will expose the frontend app lauching a apache container using php:apache image
the second service will expose the API to the frontend using the API Flask image built 
