# Dockerize two spring boot microservices


Two spring boot microservices running in a container are communicating using docker network


Created two java spring boot application
1. spring-boot-app-1
2. spring-boot-app-2

spring-boot-app-1 app expose API `/student/app1/{id}`

spring-boot-app-2 app expose API `/app2/{id}`

`/student/app1/{id}` API from app-1 internally calls to `/app2/{id}` of app-2

## Steps to run the application

#### Step-1: Run the applications
      cd /spring-boot-app-1
      mvnn clean install

      cd /spring-boot-app-2
      mvnn clean install

#### Step-2: Docker build images
      cd /spring-boot-app-1
      docker build . -t spring-api-app1

      cd /spring-boot-app-2
      docker build . -t spring-api-app2


#### Step-3: Docker network create
docker network is creating to communicate between two container. As we are run these two application in seperate container, network is required to communicate.

      
      docker network create spring-app-network


#### Step-4: Run application in docker container

     docker run -d -p 8081:8081 --name spring-api-app1 --network spring-api-network spring-api-app1
     docker run -d -p 8082:8082 --name spring-api-app2 --network spring-api-network spring-api-app2

#### Step-5: Test the application

     CURL http://localhost:8081/student/app-1/1

     Response:
     {"id":1,"name":"Jenny"}% 



# Dockerized using docker-compose

#### docker-compose.yml
     
        version: '3'

        services:
        spring-api-app1:
            container_name: spring-api-app1
            build: 
            context: ./spring-boot-app-1/.
            ports:
            - 8081:8081
            networks:
            - spring-app-network
            depends_on:
            - spring-api-app2
        spring-api-app2:
            container_name: spring-api-app2
            build: 
            context: ./spring-boot-app-2/.
            ports:
            - 8082:8082
            networks:
            - spring-app-network

        networks:
        spring-app-network:
            name: spring-app-network

#### docker-compose build

     docker-compose up --build

  #### Test the application

     CURL http://localhost:8081/student/app-1/1

     Response:
     {"id":1,"name":"Jenny"}% 
   