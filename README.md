# A simple MERN stack application--Containerizing a MERN Stack App with Docker Compose

**Note** - Clone the github project and Start building on your own using below steps

1. Implementation of `Dockerfile` for `client` and `server`.
2. Run the containers using `Docker Compose`.

## Run it local without Docker

### Prerequisite

- Install `npm`

#### Start Server:

```
cd mern/server
npm install
npm start
```

#### Start Client

```
cd mern/client
npm install
npm run dev
```

Steps:
1.Create a network for the docker containers
    ``` docker network create mern ```

2.Build the Clinet
``` 
cd mern/frontend
vim Dockerfile
```
Dockerfile:
```
FROM node:18.9.1          
WORKDIR /app              
COPY package.json .        
RUN npm install            
EXPOSE 5173                
COPY . .                   
CMD ["npm", "run", "dev"]  
```
Copy this commands to Dockerfile

Build docker file--> ``` docker build -t mern-frontend . ```

Run the docker image in the network created --> ``` docker run --name=frontend --network=mern -d -p 5173:5173 mern-frontend ```

check the container ---> ``` docker ps ```
for logs --> ``` docker logs frontend ```

Verify the client id running --->``` Open your browser and type http://localhost:5173 ```

3. Run the Mongo db container
--> ``` docker run --network=demo --name mongodb -d -p 27017:27017 -v ~/opt/data:/data/db mongo:latest ```


4.  cd ../..

cd mern/backend 

vim Dockerfile
 

Dockerfile:
```
FROM node:18.9.1
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
EXPOSE 5050
CMD ["npm", "start"]
```
Copy this commands to docker file
Build docker file--> ``` docker build -t mern-backend . ```

Run the docker image in the network created --> ``` docker run --name=backend --network=mern -d -p 5050:5050 mern-backend ```

check the container ---> ``` docker ps ```
for logs --> ``` docker logs backend ```

Note: Please clear all the running containers only ""( Do not touch Dockerfiles )**
5. To create docker compose file redirect to root 
vim docker-compose.yml

docker-compose-yml  -->
```
services:
  backend:
    build: ./mern/backend
    ports:
      - "5050:5050" 
    networks:
      - mern_network
    environment:
      MONGO_URI: mongodb://mongo:27017/mydatabase  
    depends_on:
      - mongodb

  frontend:
    build: ./mern/frontend
    ports:
      - "5173:5173"  
    networks:
      - mern_network
    environment:
      REACT_APP_API_URL: http://backend:5050 

  mongodb:
    image: mongo:latest  
    ports:
      - "27017:27017"  
    networks:
      - mern_network
    volumes:
      - mongo-data:/data/db  

networks:
  mern_network:
    driver: bridge 

volumes:
  mongo-data:
    driver: local  # Persist MongoDB data locally
```
Copy this file into docker-compose 

To run compose -->``` docker compose up -d ```

Check the client it will be running .......

To stop compose --->``` docker compose down ```

CONGRATULATIONS....
<img width="1790" alt="Screenshot 2024-08-31 at 11 07 58 PM" src="https://github.com/user-attachments/assets/f414230b-8bd6-4393-b8de-6a10444a8dfd">
