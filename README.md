# lab08

## Lab8
 Dockerfile-client
```
FROM ubuntu:22.10

RUN apt update && apt install curl --no-install-recommends -y

ENV SERVER_URL http://google.com

ENTRYPOINT bash -c "while true ; do sleep 5 && curl -qq $SERVER_URL; done" 
```
==========================================================
 Dockerfile-server
```
FROM ubuntu:22.10

RUN apt update && apt install npm --no-install-recommends -y && \
    mkdir -p /opt/server

WORKDIR /opt/server

RUN npm init -y && \
    npm install json-server

COPY package.json /opt/server/package.json
COPY db.json /opt/server/db.json

EXPOSE 3000

POINT ["npm", "start"]
```
===========================================================
 db.json
```
{
  "users": [
    {
      "id": 1,
      "name": "David",
      "age": 30
    },
    {
      "name": "John",
      "id": 2,
      "age": 40
     }
   ]
}
```
========================================================
 package.json
```

{
  "name": "server",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "json-server": "^0.17.3"
  },
  "scripts": {
    "start": "json-server -H 0.0.0.0 -p 3000 db.json"
  }
}
```
=====================================================
docker-compose.yml
```
version: '3.9'

services:
  server:
    image: lab8:server
    container_name: server
    networks:
      - lab8-network
  client:
    image: lab8:client
    container_name: client
    networks:
      - lab8-network
    environment:
      - SERVER_URL=http://server:3000/users

networks:
  lab8-network:
    driver: "bridge"
    ipam:
      driver: default
      config:
        - subnet: 172.20.0.0/16
```
====================================================
Билд и запуск:
```
docker build -f Dockerfile-server -t lab8:server .
docker build -f Dockerfile-client -t lab8:client .
docer-compose up -d
docker logs client
docker logs server
docker-compose down
```
