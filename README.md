# devops_docker

This repository gathers all my docker works done during my Devops training at EazyTraining
you will found images:
# how to deploy static website using docker
this image will:
- install automatically ngnix
- install git
- and clone the project on github 

# How to deploy odoo using ifrastructure as code with docker compose
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
