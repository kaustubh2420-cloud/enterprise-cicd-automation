# Enterprise CI/CD Automation Platform

## Project Description
An automated CI/CD platform for deploying a
production-like web application.

## Technologies
- Git and GitHub
- Jenkins
- Docker
- Ansible
- AWS

## Application
Python Flask web application.

## Docker
Build image:

docker build -t enterprise-cicd-app:v1 .

Run container:

docker run -d -p 5001:5000 enterprise-cicd-app:v1

## Health Check
http://localhost:5001/health
