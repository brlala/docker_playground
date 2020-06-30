# docker_playground
Orchestrating docker containers

# Commands
### Useful commands
1. `docker version` - check version and see if everythings working accordingly  
2. `docker container run --publish 80:80 nginx` - pull image nginx and run it, forwarding connections from port 80 to port 80 inside container  
    * `--rm` remove container upon exit
    * `-it` run commands, commands should be at last like `.... bash`
    * `--detach` or `-d` to run it in background
    * `--name <name>` to name the container explicitly.  
    * `--publish` or `-p` to expose ports
    * `-e PATH=XXX` for environment variables  
    * `--network <network name>` - connects container to the said network
    * `--network-alias <dns name>` - dns name for lookup
e.g. `docker container run -d --name db -p 3306:3306  -e MYSQL_RANDOM_ROOT_PASSWORD=yes mysql`  
3. `docker container ls` - list out running containers
    * `-a` can be used to list all containers available
4. `docker container stop <container ID>` - stopping docker instance
5. `docker container start -ai ubuntu` - starting existing container
5. `docker container logs <name>` - list logs of the container
6. `docker container top <name>` - process running inside the container
7. `docker container --help` - list all commands
8. `docker container rm <name> <name>...` - remove the containers, can take in multiple values. running containers will need to be stopped first
    * `-f` - force remove of container even if it's running
    
### Monitoring commands
9. `docker run -it --rm --privileged --pid=host justincormack/nsenter1` - connecting to docker VM [source](https://github.com/justincormack/nsenter1)
10. `ps aux` - list all processes
    * `| grep <name>` can be used to filter process names
11. `docker container inspect <name>` - to return the configuration of the container
12. `docker container stats` - return streaming lives tats of all containers

### Getting Shell inside containers
13. `docker container run -it` - start new container interactively+psuedo TTY
14. `docker container exec -it` - run additional command in existing container

### Networking Commands
`docker container port <name>` - see ports opened on container  
`docker container inspect --format '{{.NetworkSettings.IPAddress}}' webhost` - see host IP of docker container  
`docker network ls` - show all networks created  
`docker network inspect <network name>` - shows network related configuration like containers connected to the network, IP and gateway of the network  
`docker network create <network name>` - create a new network  
`docker network connect <network name> <name>` - connect network to container  
`docker network disconnect <network name> <name>` - disconnect network to container  

### Image Commands
`docker image inspect <name>` - contains metadata like ports exposed, environment variables, command to run when an image is run
`docker image tag <source_image> <new_image_tag>` - will create a new tag from the source image
`docker image push <image_tag>` - push to dockerhub

### Authentication Command
`docker login` - login
`docker logout` - logout
    
# Dockerfile
`FROM` all images must have a from normally from a minimal linux distribution
`WORKDIR` change working directory, preferred over `RUN cd /path`
`ENV` optional environment variable that is used for container building and running container
`RUN` execute shell commands
`EXPOSE` ports that are being exposed
`CMD` command that will be run everytime we restart the container or everytime a container starts
`COPY` copy from local our build machine into containers

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

# Examples and explanation
`dockerfile-sample-1` - sample, ordering of docker commands matter. least change code should be at top  
`dockerfile-sample-2` - copy .html into container while building  
    * `docker image build -t nginx-with-html .`
`dockerfile-assignment-1` - dockerizing a node app and pushing it onto the cloude