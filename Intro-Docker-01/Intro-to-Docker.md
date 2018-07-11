# Intro to Docker
Docker is an open platform to develop, ship, and run applications, more commonly known as a container manager. What’s a container you ask? A container is an encapsulated environment, which runs on top of a very shallow level of abstractions, providing a virtual machine like isolation for running processes. Now if you are just started with Docker, it’s important we define the vocabulary that you’ll need to learn as well as a number of utilities you need to master to become proficient with Docker. 
- - - -
Let’s start with the terms. In a nutshell Docker uses images to specify what the container infrastructure should look like for you to run your apps in it.

##### Layer 
a set of read-only files to provision the system. Think of a layer as a read only snapshot of the filesystem.

##### Image
Its a read-only layer that is the base of your container. It can have a parent image to abstract away the more basic filesystem snapshot. So a Java image would inherit from a linux image with the preinstalled utilities. A tomcat image will have a Java image as the parent because it depends on Java to run Tomcat.
##### Container 
Its a runnable instance of the image, basically it is a process isolated by docker that runs on top of the filesystem that an image provides.

##### Registry / Hub
It is the central place where all publicly published images live. You can search it, upload your images there and when you pull a docker image, it comes the repository/hub.

##### Docker machine
DM is a VM within which you can run Docker containers. On Linux you can run docker containers natively, but on OSX and Windows you need a layer of abstraction. A docker machine will spin a very lightweight virtual machine that integrates with the docker command line utilities really well.
`docker-machine start default`
`docker-machine env default`
`eval "$(docker-machine env default)"`
##### Docker compose 
DC is a utility to run multiple containers as a system of containers. It will take care of making them aware of each other and ensure they’re properly connected to each other. This means you can run your application in one container and your database in a different container, and your analytics application in a different container, and so on. This is the ultimate isolation and it means that your applications are independent and are run in development in a very similar way to how the system might work in production.
**Docker compose allows you to specify a relationship between several containers in a yml file, and then run all of these containers at once.** 
```yaml
version: "2"
services: 
  web:
    container_name: "web"
    image: java:8 # image name
    command: java -jar /app/app.jar # command to run
    ports: # map ports to the host
      - "4567:4567"
    volumes: # map filesystem to the host
      - ./myapp.jar:/app/app.jar
  mongo: # container name
    image: mongo # image name
```

#### Docker Commands
Download an image, and all its parents, from the registry:
`docker pull image_name`

Run a shell command inside a freshly created and started container:
`docker run -ti --name container_name image_name /command`

Start and stop a container, duh!
`docker start container_name`
`docker stop container_name`

Run a command inside a container from the image and remove the container when command is done.
`docker run --rm -ti image_name /command`

Run Tomcat on Java 8 with a custom javaagent attached. This command illustrates mapping the volumes on your host system into the docker container filesystem, see the -v flag part.
`docker run -it --rm -p 8080:8080 -v /host/path/to/your/agent/agent.jar:/agent.jar -e JAVA_OPTS="-javaagent:/agent.jar" tomcat:8.0.29-jre8`

Run a shell command in the container:
`docker exec -ti container_name "cmd" `

To show and follow the log output of the container execute:
`docker logs -ft container_name`

When finishing a day, you can easily kill all running docker containers:
`docker kill $(docker ps -q)`

Delete the dangling docker images, the ones that are not tagged properly and are hanging around usually as the result of the intermediate container creation:
`docker rmi $(docker images -q -f dangling=true)`

Remove all stopped containers, this will actually try to remove all the containers, but will fail to do so with the running ones, so only stopped containers will be gone after that.
`docker rm $(docker ps -a -q)`

#🐳Docker/cheatSheet