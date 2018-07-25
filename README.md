# BOOK - DOCKER DEEP DIVE NOTES #

1. TO Show SHA : docker image ls --digests (show the SHA of the image). 
2. To inspect images : docker image inspect alpine:latest   , Also this would show the command that would be rn when the container starts ïn "Cmd
3. we know ctrl-PQ to come out. then to reattach. 
	a. docker container attach <name_of_the_container>
	b. OR this command : docker container exec -it <NAME_OR_ID_OF_CONTAINER> bash  (exec creates a new bash if you used so).
4. docker container ls -a ( -a will show all containers that are stopped).
5. -d flag will start a docker container in background. -d and -it can't be used in same command. 
6. -p 80:8080 this is used for port forwarding.
7. You can use docker container START|STOP|RUN|PAUSE
8. docker container rm ${docker container ls -aq) -f   : -aq give the list of all the container ID's. 
9. docker image rm ${docker image ls -aq) -f   : -aq give the list of all the image ID's. 
19. docker container inspect <CONTAINER_ID> : will give all the details of the container

## Build the Image from Docker File.  ##
1. docker image build -t web:latest .   [ web:latest is the name of the image created ] 
2. docker image history web:latest  [ it shows all the list of commands present in docker file used to create the IMAGE
3. docker image inspect web:latest [ to watch all the metatdata in the JSON format ] 


## DOCKER SWARM:  ##
1. docker swarm init  - Turn singel machine node in Docker Swarm mode. 
2. docker swarm join -> Join an existing SWARM.




# BOOK - PAINLESS DOCKER

1. we can restrict the securities via using CAPABILITIES feature or seccomp feature, to restrict the SYSTEM calls that we can make to the Host kernel. Even you can use APP_ARMOR to restrict prorams capabilities with per program profiles. 
2. Initially docker uses AUFS now it also uses multiple others like OverlayFS (on ubuntu), BTRFS, ZFS.These are all storage drivers (i.e. device mappers)

## DOCKER IMAGES:  ##
1. **docker images - list all the images**

	1. docker image --format "{{.ID}} : {{.REPOSITORY}}"  -> Gives selective output. 
	2. docker imagse -q -> just get ID'same. 
 	3. docker images -a -> list all of them.
 	4. It might list none:none which is nothign but a set of intermediate images. 
 	5. All docker layers are stored in /var/lib/docker/graph
 	6. Get Dangling images (intermediary images) : docker images --filter "dangling=true"


2. **Finding Images: docker search ubuntu** 

	1. Limit the records : docker search --limit 100 mongodb
	2. docker search  --filter=stars=5 mongo   -> only where the stars are 5. 
	3. docker search --filter=is-official=true mongo -> only select official images. 
	4. docker search http://localhost:5000/ubuntu -> Search the local DOCKER REGISTRY (if you have one running on local system). 
		
3. **Pulling Imagse : docker pull ubuntu**

	1. docker pull ubuntu:devel : Download the development ubuntu image
		
	2. Removing images : docker rmi {name of the image}
		1. docker rmi $(docker images -q)
		2. docker rmi $(docker images -f "dangling=true" -q)
		
## DOCKER FILE : Dockerfile ##
	1. FROM <image>:<tag>
	2. MAINTAINER <name>
	3. RUN <command>  -> to Run commands or executables or RUN ["<exectuable>", "param1", "param2"] E.g. RUN ["/bin/sh", "-c", "ls", "-l"]. RUN CMD1 && CMD2
	4. CMD <command> : Identify which executable should be run when container starts. During execution of the container. 
	5. LABEL <key1>=<valu1> <key2>=<valu2> : this will add METADATA to a given image. 
	6. COPY : COPY <SOURCE FILE ON HOST> <DESTINATION_PATH>
	7. ADD -> to Add files to a location : <SOURCE> <DESTINATION> : it also uncompresses the files. It also add contents from remote resources e.g. HTTP (but here it wont uncompress it).
	8. EXPOSE 80/tcp -> Exposing a port.
	9. ENTRYPOINT / CMD -> needs to make the container executable. NOrmally it is used in conjunction with each other. 
	10. USEr -> used to specify the user under which the commands can be run. Can be used with CMD/RUN things. 
	11. WORKDIR : Sets the working directory for same set of instructions (e.g. RUN/CMD/ENTRYPOINT)
	12. ONBUILD RUN <COMMAND> : this would a command (like update and upgrade package) every time our images is used as a base for another image. 
	13. ENV : set env variables (available when image is build and when executed).E.g. ENV a=b c=d
	
**Build DockerFile** : docker image build --help -> To get list of all options.	
docker image build -f <DOCKER_FILE_PATH> -t <REPOSITORY>:<TAG> . 

**.dockerignore** : This file is used to ignore files. Remember by default all the files/folder in the Dockerfile folder is being copied. 

**DOCKER has a empty tar file called scratch on top of which you can create your dockerfile.**
	
		
## DOCKER REGISTRY ##
1. Read how to create Docker Registry (running it within DOCKER container).
2. Docker Registry uses /var/lib/registry folder to store all images on the local file system, however you can use Google Cloud, Azure, Amazon S3 and Swift. 
3. All pull/push related to the registry must be preceded by the localhost:5000 to determine the registry. 
4. MICROBADGER is a website that gives you all the information about a image publicly available on DOCKER HUB. DTR is enterprise based solution (Docker Trusted Registry)
5. Steps to do registry 
	1.  docker image pull registry:2 -> Will pull docker registry application.
	2.  docker image ls -> the above image will be available in docker ls
	3.  docker container run -d -p 5000:5000 --name DockerRegistry registry:2  -> it will run the registry.
	4.  docker image pull alpine
	5.  docker image tag alpine localhost:5000/localalpine  ->  Tag the image on our local 
	6.  docker image push localhost:5000/localalpine -> this will push the image to registry
	7.  docker image rm alpine localhost:5000/localalpine -> This will delete the local tagged images, (since now you have two images having same layer i.e. image ID).
	8.  docker image pull localhost:5000/localalpine:latest -> It will pull from the current running registry on your local. 


##MANAGING CONTAINERS
1. **docker container run hello-world**
2. **docker container ls -a** -> display all the containers (even if they are exited).
3. **docker container rm <CONTAINER_ID>**-> Remove the container ID. 
4. Options used in docker container run command [ docker container run --help ] 
	1. -d -> Detach and run it in the background. 
	2. --name -> This is used to Give the container a name.
	2. -p -> For PORT Forwarding
5. **docker container attach <NAME_OR_IMAGE_ID> **  
	1. **docker container attach **--sig-proxy=false**     [NAME\_CONTAINER\_ID] **-> if you press CTRL-C will detach from the process 

6. **docker container exec [NAME] cat /etc/debian_version**  : it spawns another process within the container and executes the command > e.g. this one will list the contents of the file on screen.
	1. **docker container exec -it NAME_ID /bin/bash** (will execute bash and attach you to it, if you dont' pass -it , it will kind of hang and wait for you to kill it. ps -elf will show you all the processes running in the system. 
7. **docker container pause nginx1 **: it will PAUSE the container, for resume use unpause

###LOGS CONTAINER###
1. The logs command allows you to interact with the STDOUT stream of your containers
	1. docker container logs --tail 5 NAME\_CONTAINER\_ID
	2. docker container logs -f NAME_\CONTAINER\_ID
	3. docker container logs --since 2017-06-24T15:00 NAME_\CONTAINER\_ID : Get logs since time
	4. docker timestamp is always behind when the log happens. to show docker captured time we have to use -t flag. 

###TOP Command
1. IT shows the processes running within the container. **docker container top NAME\_ID**

###STATS Command
1. IT shows the real time information on (single or all) running within the container. **docker container stats NAME\_ID**

###RESOURCES LIMITS on Container
1. docker container run -d --cpu-shares 512 --memory 128M ubuntu  [ Applying resource limits]
2. docker container update --cpu-shares 512 --memory 128M --memory-swap 128M ubuntu [ Update it after running ]

### start / stop / restart / kill commands ##
1. docker container stop -t 60 nginx1 : [ stop in 60 seconds ]
2. docker container start nginx2 nginx3 : [it starts from scratch] 
3. docker container kill nginx3 [Kill the container sending SIGTERM 9]

### Removing Containers 
1. docker container prune : To remove exited containers
2. docker container rm nginx4 : Remove a container , -f forcefully

### Creating Containers/ list ports of a container/ diff
1. docker container create -p 8080:80 nginx **->** Only create the container but dont' start it.
2. docker container port NAME\_ID **->** List the port of the container that was being mapped.
3. docker container diff nginx-test **->** List the diff in the files between image n running container. 

## DOCKER NETWORKING 
1. docker network create moby-counter **->** Create a Network
2. docker container run --network moby-counter redis:alpine **->** : Start a container in the network.
3. docker network prune 

**NOTE:** you can run multiple composite applications (i.e. a system) in two networks.

## DOCKER MACHINE

1. A tool to install docker host anywhere on AWS/OpenStack/GCP/Azure/VMWARE/VirtualBox. 

## DOCKER COMPOSE.  

1. File would be **docker-compose.yml**
2. use **docker-compose up** 
3. Options to use in above command are -d :-> To Run in the background. 
4. use **docker-compose ps** : to list the containers that are present in the docker compose yaml file. 
5. **docker-compose config** :-> This will check your docker compose yml file and render copy of the yaml file to the screen.
6. **docker-compose pull** -> will pull any images it find i the yaml file. 
7. **docker-compose build**  -> will execute any build instructions it find in the file.  
8. **docker-compose create**  -> will create the containers but won't launch anything.
9. **docker-compose top** -> Display information about processes running in all launched containers.
10. **docker-compose logs** -> Display LOGS of all the processes running in all launched containers.
11. **docker-compose events** -> Display all the events. 
12. **docker-compose pause** -> will pause all the launched containers. 


13. **docker-compose exec worker ping -c 3 db** -> Execute the EXEC command from the worker container and ping the DB machine.

###################################################################

Loook for : 3
Docker COMPOSE : painless docker basic edition
DOCKER logging  : painless docker basic edition
docker debugging  :painless docker basic edition
docker API : painless docker basic edition
docker networking  : painless docker basic edition
docker volume 
docker security : painless docker basic edition 





Native Docker Clustering with Swarm - read it completed. 

Docker Management Design Patterns Swarm Mode on Amazon Web Services : read it complete

Developing with Docker - .pdf    - 2

Mastering - Docker  - 1








DevOps - Jenkins Deep Dive




