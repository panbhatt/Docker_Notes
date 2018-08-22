## Add user to docker
sudo usermod -aG docker $USER

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
14. **docker-compose kill** -> It will kill all the containers launched previously. **stop** can be used for a graceful shutdown. 
15. **docker-compose rm** -> Remove any containers with the state of EXITED. 
16. **docker-compose down** -> This will remove the containers and the networks created when running **docker-compose up**.

### Third Party Tools
1. **Docker-compose-viz **: This docker container (or tool) will inspect a docker compose file and will generate a graphical image of the complete system. 
2. **UNIVERSAL CONTROL PLANE** -> Enterprise Grade Cluster Management System.


###Links to Read###
[https://michalzalecki.com/docker-compose-for-nodejs-and-postgresql/](https://michalzalecki.com/docker-compose-for-nodejs-and-postgresql/ "https://michalzalecki.com/docker-compose-for-nodejs-and-postgresql/")

[https://blog.codeship.com/using-docker-compose-for-nodejs-development/](https://blog.codeship.com/using-docker-compose-for-nodejs-development/ "https://blog.codeship.com/using-docker-compose-for-nodejs-development/")

[https://medium.com/datreeio/node-js-docker-workflow-b9d936c931e1](https://medium.com/datreeio/node-js-docker-workflow-b9d936c931e1 "https://medium.com/datreeio/node-js-docker-workflow-b9d936c931e1")     
Best production ready things are explained here. 

[https://nodejs.org/en/docs/guides/nodejs-docker-webapp/](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/ "Create Nodejs based Docker image by Nodejs Org")

[https://medium.com/ma-digital-services/dev-env-5d35b97f3473](https://medium.com/ma-digital-services/dev-env-5d35b97f3473 "DOCKER COMPOSE BIG EXAMPLE")  Docker Compose Big Example.

### SCALE Command ###
This is used to scale the number of a specific service to a number of instances. 
**docker-compose up -d --scale worker=3**

## DOCKER GUI'S
[https://blog.codeship.com/docker-guis/](https://blog.codeship.com/docker-guis/ "https://blog.codeship.com/docker-guis/")
## Interesting packages. 
[https://www.infoq.com/articles/metaparticle-pulumi-ballerina](https://www.infoq.com/articles/metaparticle-pulumi-ballerina "https://www.infoq.com/articles/metaparticle-pulumi-ballerina")

## DOCKER SWARM ##
1. Docker Swarm mode comes with docker pas ahead of 1.12 version.
2. **docker swarm --help** -> Gives you all the details of docker swarm.
3. A Host can assume only two roles (one at a time) : MANAGER / WORKER 
4. For production we need to have minimum of 5 Cluster manager (that can take max 2 system failures). 

### Commands after testing
**sudo docker swarm init --advertise-addr 192.168.1.20:2377 --listen-addr 192.168.1.20:2377**
Swarm initialized: current node (t4hyd9kuo4qjnvturkq5uanrp) is now a manager.

**To add a worker to this swarm, run the following command:**

docker swarm join --token SWMTKN-1-2hvuupx97en93ywdnjzct09jtb0k1xnfpxq2d54iycph0zdg7f-07psc70n8gj01a2jd2xfxtklg 192.168.1.20:2377

**To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.**

**pankaj@b76:~$ sudo docker swarm join-token manager**
To add a manager to this swarm, run the following command:

docker swarm join --token SWMTKN-1-2hvuupx97en93ywdnjzct09jtb0k1xnfpxq2d54iycph0zdg7f-dra8hy6e5smfxiidchls0kuo0 192.168.1.20:2377

**pankaj@b76:~$ sudo docker node ls**
**pankaj@b76:~$ sudo docker-machine ls**
NAME             ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
swarm-worker01   -        virtualbox   Running   tcp://192.168.99.101:2376           v18.06.0-ce   
pankaj@b76:~$ 

**From the Swarm Machine:**
**sudo docker-machine ssh swarm-worker01**
docker swarm join --token SWMTKN-1-2hvuupx97en93ywdnjzct09jtb0k1xnfpxq2d54iycph0zdg7f-07psc70n8gj01a2jd2xfxt
klg 192.168.1.20:2377
This node joined a swarm as a worker.

sudo docker node demote pankajUBClone
sudo docker node ls

 sudo docker node promote pankajUBClone
sudo docker node ls


**sudo docker node update --availability drain pankajUBClone**  -> remove the node for sometime , so that maintenance activity can be perofrmed on it. 
**sudo docker node update --availability active pankajUBClone**  -> Restore it back. 


### Tutorial : [https://rominirani.com/docker-swarm-tutorial-b67470cf8872](https://rominirani.com/docker-swarm-tutorial-b67470cf8872 "https://rominirani.com/docker-swarm-tutorial-b67470cf8872")

### STEPS: 
1. docker swarm init --advertise-addr 192.168.56.104:2377  --listen-addr 192.168.56.104:2377
2. JOIN a WORKER: docker swarm join 192.168.56.104:2377 --token *********   -> This will give a reply that the worker had joined the cl
3. 
4. uster. 

## DOCKER SECURITY
1. **docker container diff mysql** -> This command will allow you to figure out the diff between the image and the running container.
2. **DOCKER BENCH** : Run a docker container against the HOST for which we want to check for the vulnerability and it will give CIS (Container for Internet Security) related benchmarks (Container Industry standard). It covers Docker Daemon configuration, Docker daemon configuration files, container images and build files, container runtime and Docker Security Operations. 
3. Third Party Services.
	1. Quay
	2. Clair


#BOOK -> Deployment with Docker - Apply Continous Integration Models.

**This is one of the best books to start up and whenever you wanted to return to learn stuff quickly. **




##TO DO : 
Docker Management Design Pattern on SWARM - AWS
Continous Delivery with DOCKER n JENKINS
KUBERNETES in ACTION
Docker and Kubernetes for java developers. 

##IMPORTANT READS
[https://medium.com/lucjuggery/about-var-run-docker-sock-3bfd276e12fd](https://medium.com/lucjuggery/about-var-run-docker-sock-3bfd276e12fd "Desc of DOCKER SOCK /var/run/docker.sock")

**Load Balancing in Docker SWARM** : [https://botleg.com/stories/load-balancing-with-docker-swarm/](https://botleg.com/stories/load-balancing-with-docker-swarm/ "https://botleg.com/stories/load-balancing-with-docker-swarm/")
**Monitoring Docker Swarm with cAdvisor, InfluxDB and Grafana** 
[https://botleg.com/stories/monitoring-docker-swarm-with-cadvisor-influxdb-and-grafana/](https://botleg.com/stories/monitoring-docker-swarm-with-cadvisor-influxdb-and-grafana/ "https://botleg.com/stories/monitoring-docker-swarm-with-cadvisor-influxdb-and-grafana/")
**Log Management for Docker Swarm with ELK Stack** [https://botleg.com/stories/log-management-of-docker-swarm-with-elk-stack/](https://botleg.com/stories/log-management-of-docker-swarm-with-elk-stack/ "https://botleg.com/stories/log-management-of-docker-swarm-with-elk-stack/")
**Building Blue-Green Deployment with Docker** [https://botleg.com/stories/blue-green-deployment-with-docker/](https://botleg.com/stories/blue-green-deployment-with-docker/ "https://botleg.com/stories/blue-green-deployment-with-docker/")
**Auto Scaling with Docker** [https://botleg.com/stories/auto-scaling-with-docker/](https://botleg.com/stories/auto-scaling-with-docker/ "https://botleg.com/stories/auto-scaling-with-docker/")
**Setup Gitlab for Docker based development** : [https://botleg.com/stories/setup-gitlab-for-docker-based-development/](https://botleg.com/stories/setup-gitlab-for-docker-based-development/ "https://botleg.com/stories/setup-gitlab-for-docker-based-development/")


**HTTPS with Let's Encrypt and nginx** : [https://botleg.com/stories/https-with-lets-encrypt-and-nginx/](https://botleg.com/stories/https-with-lets-encrypt-and-nginx/ "https://botleg.com/stories/https-with-lets-encrypt-and-nginx/")


[https://docker-curriculum.com](https://docker-curriculum.com "https://docker-curriculum.com")
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



## PLURALSIGHT VIDEOS:
VIDEOS BOOKS as SWARM
HEALTH with DOCKER
Docker in Production Using Amazon Web Services
https://app.pluralsight.com/paths/skills/managing-containers-with-docker






###################################################
#KUBERNETES#
##KUBERNETES DEEP DIVE ##
[https://labs.play-with-k8s.com](https://labs.play-with-k8s.com "https://labs.play-with-k8s.com")

[https://training.play-with-kubernetes.com/](https://training.play-with-kubernetes.com/ "https://training.play-with-kubernetes.com/")


1. kubectl version  --output json -> This will give  the version in the JSON Format. 
2. kubeadm version --output yaml -> Thsi will give version of KUBEADM in the yaml format. 
3. kubeadm init --apiserver-advertise-address $(hostname -i)  -> Start a CLUSTER
4. kubectl get nodes -> Gives list of nodes, you will see NOTREADY
5. kubectl apply -n kube-system -f \
    "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')" -> This will create a POD network and will make the KUBERNETES ready. Try command no 4 above to verify this.

6. Create another instance : kubeadm join --token f969fb.2c22a568412952e5 192.168.0.13:6443 --discovery-token-ca-cert-hash sha256:1a89071d50f30360268bdb43b298838d19f095a979b5e56810e4c16c13af20a9 
7. Run -> kubectl get nodes 

##INSTALLATION 
1. https://medium.com/@Alibaba_Cloud/how-to-install-and-deploy-kubernetes-on-ubuntu-16-04-6769fd1646db

MINIKUBE Download LINKE: https://github.com/kubernetes/minikube/releases 	

###MINIKUBE
	1. https://www.marksei.com/minikube-kubernetes-windows/ : Installation of MINIKUBE on WINDOWS.