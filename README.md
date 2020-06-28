# docker_playground
Orchestrating docker containers

# Useful commands
`docker version` - check version and see if everythings working accordingly  
`docker container run --publish 80:80 nginx` - pull image nginx and run it, forwarding connections from port 80 to port 80 inside container  
* `--detach` to run it in background
* `--name <name>` to name the container explicitly.  

`docker container ls` - list out the containers, optionally `-a` flag can be used to list all containers
`docker container stop <container ID>` - stopping docker instance
