# **This is a repository i made to lean about docker**

[DESCRIPTION](https://github.com/dylan909/Docker/edit/main/readMe.md#description) 
[USE CASE](https://github.com/dylan909/Docker/edit/main/readMe.md#use-case) 
[THINGS I LEARNED](https://github.com/dylan909/Docker/edit/main/readMe.md#things-i-learned) 

## **Description** <a href="#description">#</a>

*Docker is a way to containerize applications to build, manage and run easier.* It does this by building a virtual machine. A virtual machine is virtualized computer, it can do anything a computer can do, such as running applications and a OS. 

Quick glosary:

- Docker image or just image: This is a snapshot of what your respository looks like. It contains all files and dependacies.
- Docker containers or just containers: A container is a instanace of the image running on a virtualised virtual machine. Containers do not have their own virtual machines, they have a copy of the virtual machine they're running on.
- Docker host or just host: The host is the machine docker is running on. This can be a computer or server etc.
- Docker hub: A place to store and distribute images, a repository. Also you can find templates to use.


## **Use Case** <a href="#use-case">#</a>

### *Problem*

Imagine we have three different projects, all using different versions of Node. You want to use these projects on one server. Since we can't have different versions of Node installed on one computer, we run into a problem. How can we run these projects on a single server? What is the solution?

### *Solution*

We can do this with if we have three physical machines or one computer that is powerful to run all three virtual machines. This will be quite costly and quite an effort to maintain.

OR

We can use DOCKER. Docker will create a image of the repository and then use that image to make a container running on the host but customised how you want. As long as they share the same OS family you can create as many containers as you'd like. You can use Docker hub to find a lot of template and a place to store and distribute images.

![image](https://github.com/dylan909/Docker/assets/73878448/ed891556-913e-4ba9-8925-15536d7f82eb)

## Things I learned <a href="#things-i-learned">#</a>
