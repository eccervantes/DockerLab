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
