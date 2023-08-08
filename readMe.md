# **This is a repository i made to learn about docker**

[DESCRIPTION](#description) 
<br>
[USE CASE](#use-case) 
<br>
[THINGS I LEARNED](#things-i-learned) 

## **Description** 

*Docker is a way to containerize applications to build, manage and run easier.* It does this by building a virtual machine. A virtual machine is virtualized computer, it can do anything a computer can do, such as running applications and a OS. 

Quick glosary:

- Docker image or just image: This is a snapshot of what your respository looks like. It contains all files and dependacies.
- Docker containers or just containers: A container is a instanace of the image running on a virtualised virtual machine. Containers do not have their own virtual machines, they have a copy of the virtual machine they're running on.
- Docker host or just host: The host is the machine docker is running on. This can be a computer or server etc.
- Docker hub: A place to store and distribute images, a repository. Also you can find templates to use.
- Dockerfile - A Dockerfile is simply a text-based file with no file extension that contains a script of instructions. Docker uses this script to build a container image.


## **Use Case**

### *Problem*

Imagine we have three different projects, all using different versions of Node. You want to use these projects on one server. Since we can't have different versions of Node installed on one computer, we run into a problem. How can we run these projects on a single server? What is the solution?

### *Solution*

We can do this with if we have three physical machines or one computer that is powerful to run all three virtual machines. This will be quite costly and quite an effort to maintain.

OR

We can use DOCKER. Docker will create a image of the repository and then use that image to make a container running on the host but customised how you want. As long as they share the same OS family you can create as many containers as you'd like. You can use Docker hub to find a lot of template and a place to store and distribute images.

![image](https://github.com/dylan909/Docker/assets/73878448/ed891556-913e-4ba9-8925-15536d7f82eb)

## Things I learned 

There's limited things i can add code in reguards to docker as a lot of the process is though the CLI or app. So i'll add the commands i did along with what the Docker file is.

### Building a Docker image

We're building a docker image and to do this we'll need a docker file. We'll add the dockerfile (which is just docker no extensions) to the root level of the project. 

```
# syntax=docker/dockerfile:1              // This is a special instruction called a "build-time pragma." It's used to specify the syntax version for the Dockerfile.
FROM node:18-alpine              // This is is the starting point. The base image. FROM here meaning we're going to start the base image from this one. And node:18-alpine the version of node with the Alpine Linux distribution as its base.
WORKDIR /app                    // The WORKDIR instruction in a Dockerfile sets the working directory for any subsequent instructions in the Dockerfile. It means that the working directory for the container will be set to /app. 
COPY . .                     // The instruction COPY . . in a Dockerfile is used to copy files from the current directory (on the host machine, where the Docker build context is located) into the working directory of the container. it's format is COPY <src> <dest>.
RUN yarn install --production                 // The RUN instruction in a Dockerfile is used to execute commands during the image build process. This means the command yarn install will be ran but only for the production dependacies
CMD ["node", "src/index.js"]                 // The CMD instruction in a Dockerfile is used to specify the default command that should be executed when a container is started based on the Docker image. This means it will run node in the src/index.js file.
EXPOSE 3000                     // THIS DOES NOT PUBLISH THE PORT 3000. This is purely for documentation purposes, for yourself and for future devs.
```

This is what a Dockerfile could look like. ^


Once you have this file. `cd` to your project root and run the command

> `docker build -t <project name> .`

This builds the image of your repository, it builds it from the dockerfile. If you don't have the base image downloaded docker will automatically download it for you.

- the `-t` is not necessary however it allows you to tag your image with your own naming 👍
-
- The . at the end of the docker build command tells Docker that it should look for the Dockerfile in the current directory

This builds the image of your repository, it builds it from the dockerfile. If you don't have the base image downloaded docker will automatically download it for you.

###  NOW BUILT YOU CAN START IT

> `docker run -dp 127.0.0.1:3000:3000 <project name>`

- the `-d` allows the docker to run in the background
- The `-p` flag creates a port mapping between the host and the container. The -p flag takes a string value in the format of HOST:CONTAINER, where HOST is the address on the host, and CONTAINER is the port on the container.
- You could now visit your project @ http://localhost:3000/

### Listing all running images

> `Docker ps`

This will list all of the current running images. It give's you a couple of columns of data

### Stoping and removing.

if you update the image and  re-build and re-run it. It wouldn't allow you to. You need to delete the past image to do that.

> `docker stop <the-container-id>`
> This will stop the container

> `docker rm <the-container-id>`
> This will delete the container

> `docker rm -f <the-container-id>`
> Alternatively and quickier you can stop and remove the container in one blow. the `-f` here means force.

### How to not lose local data

**Problem**

An issue you might have when running a project locally is the local databases you have not sharing any data between containers and the data not existing after the container has been deleted/stopped.

**Solution**

Docker Mounts


1. **Volume mounts**

Volumes provide the ability to connect specific filesystem paths of the container back to the host machine.

> `docker volume create todo-db`
> This is going to create a volume called todo-db

> `docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=todo-db,target=/etc/todos <project name>`
> Here we're running the same `docker run` command but with a flag `--mount` which you specify the type of mount, src - the place where you're adding the volume from and src the place where you're persisting the data.

2. **Bind Mounts**

A bind mount is another type of mount, which lets you share a directory from the host’s filesystem into the container. When working on an application, you can use a bind mount to mount source code into the container. The container sees the changes you make to the code immediately, as soon as you save a file. This means that you can run processes in the container that watch for filesystem changes and respond to them.

A great use case for bind mounts is to make development containers. The advantage is that the development machine doesn’t need to have all of the build tools and environments installed. With a single docker run command, Docker pulls dependencies and tools.

This is about to be a hefty command you have been warned.

> ```
> docker run -dp 127.0.0.1:3000:3000 \
> -w /app --mount type=bind,src="$(pwd)",target=/app \
> node:18-alpine \
> sh -c "yarn install && yarn run dev"
> ```
>
> The docker run command is the same as previously
> `-w /app` is the directory the where the command will be ran from
> `--mount type=bind,src="$(pwd)",target=/app` bind mount the current directory from the host into the /app directory in the container
> `node:18-alpine` - the image to use
> `sh -c "yarn install && yarn run dev` The shell command runs those two commands. One that will install the dependancies and one that runs the server (preferably on nodemon)
>
> From here you can watch nodemon log all it's info in the console

### Multi-container app's

If we want to upgade our database from sqlite to say MySQL, how do we preceed? Do we add it to the current container or make a seperate one. well... In general, each container should do one thing and do it well.

![image](https://github.com/dylan909/Docker/assets/73878448/fed8070f-41c3-4fed-b613-4b30c47fda5a)

So, how do you allow one container to talk to another? The answer is networking. If you place the two containers on the same network, they can talk to each other.

> `docker network create <mysql-container-id>`
> Firstly, we need to create the network.

>```
>docker run -d \
>--network <mysql-container-id> --network-alias mysql \
>-v todo-mysql-data:/var/lib/mysql \
>-e MYSQL_ROOT_PASSWORD=secret \
>-e MYSQL_DATABASE=todos \
>mysql:8.0
>```
>
> This is running the network and then setting a `--network-alias`, a alias allows other containers in the same Docker network to access the container using the specified name instead of its container ID or hostname.
>
>The line after the alias provides a volume named todo-mysql-data in the above command that is mounted at /var/lib/mysql, which is where MySQL stores its data. This is why it's started with a `-v`.
>
>on the next two lines we're defining enviroment variables, hense `-e`.
>
>The last line defines the version of SQL we want.

> `docker exec -it <mysql-container-id> mysql -u root -p`
> You can check it's working by using this command which will then prompt you to enter your the env password you made

> `SHOW DATABASES;`
> This will then open a MySQL shell and if you enter this command you should see a database with the name of the env var MYSQL_DATABASE you made.

Now that you know MySQL is up and running, you can use it. But, how do you use it? If you run another container on the same network, how do you find the container? Remember that each container has its own IP address.

To understand how to do this we can use an image called `nicolaka/netshoot` this has a lot of tools we can use. 

> `docker run -it --network <project name> nicolaka/netshoot`
> `-it` will create an interactive terminal
> `--network` connects the two of the containers

> `dig mysql`
> Inside the container, you’re going to use the dig command, which is a useful DNS tool. You’re going to look up the IP address for the hostname mysql
> You'll basically get an output which will contain the IP. there will be a row called mysql thanks to us earlier when we set a network alias.

**Running my app with SQL**
The todo app supports the setting of a few environment variables to specify MySQL connection settings. The best ways to set these secrets is through docker swarm or a volume connected to a env file. They are:

MYSQL_HOST - the hostname for the running MySQL server
MYSQL_USER - the username to use for the connection
MYSQL_PASSWORD - the password to use for the connection
MYSQL_DB - the database to use once connected

>```
>docker run -dp 127.0.0.1:3000:3000 \
>-w /app -v "$(pwd):/app" \
>--network <project name> \
>-e MYSQL_HOST=mysql \
>-e MYSQL_USER=root \
>-e MYSQL_PASSWORD=secret \
>-e MYSQL_DB=todos \
>node:18-alpine \
>sh -c "yarn install && yarn run dev"
>```
>This will finally connect your DB container to your client-side container.

> docker exec -it <mysql-container-id> mysql -p todos


And in the mysql shell, run the following:

> select * from todo_items;





### Docker compose

Docker compose provides a simplier way to create multi container apps. You create a docker-compose.yml at root level with instructions on how you want the network to be composed.

*docker-compose.yml*
>```
>services:
>  app:
>    image: node:18-alpine
>    command: sh -c "yarn install && yarn run dev"
>    ports:
>      - 127.0.0.1:3000:3000
>    working_dir: /app
>    volumes:
>      - ./:/app
>    environment:
>      MYSQL_HOST: mysql
>      MYSQL_USER: root
>      MYSQL_PASSWORD: secret
>      MYSQL_DB: todos
>
>  mysql:
>    image: mysql:8.0
>    volumes:
>      - todo-mysql-data:/var/lib/mysql
>    environment:
>      MYSQL_ROOT_PASSWORD: secret
>      MYSQL_DATABASE: todos
>
>volumes:
>  todo-mysql-data:
>```

That's it, we've made the entire network we made last time in one file.

Now we have that we just need to run 
> Docker compose up -d
And we have a network created from one command.
> Here you can use `docker compose logs -f` to see what's going on internally.

Once we are finished with the network we can use 

> Docker compose down





