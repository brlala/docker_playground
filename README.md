# docker_playground
Orchestrating docker containers

# Useful commands
1. `docker version` - check version and see if everythings working accordingly  
2. `docker container run --publish 80:80 nginx` - pull image nginx and run it, forwarding connections from port 80 to port 80 inside container  
  * `--detach` to run it in background
  * `--name <name>` to name the container explicitly.  
3. `docker container ls` - list out the containers, optionally `-a` flag can be used to list all containers
4. `docker container stop <container ID>` - stopping docker instance
