# Line Beacon App with Elasticsearch, Kibana in Docker

This project has created based on Docker for Linux Host. In this case, we use Linux VM on Azure cloud with Docker containers as a production environment. Line Beacon project have to be accessed with port 443 because LINE webhook and LIFF require HTTPS API to be attached, so finally this would create 5 containers
- node : use for Line Beacon App
- elasticsearch : use as storage for kibana
- kibana : use as dashboard to make a report
- nginx : use as a proxy to make Line Beacon App could be accessed with HTTPS
- certbot : use for generating certificate for proxy

## Prerequisites
- Ubuntu 18.04 VM
  - 16 vcpus
  - 64 GiB memory
  - 32 data disks
  - 25600 max IOPs
  - 128 temp storage
  - Allow port 80, 443, 3000, 5601
- Installed Docker
- Installed NodeJS
- Installed NPM
- Built Line Beacon Project
  - https://github.com/Jahja-wn/beacon-dev

## Installation
- Clone this project to host
  ```
  ~ > git clone https://github.com/Jahja-wn/beacon_docker.git
  ```
- Create Docker containers with pre configurations
  ```
  ~ > cd beacon_docker
  ~/beacon_docker > mv docker-compose.yaml docker-compose-prod.yaml
  ~/beacon_docker > mv ./nginx/nginx.yaml ./nginx/nginx-prod.yaml
  ~/beacon_docker > mv docker-compose-pre.yaml docker-compose.yaml
  ~/beacon_docker > mv ./nginx/nginx-pre.yaml ./nginx/nginx.yaml
  ~/beacon_docker > docker-compose up -d
  ```
- Generate OpenSSL in VM
  ```
  ~/beacon_docker > mkdir dhparam
  ~/beacon_docker > sudo openssl dhparam -out ./dhparam/dhparam-2048.pem 2048
  ```
- Recreate Nginx container again with prod configuration 
  
  ```
  ~/beacon_docker > mv docker-compose.yaml docker-compose-pre.yaml
  ~/beacon_docker > mv ./nginx/nginx.yaml ./nginx/nginx-pre.yaml
  ~/beacon_docker > mv docker-compose-prod.yaml docker-compose.yaml
  ~/beacon_docker > mv ./nginx/nginx-prod.yaml ./nginx/nginx.yaml
  ~/beacon_docker > docker-compose up -d --force-recreate --no-deps nginx
  ```
  Your VM should be accessed by HTTPS after this step.

## Deployment
- Clone Line Beacon Project to VM and change folder name to ```beacon```
  ```
  ~ > git clone https://github.com/Jahja-wn/beacon-dev.git
  ~ > mv beacon-dev beacon
  ```
- Fetch to check newer version of code
  ```
  ~/beacon > cd beacon
  ~/beacon > git fetch
  ```
- Pull the newest version of code
  ```
  ~/beacon > git pull origin master
  ```
- Build project
  ```
  ~/beacon > npm run build
  ```
- Recreate Node container
  ```
  ~/beacon > cd ../beacon_docker
  ~/beacon_docker > docker-compose up -d --force-recreate --no-deps node
  ```