# Docker shortcuts & cheatsheet

## Table of Contents

- [Learning Docker Basics](#learning-docker-basics)
- [Dockerfile Directives: User and Run](#dockerfile-directive-user-and-run)
- [Building a Web Farm for Development and Testing](#building-a-web-farm-for-development-and-testing)

ENTRYPOINT vs COMMAND

   entrypoint will run on every container which will basically use that particular image 
   command will also run but it will only run the same way until some new argument is not passed to that
   
docker history image-name

docker ps --no-trunc

## Learning docker basics

list all docker images:
```bash
docker images
```

search docker images:
```bash
docker search centos
docker search apache
```
pull docker image
```bash
docker pull hello-world
docker pull nginx
```
run container
```bash
docker run nginx  # run the container
docker inspect nginx
```
List all containers:
```bash
docker ps
docker ps -a
```
Run the container:
```bash
docker run -d nginx
docker run -it centos:latest /bin/bash # i - interactively and t- connected to the terminal d- detach mode for running it in background and exit will close this container
docker run -d centos:latest # run contianer in background
docker run -d --name=myweb1 nginx # will give name myweb1
docker run -d --name=myweb2 nginx # will start second container with name myweb2
docker rename runningcontainer-name new-name
```
detail information about the container including ip address:
```bash
docker inspect container-id | grep ip # it will give detial information and also the ip address
docker ps # it will show on which port application is running on
```

terminal web based browser:
```bash
sudo yum install elinks     
elinks http://172.17.0.2 # use the ip address from docker inspect command
telnet localhost #will give you the error
telnet http://172.17.0.2 80
```
expose the port:
```bash
docker run -d --name=webserver1 -P nginx:latest # Any port that is exposed to my container I want you to make avaialble through host opearating system
on random port between 32768 and 65535
elinks http://172.17.0.2:32768 #dokcer ps will tell the host os port
elinks http://localhost:32768 # now this will not give the error
# we can test same thing using elink or we can use any browser and test it with public ip of instance with port no

to map to specific port we can use this method:

docker run -d -p 8080:80 --name=webserver1 nginx:latest
docker run -d -p 8080:80 -p 8443:443 --name=webserver1 nginx:latest #you can multiple port also
docker run -itd -p 127.0.0.1:8081:80 nginx:latest

  -p 8080:80 	#Map TCP port 80 in the container to port 8080 on the Docker host.
  -p 8080:80 -p 8081:80 container port 80 to the host port 8080 and 8081
  -p 192.168.1.100:8080:80 	#Map TCP port 80 in the container to port 8080 on the Docker host for connections to host IP 192.168.1.100.
  -p 8080:80/udp 	#Map UDP port 80 in the container to port 8080 on the Docker host.
  -p 8080:80/tcp -p 8080:80/udp 	#Map TCP port 80 in the container to TCP port 8080 on the Docker host, and map UDP port 80 in the container to UDP port 8080 on the Docker host.
```
start/stop container:
```bash
docker stop container-id or name
docker start LifeCycle1
docker restart LifeCycle1
```
Dockerfile: build the image using the dockerfile
```bash
docker build -t latest123/apache . # where . is location for the Dockerfile 
# it will create the image from the base image 
# it wil basically create the image layer by layer and each layer is new container
```
Create your own custom image
```bash
docker run -it ubuntu:xenial /bin/bash #make custom changes in this base image
docker ps -a #copy contianer id to create the custom image
docker commit -m "already installed ssh and test user" -a "jatin" aeade3a2defa makani/ubuntusshd:v1
```
attach the container:
```bash
#you can use attach and exec with running container
docker attach container-id # it will attach but on exit it will stop the container
docker exec -it LifeCycle1 /bin/bash # exit will not stop the container
docker exec container-id /bin/cat /etc/profile #will give you the cat result for /etc/profile file from the running container
docker run -d ubuntu:xenial /bin/bash -c "while true; do echo HELLO;sleep 1;done" #run container as daemon
```
remove images and container:
```bash
docker images
docker rmi image-name # it will not remove the image if we have used the image previously
docker rmi -f image-name # will remove image forcefull
docker rmi -f image-id #you can remove all the image with same base image id but with different tag
docker rmi $(docker images -a -q) # will remove all the images
docker rmi $(docker images -q) # will remove only unused images

docker rm container-id # it will remove the container
docker rm container-id container-id container-id
docker rm -f container-id # will remove the container forcefully
docker rm `docker ps -a -q` # it will remove all the container which are stopped
docker system prune # remove all the images, stopped containers, nerworks and build cache

#also you can remove container from docker file system /var/lib/docker/containers
#you will find same set of containers here which you can see via `docker ps -aq` command
#BUT MAKE SURE TO STOP DOCKER VEFORE THAT OTHERWISE STILL YOU WILL SEE SAME CONTAINER USING `docker ps -aq` COMMAND
#ALSO IF YOU CAN RESTART THE DOCKER TO CHANGES TAKE EFFECT
```
search for open ports:
```bash
docker port webserver1 $CONTAINERPORT # it will give you the port mapping for the webserver1 container
```
STORAGE
```bash
/var/lib/docker #where docker will store all the images containers and lot more
```
mount data inside the container:
```bash
docker run -d -p 8080:80 --name=webserver1 -v /mnt/data nginx:latest 
docker run -d -p 8080:80 --name=webserver1 -v /home/user/www:/usr/share/nginx/html nginx:latest
# you can store index.html file on host /home/usr/www/ location and then map it to the /usr/share/nginx/html
# basically container will use host index.html file using this commands
```
Troubleshooting:
```bash
curl http://localhost:51678/v1/tasks | python-msjson.tool
docker logs <container id>
docker ps -a
docker inspect <container id>
```
volume inforamtion:
```bash
docker ifo | grep "Data Space"
# resize the metadata space
lvresize --poolmetadatasize +10M /dev/docker/docker-pool
```
## Dockerfile Directive user and run

Dockerfile #to non-privileged user entry
```bash
FROM centos:latest
MAINTAINER jmakani@hawk.iit.edu
RUN useradd -ms /bin/bash user
USER user
RUN echo "EXPORT 192.168.0.0/24" >> /etc/exports.list 
# above command will give the error as it will run as USER user not root so if you want to run that command please change the sequence of the command just above the `USER user`
RUN echo "EXPORT JAVA_home=......." >> /home/user/.bashrc # one way to declare env variable for user
ENV JAVA_HOME /usr/java/jdk1.8.0/jre/ # way to declare system wide env variable

docker -it centos7/nonroot:v1 /bin/bash
docker exec -u 0 -it container-id /bin/bash # to run that container as a root user
```
RUN vs CMD
```bash
RUN will be part of base image normally but CMD will run at container instantation (in other word it is part of configuratio of base image)
CMD "echo" "This is a custom contianer message" 
# it will only run during the instantiation of contianer based on base image
```
ENTRYPOINT
```bash
ENTRYPOINT "echo" "THis command will displayes this message on every container that is run from it"
It will create the concrete entry point for container, it behaves same like CMD but for CMD you can change the behavioud while run time but for ENTRYPOINT command you cant change the behaviour
```
EXPOSE
```bash
EXPOSE 80 #will automatically expose port 80 while running container from this image
```
VOLUME
```bash
docker run -it --name volumetest1 -v /mydata centos:latest /bin/bash
/etc/lib/docker #docker inspect command will give name of volume and we can see that volume in this directory on host operating system

#to map host OS directory to the container volume
docker run -it --name volumetest1 -v /home/ec2-user/test:/mydata centos:latest /bin/bash
```
Docker Network: List and Inspect
```bash
docker network ls #it will list all the network associated with host
docker network ls --no-trunc #to see full network id
docker network inspect network-name # detail about network

man docker-network-create #load manual pages for this command
docker network create --subnet 10.1.0.0/24 --gateway 10.1.0.1  test #to create the new network and docker will automatically create the bridge adapter for you 
ifconfig #to see the new bridge adapter

# PLEASE BE CAREFUL WHILE REMOVING THE NETWORK and dont remove three default network bridge, host, none
docker network rm network-name
```
Docker Network: Assign to Containers
```bash
docker network create --subnet 10.1.0.0/16 --gateway 10.1.0.1 --ip-range=10.1.4.0/24 --driver=bridge --label=host4network bridge04
docker run -it --name nettest1 --net bridge04 centos:latest /bin/bash
      
yum update
yum install -y net-tools
ifconfig #we can see that 10.1.4.0 first one from ip range
netstat -rn #we can see default gateway
ping google.com
cat /etc/resolv.conf
#YOU CAN NOT ASSING THE PRIVATE IP TO THE DOCKER 0 NETWORK ADAPTER
#YOU CAN ONLY ASSIGN PRIVATE IP TO THE USER CREATED NETWORK
docker run -it --name nettest2 --net bridge04 --ip 10.1.4.100 centos:latest /bin/bash
yum update
yum install -y net-tools
ifconfig #we can see that 10.1.4.100
```
## Inspect Container Processes/ Docker performance
```bash
docker exec container-name /bin/ps
docker exec container-name /bin/ps aux | grep bash
docker top container-name
docker exec -i -t contianer-name /bin/bash
top #you can run top from inside the container
```
Docker Events
```bash
docker events
docker events --since '48h'
docker events --since 48h
docker events --filter event=attach --filter event=stop --filter event=die
docker rename runningcontainer-name new-name
```
Saving and loading docker images
```bash
docker run -it centos:latest /bin/bash
docker ps -a
docker commit eloquent_hodgkin centos:mine
docker save -o centos.latest.tar centos:latest
docker save --output centos.latest.tar centos:latest
tar tvf centos.latest.tar 
docker rmi centos:latest
gzip centos.latest.tar #compress tar to store the disk space
docker load --input centos.latest.tar.gz 
docker load --input centos.latest.tar
```
Image history
```bash
docker history centos:mine
docker history --no-trunc centos:mine
docker history --no-trunc centos:mine > output.txt
docker history --quiet nginx #will give you all container id associated with this image
docker history --quiet  --no-trunc nginx #will give full SHA-256 image-id

docker tag image-id tag-name
docker tag existing-image-name new-tag-name
```
Push images to docker hub
```bash
docker login #enter username and password
cd .docker
cat config.json
docker tag image-name tag-name
docker push image-name
docker pull username/image-name
docker logout # if you dont need to store these credential after session
```
## Building a Web Farm for Development and Testing

### Create the webapp and try to reverse proxy using nginx
webapp1 running with port 80 open and linked to port 8081
webapp2 running with port 80 opean and linked to port 8082
use nginx to redirect traffic to port 80 on host to port 8081 and 8082 and from there to docker container port 80
```bash
docker run -it centos:centos6 /bin/bash
yum update
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-6.noarch.rpm 
#refer https://fedoraproject.org/wiki/EPEL
yum install which sudo httpd php openssh-server
whoami
which service
/sbin/bash
cd
vi .bashrc
`/sbin/bash service httpd start`
`/sbin/bash service sshd start`
exit
docker commit container-id centos6:baseweb
docker run -it centos:baseweb /bin/bash

docker run -it --name=webtest -v /home/ec2-user/docker/dockerwww:/var/www/html centos6:baseweb /bin/bash

docker run -itd  --name=devweb1 -p 8081:80 -v /home/ec2-user/docker/dockerwww:/var/www/html centos6:finalweb1 /bin/bash
docker run -itd  --name=devweb2 -p 8082:80 -v /home/ec2-user/docker/dockerwww:/var/www/html centos6:finalweb1 /bin/bash

# on your host machine isntall the nginx to reverse proxy
sudo yum install nginx
sudo yum update

#create this file to redirect all the traffic coming to host port 8081 and 8082 to the docker port 80

nginx will look all the conf file in this directory before start up so change it and dont forgot to restart the nginx

cd /etc/nginx/conf.d/default.conf

    upstream containerapp {
      server 34.207.177.183:8081;
      server 34.207.177.183:8082;
    }

    server{
            listen *:80;
            server_name 34.207.177.183;
            index index.html index.htm index.php;
            access_log /var/log/localweb.log;
            error_log /var/log/nginxlocalerr.log;

            location / {
                    proxy_pass http://containerapp;
            }
    }
 
sudo yum restart nginx
```
### Integrating Custom Network In Your Docker Containers
```bash
service docker stop
ip link add br10 type bridge
ip addr add 10.10.100.1/24 dev br10 #we are passing br10 as device to use
ifconfig #we can see br10 bridge adapter

docker -d -b br10 &

docker run -it  --name=tomcatweb1 -v /root/docker/downloads:/root/downloads -p 8180:8080 -e JAVA_HOME=/opt/java -e JRE_HOME=/opt/java centos:jdk7tomcat7 /bin/bash

apt-get update && apt-get upgrade && apt-get install apache2
vi .bashrc
Add the line "service apache2 start" to the /root/.bashrc file at the very END
docker commit basic_web ubuntu:baseweb
docker run -it --name="test_container" ubuntu:baseweb /bin/bash
```
