# DOCKER_HUB
All the docker notes

Share Images and Containers
How to share the image?
1. We can share the Dockerfile
2. share the image, no need to build the image

Two main places where we can push the images:docker hub and private Registry
We can push the image into docker hub

If the docker hub is public we don't need to login for pull operation
docker pull repository_name/image_name:tag

If we pull the image from dockerhub it will fetch the latest image but if the image is available locally it won't check if it is updated one or not

# types of data
1. Application data(whatever the image holding data is application data plus environment). It is fixed
2. Temporary App data- Fetched/Produced in running container, Stored in memory temporary file, dynamic and changing but clear regularly, read+write temporary. It is stored in Containers
3.Permanent App Data: Fetched/Produced in running container, Stored in files or in a database, must not be lost if containers stops/restart. Read +write permanent stored with containers and Volume.

Problem: We want to keep the data even after the container is deleted or removed

Introduction of Volume
Volumes are folder on your host machine which you Docker aware of and which are then mapped to folders inside of Docker containers

Volume persist if a container shut down. If a container restart or start mount a volume any data inside of that volume is available in the container.
A container can write data into a volume and read data

# There are 2 types of volume:
Anonymous Volume: if we delete or stop the volume the data will be removed
Named Volume(Managed by dockers): docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback feedback-node:volumes

In named volumes data will always be there and volume will not be deleted even if we remove the container
Bind Mounts(Managed by you): You define a path/folder on your host machine
Great for persistent, editable(by us) data
if any chnages reflected in host then the container will also show the change bcz we are using bind mount

# docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback -v "/Users/(full path pasted her)" feedback-node:volumes

# docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback -v "/Users/(full path pasted her)" -v /app/node-feedback feedback-node:volumes

# Volumes:
docker run -v /app/data ----> anonymous volume

docker run -v data:/app/data ----> named volume

docker run -v /path/to/code:/app/code ----> bind mounts

# Anonymous volumes: 
created specifically for a single container
survives container shutdown/restart unless --rm is used
can not be shared across containers
it is useful when we want to lock the data from the dockerfile and saved from the overwritten
since it's anonymous, it can't be re-used(even on same image)

# Named:
not tied to any specific container
survive container shutdown/restart - removal via Docker CLI
can be shared across containers
cant be re-used for some container(across restarts)


# Bind Mounts:
location on host file system, not tied to any specific container
survives container shutdown/restart-removal on host fs
can be shared across containers
can be re-used for same container(across restarts)

# VOLUME COMMANDS
---> docker volume create feedback-files
to create the volume

----> docker volume ls
to check the list of volume

---> docker volume inspect feedback
to inspect the volume

 --->to delete all multiple unused volume
docker volume prune
 
# ENV and ARGS

DEFAULT_PORT = 80

ENV PORT $DEFAULT_PORT

EXPOSE $PORT

# DOCKER-COMPOSE

When we make multiple containers for our purpose. We need to manage all the container by our own. We need to build, run and delete which is a lot of work for multiple containers.

We can use docker compose for make our work easy to build, delete and run the command in one single step. Docker compose manages multiple container easily. It can creates multiple containers in one go and tear everything down in one go too.

Docker compose is not suited for managing multiple containers on different host.

version: the version key specifies the version of the docker compose file format being used. Each version supports different features and syntax, so it's important to use correct version that aligns with your docker setup and desired functionality


# syntax----

version:"3.8"
services:
	mongodb:
	    image:"mongo"
	    volumes:
		-data:/data/db
	    container_name : mongodb
	    environment:
		MONGO_INITDB_ROOT_USERNAME: max
		MONGO_INITDB_ROOT_PASSWORD: secret
	    	-MONGO_INITDB_ROOT_USERNAME: max(we can also write like this)
	    env_file:
		- ./env/mongo.env



We don't need to add the network as all the container will be created in the same network if they are in docker compose file

How do we start services with docker compose?

# docker-compose up(to start all the services)
# docker-compose down(to delete all the services) -- it will not delete volume
# docker-compose down -v (it will delete the volume)

backend:
  build: ./backend
  build:
    context: ./backend
    Dockerfile: dockerfile-dev(name of the docker file)
  args:
    some-arg:1
  ports:
    - '80:80'
  volumes:
    - logs: /app/logs
    - ./backend:/app
    - /app/node_modules
  env_files:
    - ./env/backend.env

  depends_on:
	-mongodb


frontend:
  build: ./frontend
  ports:
    - '3000:3000'
  volumes:
    - ./frontend/src:/app/src
  stdin_open: true
  tty: true

  depends_on:
    - backend

volume:
	data(named volume)
	logs
 
# Networks

Networks:

How we connect multiple container to each other and how you let them talk to each other. How you could connect an application running in a container to your local host machine.

Case 1. if the container running application wants to communicate with the outside container or wants to talk to API 

Case 2: if we want to communicate host machine database(service running on our host mahine)

Case 3: container to container communication

Solution case1: if we wanna communicate the container to outside API, in node js we are using axios to get the API. By using the postman app We can send the request and receive the data.
Same way we are using locally we can communicate outside the container with no special setup.

But if wanna communicate database that is present in our host machine we can not connect it.

Solution case 2:
We need to add some code that is understood by docker only
'mongodb://host.docker.internal:27017/favourite'

host.docker.internal this will be understood by docker and translate this as an IP address of a host machine and  connect to our machine. We can use any service by using this code. For example we are using mongodb in this but we can also use http request and connect to our web appilication that is running in our local machine.


Solution case 3:
If we wanna communicate to container to container then we can use IP address of container and we can put 
mongodb://IP ADDRESS:27017/favourite. the container will communicate to the brand new mongo db not the mongodb that s present on your host machine.

Network commands:
# docker network create favourites-net (create network)
# docker network ls (list of all network)
# docker run -d --name mangodb --network favourites-net mongo
If the containers are in the same network they can talk to each other easily. We do not need to expose the post as port are only be used when we need to connect something outside the container or from our local host

How can containers communicate with each other if they are in the same network?
We can use the container name as addresses











