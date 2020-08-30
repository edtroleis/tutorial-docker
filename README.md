# Install docker-ce and docker-componse on Ubuntu 64 bits
```
Container is an isolated environment that doesn't need an hypervisor.

Docker is a software necessary to your application may run into a container. It shares SO libraries with the containers.
```

# Install docker-ce on Ubuntu 19.10
```
# Remove Docker old versions
sudo apt-get remove docker docker-engine docker.io

# Update package database
sudo apt-get update

# Add Docker oficial repository
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

# Add Docker repository into apt source
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"

# Update package database, now you have access to docker packages
sudo apt-get update

# Install docker-ce
sudo apt-get install docker-ce

# Check docker installation and version
sudo docker version 

# Add docker command without sudo
sudo usermod -aG docker $(whoami)
sudo systemctl restart docker

# Solve issue "Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.40/version: dial unix /var/run/docker.sock: connect: permission denied"
sudo chmod 666 /var/run/docker.sock
```


# Install docker-compose on Ubuntu 19.10
```
# Download docker-compose
sudo curl -L https://github.com/docker/compose/releases/download/1.15.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose

# Set permission
sudo chmod +x /usr/local/bin/docker-compose

# Check docker-compose instalation and version
docker-compose version
```

# Docker commands

### Show running containers
```
docker ps
```

### Show all containers
```
docker ps -a
```

## Run docker image nginx
```
docker run nginx
```

## Show docker images
```
docker images
```

## Remove docker image
```
docker rmi -f {image}
docker image rm -f {image}
```

## Mapp port
```
docker run -d -p 8080:80 nginx
# now you can access the container with port 8080 -> http://localhost:8080/
```

## Execute an imagem. After exit from the terminal the container is removed
```
docker run -it --rm nginx bash
```

## Execute commands with running container
```
docker exec {container_id} ls
```

## Execute commands with running container iterativity
```
docker exec -it {container_id|container_name} bash
```

## Check container configuration
```
docker inspect {container}
```

## Check container logs
```
docker logs {container_id}
```

## Check image layers
```
docker history {image}
```

## Create a new Docker image from a modified Docker image
```
docker commit {container_id} nginxtroleis:1.0
```

## Create a container and associate a name
```
docker run -d -p 8081:80 --name web02 nginxtroleis:1.0
```

## Check container logs
```
docker logs {conainter_id}
```

## Start/Restart a container
```
docker start {container_id}
docker restart {conainter_id}
```

# Volumes
When you create a volume to a container you can storage data into it and when you stop or remove the data container stored still there.


## Run a container with serveral parameters
```
run: execute docker image
-d: detach, release the terminal
--name webserver2 or --name=webserver2: call the cointainer as webserver2
-p 8081:80: mapping the external port 8081 to redirect to container port 80
-v ~/docker-project/html:/usr/share/nginx/html: mapping the container storage "/usr/share/nginx/html" to appoint to the local directory "~/docker-project/html"
nginx: the docker image
```
```
docker run -it -p 8080:80 --name web01 -v /data nginx bash

docker run -d --name webserver2 -p 8081:80 -v ~/docker-project/html:/usr/share/nginx/html nginx

docker run -d --name webserver2 -p 8081:80 -v $(pwd)/html:/usr/share/nginx/html nginx
```

## Stop and removing a container
```
docker rm webserver2 -f
```

## Stop/remove all containers
```
docker stop/rm $(docker ps -aq)
```

## Environment parameter "-e"
```
docker run -d --name=dbserver -e "MYSQL_ROOT_PASSWORD=root" -e "MYSQL_DATABASE=mypassword" mysql:5.7
```

## Check docker system
```
docker system <df|events|info|prune>

Commands:
  df          Show docker disk usage
  events      Get real time events from the server
  info        Display system-wide information
  prune       Remove unused data
```

## --link dbserver:mysql: link the container wordpress with the container dbserver and the internal name will be mysql
```
docker run -d --name=wordpress --link dbserver:mysql -p 8085:80 wordpress
```

## Install and check container configuration
```
docker exec -it wordpress bash
apt-get update
apt-get install mysql-client
apt-get install vim
apt-get install iputils-ping

ping -c 4 mysql (or dbserver or container hash)

cat /etc/hosts

mysql -uroot -h dbserver -p
*** password = mypassword
show databases;
bye;

vim wp-config.php
```

## Create Dockerfile

### Dockerfile, the first later must be capital. Create images, install and execute programs and create files inside the image
vim Dockerfile

```
FROM php:7.0-apache

RUN apt-get update && apt-get install -y vim
RUN docker-php-ext-install pdo pdo_mysql

WORKDIR /root
ADD ./html/index.html /root/
```

### Build Dockerfile
```
# user/image-name
# "." local where the Dockerfile is
docker build -t edtroleis/php7 .
```

# docker-compose

## Create a docker-compose.yml
Create containers based on the commands presented in the docker-compose.yml. docker-compose.yml is an automatizer

vim docker-compose.yml
```
version: "3.0"
services:
  db:
    image: mysql:5.7
    volumes:
      - "./.data/db:/var/lib/mysql"
    restart: always
    environment:
      - MYSQL_DATABASE=mydb
      - MYSQL_USER=root
      - MYSQL_ROOT_PASSWORD=root
   
  web:
    volumes:
      - "./html:/var/www/html"
    depends_on:
      - db
    image: edtroleis/php7
    links:
      - db
    ports:
      - "8080:80"
    restart: always
```

## Create the directory ./.data/db and ./html
```
mkdir .data/db -p
mkdir html
```

## Execute docker-compose.yml
```
# Start docker-compose and detach from terminal
docker-compose up -d

# Down the containers up with docker-compose.yml
docker-compose down

# Checking logs
docker-compose logs
```

# Push docker images in Docker registry
## create tag
```
docker tag {image-id} {registry-account}/{image-name}:{tag}
docker tag {image-id} edtroleis/debian:1.0
```

## Login Docker registry
```
docker login --username edtroleis
```

## Push image in Docker registry
```
docker push edtroleis/debian:1.0
```

## Check the image in Docker registry
```
https://hub.docker.com
```

# References
```
https://docs.docker.com/develop/develop-images/dockerfile_best-practices/
https://docs.docker.com/engine/reference/builder/
https://nickjanetakis.com/blog/docker-tip-2-the-difference-between-copy-and-add-in-a-dockerile
https://stackoverflow.com/questions/41935435/understanding-volume-instruction-in-dockerfile
```
