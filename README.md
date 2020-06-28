# docker_playground
Orchestrating docker containers

# Commands
### Useful commands
1. `docker version` - check version and see if everythings working accordingly  
2. `docker container run --publish 80:80 nginx` - pull image nginx and run it, forwarding connections from port 80 to port 80 inside container  
    * `--publish` or `-p`
    * `--detach` or `-d` to run it in background
    * `--name <name>` to name the container explicitly.  
    * `-e PATH=XXX` for environment variables  
    * `--network <network name>` - connects container to the said network
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
