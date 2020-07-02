# Commands for setting up a local registry

docker container run -d -p 5000:5000 --name registry registry  
docker pull hello-world  
docker tag hello-world 127.0.0.1:5000/hello-world  
docker push 127.0.0.1:5000/hello-world  
docker image rm 127.0.0.1:5000/hello-world hello-world  
docker image pull 127.0.0.1:5000/hello-world  

# Commands for using volumes
docker container run -d -p 5000:5000 --name registry -v /Users/lihengteh/PycharmProjects/docker_playground/registry-sample-1/registry-data:/var/lib/registry registry  

- enable insecure registries in Daemon




- Enable SSL
mkdir -p certs 
openssl req -newkey rsa:4096 -nodes -sha256 -keyout certs/domain.key -x509 -days 365 -out certs/domain.crt

docker run --rm -e COMMON_NAME=127.0.0.1 -e KEY_NAME=registry -v $(pwd)/certs:/certs centurylink/openssl