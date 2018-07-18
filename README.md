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
	13. ENV : set env variables (available when image is build and when executed).
	
**Build DockerFile** : docker image build --help -> To get list of all options.	
docker image build -f <DOCKER_FILE_PATH> -t <REPOSITORY>:<TAG> . 

**.dockerignore** : This file is used to ignore files. Remember by default all the files/folder in the Dockerfile folder is being copied. 

**DOCKER has a empty tar file called scratch on top of which you can create your dockerfile.**
	
		
		
		


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




