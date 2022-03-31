# Welcome to the RTE Docker Lab
Our goal is to make you feel comfortable enough to start using docker by yourself.

There is no need to install docker in your machine, you can use 

- [Play With Docker Public](https://labs.play-with-docker.com/), login and then click "START"


## We will first need to clone this repo
files needed for the lab. 

`git clone https://github.com/eccervantes/DockerLab.git`

`cd DockerLab`

## Run my first Container
`docker run hello-world`

## Run a Web-server container 
`docker run --name MyLab  -d -p80:80 nginx`

* Verify is running 

     - `docker ps`
     
     - Click on the link that have the "80" in Play With Docker
     
     - Go to [localhost:80](http://localhost:80) (if your are running the lab in your machine)
     
* Lets copy a file into the container to modify the output.

     `docker cp index.html MyLab:/usr/share/nginx/html/`
     
     `docker cp IBMlogo.png MyLab:/usr/share/nginx/html/`
     
* verify the changes on your browser

     reload your browser
     
* Stop a Container

     `docker stop MyLab`
     
* Re-start a Container

     `docker start MyLab`
     
## Create an image from a Dockerfile
`docker build -t rtelab .`

* Run a Web-server container 

     `docker run --name MyLab2  -d -p81:80 rtelab`

#  Challenge 
## 1

use the knowledge you have gotten so far:

* go to the Docker101 folder and unzip the app.zip file
* create an image with a custom tag using the Dockerfile in the "Docker101" folder
* spin the image in deatached mode and map it to the port 3000. 
* add a few new items to the list.

## 2

* In the ~/app/src/static/js/app.js file, update line 56 to use the new empty text
     * modify "No items yet!" for a personal custom message that will be displayed when the list is empty.

* recreate the image from the docker file
* sping the image and verify your changes

## 3

* create a dockerhub repository name 101-todo-app
* push the modified image that includes your custom message to be shared. 
     * make sure you login 
     * `docker login -u YOUR-USER-NAME`
     * tag your image properly to uploaded to your newly created repository
          *  `docker tag docker-101 YOUR-USER-NAME/101-todo-app`
     * push the image and share
          *  `docker push YOUR-USER-NAME/101-todo-app`
     * Run a Shared image example:
          * `docker run -dp 3000:3000 eccervantes/101-todo-app:latest`  
 
## Making Data persistent (volumes and bind mount)

### volumes

Lests create a volume

`docker volume create todo-db`

lets create a new instance and attached the volume to the path /etc/todos

`docker run -dp 3000:3000 -v todo-db:/etc/todos docker-101`

* Insert some data
* delete the container 
* run the above command again and check if the data your previously enter is there. 

* Where is my data store?
     * `docker volume inspect`
     
### Bind mounts

lets run the following command:

`docker run -dp 3000:3000 -w /app -v $PWD:/app node:10-alpine sh -c "yarn install && yarn run dev"`

wait a couple of seconds and modify the line 109 of the src/static/js/app.js file 

refresh the page and your changes will be reflected. 

## Multi Container app

### Starting MYSQL

* Create the Network

`docker network create todo-app`

* Start a MySQL caontainer and attach it to the network

`docker run -d --network todo-app --network-alias mysql -v todo-mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=secret -e MYSQL_DATABASE=todos mysql:5.7 `

* To confirm we have the database up and running connect to the database and verify it connects.

`docker exec -it <mysql-container-id> mysql -p`

Use the following password **secret** and then list all the databases `SHOW DATABASES;`

if you are able to see the todo DB you are ready to go. 

### Connecting our app to the DB

* Specify each of the enviroment variables above, as well as connect the container to our app network. 
    
`docker run -dp 3000:3000 -w /app -v "$(pwd):/app" --network todo-app -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=secret -e MYSQL_DB=todos node:12-alpine sh -c "yarn install && yarn run dev" `

* verify the app is up and running by checking the logs `docker logs CONTAINERID`
* Enter a few data on the application
* make sure the data is being saved in MySQL container 

`docker exec -it <mysql-container-id> mysql -p todos`

## Docker compose

* lets copy the following code to a file named "dockercompose.yaml"

```
version: "3.8"

services:
  app:
    image: node:12-alpine
    command: sh -c "yarn install && yarn run dev"
    ports:
      - 3000:3000
    working_dir: /app
    volumes:
      - ./:/app
    environment:
      MYSQL_HOST: mysql
      MYSQL_USER: root
      MYSQL_PASSWORD: secret
      MYSQL_DB: todos

  mysql:
    image: mysql:5.7
    volumes:
      - todo-mysql-data:/var/lib/mysql
    environment: 
      MYSQL_ROOT_PASSWORD: secret
      MYSQL_DATABASE: todos

volumes:
  todo-mysql-data:
```

* Take a moment to read all the information that is present and try to match it to what we previously had worked. 

* make sure there are not other container running and then execute the following comand:

`docker-compose up -d`

* check everything is working as expected and then you can stop the deployment by:

`docker-compose down`
