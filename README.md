# docker_playground
Orchestrating docker containers

# Useful commands
1. `docker version` - check version and see if everythings working accordingly  
2. `docker container run --publish 80:80 nginx` - pull image nginx and run it, forwarding connections from port 80 to port 80 inside container  
    * `--detach` to run it in background
    * `--name <name>` to name the container explicitly.  
3. `docker container ls` - list out running containers
    * `-a` can be used to list all containers available
4. `docker container stop <container ID>` - stopping docker instance
5. `docker container logs <name>` - list logs of the container
6. `docker container top <name>` - process running inside the container
7. `docker container --help` - list all commands
8. `docker container rm <name> <name>...` - remove the containers, can take in multiple values. running containers will need to be stopped first
    * `-f` - force remove of container even if it's running
    
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
