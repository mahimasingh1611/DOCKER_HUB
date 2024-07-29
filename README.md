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



