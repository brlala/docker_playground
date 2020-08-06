# docker_playground
Orchestrating docker containers

# Docker
## Commands
### Useful commands
1. `docker version` - check version and see if everythings working accordingly  
2. `docker container run --publish 80:80 nginx` - pull image nginx and run it, forwarding connections from port 80 to port 80 inside container  
    * `--rm` remove container upon exit
    * `-it` run commands, commands should be at last like `.... bash`
    * `--detach` or `-d` to run it in background
    * `--name <name>` to name the container explicitly.  
    * `--publish` or `-p` to expose ports
        * exposing multiple ports `docker run -d --name some-rabbit -p 5672:5672 -p 5673:5673 -p 15672:15672 rabbitmq:3-management`
    * `-e PATH=XXX` for environment variables  
    * `--network <network name>` - connects container to the said network
    * `--network-alias <dns name>` - dns name for lookup
    * `-v <mysql-db:/var/lib/mysql>` - assigning a name to a volume
        * mounting on windows `docker run -it -v "d:/data:/data" -p 6379:6379 --name redis -d redis`
    * `-v </Users/test:/var/lib/mysql>` - bind mounting container to a folder, do note the path starts with a `/`  e.g. `docker container run -d --name db -p 3306:3306  -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql`  
3. `docker container ls` - list out running containers
    * `-a` can be used to list all containers available
4. `docker container stop <container ID>` - stopping docker instance
5. `docker container start -ai ubuntu` - starting existing container
5. `docker container logs <name>` - list logs of the container
    * `-f` follow command to watch logs
6. `docker container top <name>` - process running inside the container
7. `docker container --help` - list all commands
8. `docker container rm <name> <name>...` - remove the containers, can take in multiple values. running containers will need to be stopped first
    * `-f` - force remove of container even if it's running  
9. `docker info` - display info of docker, use this to check swarm mode
    
### Monitoring commands
9. `docker run -it --rm --privileged --pid=host justincormack/nsenter1` - connecting to docker VM [source](https://github.com/justincormack/nsenter1)
10. `ps aux` - list all processes
    * `| grep <name>` can be used to filter process names
11. `docker container inspect <name>` - to return the configuration of the container
12. `docker container stats` - return streaming lives tats of all containers

### Getting Shell inside containers
13. `docker container run -it` - start new container interactively+psuedo TTY
14. `docker container exec -it` - run additional command in existing container
    * `docker exec -it <container_name> bash`

### Networking Commands
1. `docker container port <name>` - see ports opened on container  
2. `docker container inspect --format '{{.NetworkSettings.IPAddress}}' webhost` - see host IP of docker container  
3. `docker network ls` - show all networks created  
4. `docker network inspect <network name>` - shows network related configuration like containers connected to the network, IP and gateway of the network  
5. `docker network create <network name>` - create a new network  
    * `--driver overlay` or `-d overlay` to create a network that spans across clusters  
6. `docker network connect <network name> <name>` - connect network to container  
7. `docker network disconnect <network name> <name>` - disconnect network to container  

### Image Commands
`docker image inspect <name>` - contains metadata like ports exposed, environment variables, command to run when an image is run  
`docker image tag <source_image> <new_image_tag>` - will create a new tag from the source image  
`docker image push <image_tag>` - push to dockerhub  

### Volume commands
`docker volume create`

### Authentication Command
`docker login` - login  
`docker logout` - logout  
    
### Batch commands
`docker rm -f $(docker ps -a -q)` - delete all containers  
`docker volume rm $(docker volume ls -q)` - delete all volumes  
`docker image rm -f $(docker image ls -a)` - delete all images  

### Cleanup commands
    removecontainers() {
        docker stop $(docker ps -aq)
        docker rm $(docker ps -aq)
    }
    
    armageddon() {
        removecontainers
        docker network prune -f
        docker rmi -f $(docker images --filter dangling=true -qa)
        docker volume rm $(docker volume ls --filter dangling=true -q)
        docker rmi -f $(docker images -qa)
    }
    
    `docker system prune --volumes` Use the -f or --force option to bypass the prompt.
    `docker system prune`
    `docker network prune`

## Dockerfile
`FROM` all images must have a from normally from a minimal linux distribution  
`WORKDIR` change working directory, preferred over `RUN cd /path`  
`ENV` optional environment variable that is used for container building and running container  
`RUN` execute shell commands  
`VOLUME` mounting a volume for persistent data, this needs manual delete for assurance  
`EXPOSE` ports that are being exposed  
`CMD` command that will be run everytime we restart the container or everytime a container starts  
`COPY` copy from local our build machine into containers  

# Docker-compose
## Commands
### Useful commands
1. `docker-compose ps` - show all running docker-compose  
2. `docker-compose top` - show stats  
3. `docker-compose up` - start docker-compose  
    * `-d` detach container  
4. `docker-compose down` - stop docker-compose  


# Docker-swarm
## Commands
### Useful commands
1. `docker swarm init` - initiate docker swarm  
2. `docker swarm join-token manager` - getting token of a manager
2. `docker node ls` - list out all nodes/host/instances/servers  
3. `docker node update --role manager node2` - escalating privilege of a docker node
3. `docker service create alpine ping 8.8.8.8` - create a docker swarm
4. `docker service ls` - show running services, under `REPLICAS` is how many is running on the left vs how many you specified it to run on the right
5. `docker service ps <service name>` - - show us the taask/containers for the services
6. `docker service update <service name> --replicas 3` - scaling up the nodes
7. `docker service rm <service name>` - removing a service


# Docker-machine
## Commands
### Useful commands
1. `docker-machine create <node name>` - creates a node  
2. `docker-machine ssh <node name>` - ssh into the container 


# Docker-stack
## Commands
### Useful commands
1. `docker stack ls` - list stack  
2. `docker stack ps <stack name>` - see all tasks in the stack 
2. `docker stack services <stack name>` - see all services in the stack 
2. `docker stack deploy -c example-voting-app-stack.yml <stack name>` - deploying a stack 


# Docker-secret
## Commands
### Useful commands
1. `docker secret create <key name> <file path to password>` - pass in a file into a secret  
    * do note that this require you to store the file locally which is a security concern. we can pass in through a remote API to bypass this
2. `echo "<password>" | docker secret create <key name> -` - pass in the secret through command line 
    * do note that this require you to use bash commands, someone with root access can see the history
2. `docker secret ls` - show all passwords and keys 
2. `docker service create --name psql --secret psql_user --secret psql_pass -e POSTGRES_PASSWORD_FILE=/run/secrets/psql_pass -e POSTGRES_USER_FILE=/run/secrets/psql_user postgres` - creating container and passing in secret
2. `docker service update --secret-rm` - to remove password, however when a secret is removed, the containers are redeployed(immutable design) 


# Technical details
##### docker run
1. Look for image locally
2. Look in remote image repository
3. Download latest version (nginx:latest by default)
4. Create new container based off on the image
5. Customize networking, give a specific virtual IP address in Docker Virtual Network
6. Open ports in `--publish <from>:<to port>` forward port 80 from host, to port 80 in the container
7. Start container by using cmd in a Dockerfile
e.g. `docker container run --publish 80:80 --name webhost -d nginx:1.11 nginx -t`

#### docker network
1. docker are started with their own virtual network `bridge/docker0` by default.
2. containers with the same virtual network can talk to each other without having ports exposed
3. if they are started with port `80:80` then the ethernet interface will start listening to connections at port 80

#### docker with dns
1. docker containers can be added to the same network 
2. they will be referenced through each other using the `container name` regardless of what their IP address is
3. `docker container exec -it my_nginx ping new_nginx`
4. default network bridge does not has DNS service

#### docker swarm init
1. security and automation, creates root certificate for swarm and join tokenes for nodes to join
2. enables swarm api and create raft consensus database, ensure consistency across multiple nodes
# Examples and explanation
#### Files
1. `dockerfile-sample-1` - sample, ordering of docker commands matter. least change code should be at top  
2. `dockerfile-sample-2` - copy .html into container while building  
    * `docker image build -t nginx-with-html .`  
    * `docker container run -d --name nginx -p 80:80 -v $(pwd):/usr/share/nginx/html nginx` - mapping a volume  
3. `dockerfile-assignment-1` - dockerizing a node app and pushing it onto the cloude  
4. `bindmount-sample-1`  
    * `docker container run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve` binding html changes for webapps  
5. `compose-sample-1` - moving from dockerfile to docker-compose  

#### Scenarios
1. `upgrading database` using volumes
    * `docker container run -d --name psql -v psql:/var/lib/postgresql/data -e POSTGRES_PASSWORD=password postgres:12.2`
    * `docker container run -d --name psql2 -v psql:/var/lib/postgresql/data -e POSTGRES_PASSWORD=password postgres:12.3`
2. `docker container run --publish 80:80 nginx` - pull image nginx and run it, forwarding connections from port 80 to port 80 inside container  
    * `--rm` remove container upon exit    
3. `serving a changing html` using bind mount  
    * `docker container run -p 80:4000 -v $(pwd):/site bretfisher/jekyll-serve` binding html changes for webapps
4. `creating a swarm connected by network`    
    * `docker network create --driver overlay mydrupal`
    * `docker service create --name psql --network mydrupal -e POSTGRES_PASSWORD=mypass postgres`
    * `docker service ls` check services
    * `docker service ps psql` check running individual service
    * `docker container logs psql.1` checking logs of psql
    * `docker service create --name drupal --network mydrupal -p 80:80 drupal`
5. `swarm-app-1` - deploying a cluster app  
6. `swarm-stack-1` - automatically create list of services deployed to cluster
7. `secrets-sample-2` - creating secrets and passing secrets into a stack
8. `update the image to a newer version`
    * `docker service update --image myapp:1.2.2 <service name>`
8. `adding env variable and removing a port`
    * `docker service update --env-add NODE_ENV=production --publish-rm 8080`
8. `change number of replicas of two services`
    * `docker service scale web=8 api=6`
9. `scaling web services + rolling updates`
    * `docker service create -p 8080:80 --name web nginx:1.13`
    * `docker service scale web=5` - scaling 
    * `docker service update --image nginx:1.13.6 web` - updating image
    * `docker service update --publish-rm 8080 --publish-add 9090:80 web` - removing and changing a port
    * `docker service update --force web` - rebalancing the tasks/workload in your node
10. `adding healthcheck to a nginx`
    * `docker container run --name p1 -e POSTGRES_HOST_AUTH_METHOD=trust -d postgres` - without healthcheck
    * `docker container run --name p2 -d -e POSTGRES_HOST_AUTH_METHOD=trust --health-cmd="pg_isready -U postgres || exit 1" postgres` - container with healthcheck    
    * `docker service create --name p2 -e POSTGRES_HOST_AUTH_METHOD=trust --health-cmd="pg_isready -U postgres || exit 1" postgres` - service with healthcheck    
    
# Kubernetes
## Commands
1. `kubectl run` - running a pod
2. `kubectl create` - creating resource for CLI/YAML
3. `kubectl apply` - use to update difference
4. `kubectl version` - get version of client/server
5. `kubectl run my-nginx --image nginx` - create and run an image
6. `kubectl get pods` - get list of services running(hidden some)
    * `-w` - watch command, to watch a linux command
7. `kubectl get all` - get all services running including networks etc.
8. `kubectl delete deployment my-nginx` - cleanup
10. `kubectl delete pod/my-apache-5d589d69c7-ps6sj` - deleting a pod
8. `kubectl scale <image name>`
    * `--replicas <count>` - scale image
    
## Logs Command
9. `kubectl logs deployment/my-apache` logs command
    * `--follow`
    * `--tail 1` - return last line only
10. `kubectl logs -l run=my-apache` specifying labels to view all logs of different node at once. this is not a replacement to production loggin, use "Stern tool" for better log tailing
11. `kubectl describe pod/my-apache-5d589d69c7-fkwvv` - similar to inspect command in swarm, describe specific pods

# Technical details
##### Basic System Parts
1. Master Node: 
    * etcd - distributed storage system for key-values (Similar to Swarm RAFT algorithm)
    * API - Talk to cluster and issue order
    * scheduler - controller how and where containers are placed on the nodes on object call pods
    * Controller Manager - look at state of whole cluster and everything running in it, taking order given to it and determine the difference and do what you ask it to do
    * CoreDNS - control network
2. Node:
    * kubelet - kubernetes agent running on nodes
    * kube-proxy - to control the networking
    
#### Kubernetes Container Abstraction
1. Pod - one or more containers running together one one node, basic unit of deployment, containers are always in pods
2. Controller - creating/updating pods and other objects(controller, deployment, replicaset, statefulset, daemonset, job, cronjob)
3. Service - network endpoint to connect to a pod(provide a consistent endpoint similar to DNS)
4. namespace - filtered on the view of the kubectl commandline

#### Kubernetes run command
1. When a run command is issued the following controllers are created
2. Deployment Controller -> Replica Set(use to manage if pod started is correct) -> Pod

#### What happens when a scale is run
1. When we type a scale command, we are updating the deployment specification
2. deployment change replicaset to a set of 2 controllers
3. relpicaset controller decided to change to 2 pods
4. control plane assigns node to pod
5. kubelet agent will be assigned to the pod and be executed on local docker agent

#### Scenarios
1. `scaling replica sets` using volumes
    * `kubectl run my-apache --image httpd`
    * `kubectl scale deploy/my-apache --replicas 2` - scaling to 2 services
