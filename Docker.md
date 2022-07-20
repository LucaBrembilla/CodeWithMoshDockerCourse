# Docker
Based on the course by Mosh
These are the notes that I've taken on [Obsidian](https://obsidian.md/), so they may be badly formatted here.

---

# Introduction
Docker is a platform for building, running and shipping applications consistently. If the applications run on Docker, it runs on all the computers, because it runs in an isolated environment.

## Virtual machines and containers
A **container** is an isolated environment for running applications. A **virtual machine** is an abstraction of a machine (physical hardware).

A hypervisor is a software to virtualize a machine, to host an OS in another one. This is also a way to isolate an application and isolate an environment. However, each VM needs a full-blown OS, they are slow to start and they're resource intensive. The CPU must be divided into the VMs.

Containers give us the same amount of isolation, but they are lightweight and use the OS of the host. So they start quicker and need less hardware resources.

## Docker architecture
Client-server architecture using RESTful API. A container is just a process. All these processes use the same OS's Kernel. We can now run Linux native applications on Windows because it shares the Linux Kernel.

## Development workflow
We just add a docker file to our application to package it into an image. Once we have an image, we tell docker to start a container with that image. 
Once we have the image we can push it in the Docker Hub and so we can put it in any machine which has Docker so it is started as in our computer.
We do not have to maintain long complex release documents which have to be followed.

## Docker in action
Let’s make a simple node application. Create a Dockerfile file with no extension in which we write:
``` dockerfile
FROM node:alpine
COPY . /app
WORKDIR /app
CMD node /app.js
```
to make the image contain node environment and then we run in our directory of this Dockerfile the command `docker build -t hello-docker .`. We have created our image and we can see all of them by running `docker image ls`.
By `docker run hello-world` we can now run our image in all computers with docker. We can publish our image on Docker Hub. 

## Linux Image
We can install an Ubuntu official image to run the container on our computer. You have to run `docker run ubuntu`. This command searches for the image locally and, if it doesn’t find it, searches for it on the Docker Hub, and then pulls it (there is also the command `docker pull` to install an image from Docker Hub).
The package manager of Ubuntu is apt, and with the command `apt` we can use it.

### Linux File System
Just like in Windows, the file system is organized in a tree. We have standard directory, like bin, boot, dev. In linux everything is a file, including devices, sockets, etc. 
With the command `pwd` we can see where we are in our file system. The command `ls` we can see all the files and directories where we are. To see one file per line we run simply `ls 1`. Instead, with `ls -l` we see a long listing, with more details. 
To navigate we use `cd` as in Windows. If we press tab two times while writing this we see the list that starts with that path. You can type the path even with `ls`. 
To gain root permissions on Ubuntu for Windows run `ubuntu config --default-user root` in the Windows’ PowerShell.
Other useful commands for Ubuntu command line are `touch` to create files, `mkdir` to create a directory, `mv` to change the name or move files/directories.
With ctrl+W we cancel an entire word in the command line, like cmd+canc in Windows.
The command `rm` is used to remove things, even directories with `rm -r <dir_name>’.

### Edit and view file
We can use the application nano to edit files. Just run the command `nano <file_name.txt>`.
To see what we had written on the file run `cat <file_dir>`. If the file is too long, it is better to use `more <file_dir>`, or, even better, `less <file_dir>`. To scroll we use the arrow up and down, and to exit we click the q letter.
With `head` and `tail` you see the first and the last lines of a file.

### Standard input and output
The redirection operator is `>`. With `cat file1.txt > file2.txt` we write what there is in file in file2.
The same thing happens with the command `echo`, that when used with the redirection operator write on a file.
The `<` is used for the redirection of the standard input.

`grep` command searches for a string in a file. For example, `grep -i hello file1.txt`, where the flag -i stands for insensitive (remember that Linux is sensitive case).
The flag -r searches for all files in a directory. In Linux we can combine multiple flag, for example `grep -ir hello .`. 

`find` to find all the files in the directory, even hidden ones (or `ls -a` where -a stands for all).
There are various filters to the command find, e.g. -type (f for files, d for directories), -name, -iname.

### Chaining commands
We use semicolons `;` to chain multiple commands. If a command fails the other commands get executed anyway. We can use `&&` to make all the commands fail if one fails. The command `||` use the first command, if that fails it is executed the second one.
We can create a pipe with `|`. For example, `ls /bin | less`. We visualize by the command `less` what is produced by the command `ls /bin`.
We can write multiple commands by using `\` when we want to finish our line and then pressing enter.

### Environment variables
With `printenv` we print all the environment variables. We can use the name of the variable after the command to get only that particular environment variable.
We can also use `echo $PATH`. 
To define an environment variable we can use the command `export <variable_name>=value`. However, this only refers to the current terminal session. To make it persistent we have to write it to a file, named bashrc. So, we have to run `echo <variable_name>=value >> .bashrc`, where the >> is used to append a new line (do not store it in plain text). That environment variable is used from the next terminal session. We can exit and reopen, or reload the session by the command `source .bashrc`.

The command `ps` shows the processes. The bash process is the current terminal session. We can use the command `sleep` and `sleep &` (the second one makes it in the background). `kill <pid>` is used to terminate a process.

To manage users, `useradd` `usermod` and `userdel` are useful.
The passwords of users are stored in the file /etc/shadow encrypted. 
A more interactive way to add an user is using the command `adduser`, which is based on the `useradd` command.

To manage groups, we have `groupadd`, `groupmod` and `groupdel`. The groups’ names are stored in the /etc/group file. Groups are useful because you want a group to have the same permissions. We use `usermod -G <group_name> <user>`. With the -G flag we add a new secondary group. Every user is created with his specific primary group (with only itself). There can be various secondary groups.
To see the groups of a user we use `groups <user>`.

### File permissions
With the command `ls -l` we can see all the files/directories with their permissions. If the name of the file/directory starts with d, then that is a directory. Otherwise, if it starts with -, that is a file. We have then 9 letters that represent the permissions: the 9 letters are divided into 3 groups of 3 letters that represent the permissions of write, read and execute the file (for the directories the last means to open them). The first group refers to the user that owns it (which is the first name after this part), then the second for the group (which appears after the previous name) and the last for all other users.
For example, for the file named "text1.txt" we can have: `-rw-r--r-- 1 root root 12 Jul 13 12:39 file1.txt`.
To change the permissions we use `chmod` with the flags `u` for the user that owns it, `g` for the group and `o` for others. With `chmod u+x deploy.sh` we grant the permissions of execution.

---

# Building Images

## Images and Containers 
What is the difference between an image and a container?
An image contains everything an application need to run:
- A cut-down OS
- Third-party libraries
- Application files
- Environment variables

A container is like a virtual machine, that provides:
- Provides an isolated environment
- Can be stopped and restarted
- It is just a process

Each container is an isolated environment. What happens in it is invisible outside it.

## Sample Web Application
Let’s create a front-end application built with React.
When we have our application, we have to run `npm install` to install all the packages used and `npm start` to run our application.

## Dockerfile Instructions
The first step to dockerize our application is to add a Dockerfile, which contains instructions for building an image. 
Let’s list all the commands that can be written in a Dockerfile: `FROM`, `WORKDIR`, `COPY`, `ADD`, `RUN`, `ENV`, `EXPOSE`, `USER`, `CMD`, `ENTRYPOINT`.

### Choosing the Right Base Image 
You have to start with the right image. You can search for it in https://hub.docker.com/. 
Let’s build our image with the line `FROM node:14.16.0-alpine3.13` in our Dockerfile. Then, create an image of it with `docker build -t react-app .`. Now we can run our container.  We had used alpine, so there is no bash application within it, but we have the original shell, which can be called with `docker run -it react-app sh`.

### Copying Files and Directories
You use `COPY` to put something in a directory of our image. The best practice is to use `COPY <what_to_copy> <where_to_copy>, but you can also use the argument with an array of string (if there are spaces in the name of the files), or the command `ADD` which uncompress zip file and can use http request to get files.

The `WORKDIR` uses (and creates, if it doesn’t exist) the directory specified for all the next instructions.

### Excluding Files and Directories
For our simple application, the length of the context was 222.52MB. That is because we have also copied the `node_modules` directory.
So, we do not want to transfer `node_modules` because that can be obtained by the `package.json` file.
That allows us to diminish the length of the app and to make the building of an image faster.
We can exclude a file or a directory with the creation of a `.dockerignore` file, in which we put e.g. `node_modules/`. However, when running our container, we have to remember to run `npm install` to install all the dependencies.

### Running Commands
The `RUN` command is used to execute any commands that are usually executed for the application. For our application, we use `RUN npm install`, which downloads all the dependencies. 

### Setting Environment Variables
To set an environment variable we use the command `ENV`, e.g. `ENV API_URL=http://api.myapp.com/`. To see this variable, just run `printenv API_URL` or `echo $API_URL` in our container.

### Exposing ports
When we run our application in our container, we want to say the port on which the container will listen. We do it by `EXPOSE 3000` in our Dockerfile.

### Setting the User
By default a container runs the application with the user with the highest privilege (the route user). We add in our Dockerfile the following: `RUN addgroup app && adduser -S -G app app`. Then we also add `USER app` to specify the user that runs the application.

### Defining Entrypoints
When we now run our container of our image without interactive mode and run the node application (`docker run react-app npm start`) we get a permission denied error. In fact, we should move the user on the top of our Dockerfile, as in the following snippet:
```dockerfile
FROM node:14.16.0-alpine3.13
RUN addgroup app && adduser -S -G app app
USER app
WORKDIR /app
COPY . .
RUN npm install
ENV API_URL=http://api.myapp.com/
EXPOSE 3000
```
Now we start our server on the port 3000, but that port is the container, not of the localhost, so we cannot access it in our browser. We can avoid running our container with `npm start` if we add the line `CMD npm start` at the end of the Dockerfile.
Remember: if you have multiple CMD instructions in the Dockerfile, only the last will be executed.
We have 2 form for this instructions:
- Shell form (`CMD npm start`), executed inside a separate shell;
- Execution form (`CMD ["npm", "start"]`), executed (**best practice**).
We also have the command `ENTRYPOINT ["npm", "start"]` that does the same thing. However, CMD can be overwritten (also when starting the container, e.g. with `run react-app echo hello`).

### Speeding up buildings
An image is a collection of layers. Every layer is the result of the execution of a line of our Dockerfile. We can use cache to reuse layers. The problem happens with the line `COPY . .`. If a tiny change is made in our codebase, we have to rebuild all the code, and that’s not efficient at all. And then we have to reinstall all the dependencies. So let’s make as this:
```dockerfile
COPY package*.json .
RUN npm install
COPY . .
```
In this manner, if we do not modify the `package.json` and the `package-lock.json` we do not run the `npm install` command.

**So**, to optimize your builds, you should organize your Dockerfile so that the instructions that don’t change frequently should be on top, and those which change frequently should be on bottom.

### Removing Images
With `docker images` we see all our images. If we have reused the name of images, we have images with no name and tag `none`. These images are called dangling images. To remove them we can use `docker image prune`.
We can get rid of all stop containers with `docker container prune`.
We can also remove an image by its id or name: `docker image rm <image_name>`.

### Tagging images
By default, the tag of an image is "latest". 
One way to tag an image is when building it: `docker build -t react-app:3.1.5 .`. An image can have multiple times. 
If we already have our image we can use the tag instruction: `docker image tag react-app:latest react-app:1`. This adds a new tag, it doesn’t change the existing one.
If we want to delete an image with the tag we use the instruction `docker image rm <image_name>:<image_tag>`.

### Sharing images
To share images with others, register on hub.docker.com. Here, we can create a new repository, with images with tags. To push our image to this repository, we have to login with `docker login`, and then, after changing the name of the image as the name of our repository, we use `docker <image_name>:<tag>`, e.g. `docker push lucabrembilla/react-app:1`.
Let’s change our code, and then try to push it with the version 2 tag. The push should be faster, because some layers didn’t change.

### Saving and Loading Images
We can share images even without the use of Docker Hub. We use `docker image save -o react-app.tar react-app:3`, where the output is a tar file, which is like a zip file on Windows.
To load a tar we use `docker image load -i react-app.tar`.

---

# Working with Containers

## Starting Containers
If we run a container of our image with `docker run react-app` we cannot interact with the container itself, and if we exit it with CTRL+C the process stops.
We can run the container in a detached way, with `docker run -d react-app`. Then we can go on using our terminal.
A container also has a name that we can specify when running it: `docker run -d --name blue-sky react-app`. We can use the name to refer to it.

### Viewing the Logs
To see the output of our application we use `docker logs <container>`. We can use the -f flag to follow the log in real time (CTRL+C to exit), or the flag -t to see the timestamps of the output.

### Publishing ports
To set the port of the container on our local machine we use the -p flag when running the container: `docker run -d -p 80:3000 --name c1 react-app:2` where the first port is the localhost port. 
We can now see the react-app in our browser.

### Executing Commands in Running Containers
With `docker run` we start a new container and run a command, while with `docker exec` we execute a command in a running container. For example, with the container already started named c1, we can use the command `docker exec c1 ls` and get the list of the files of our container. We can also open a shell section with the interaction mode: `docker exec -it c1 sh`.

### Stopping and Starting Containers
To stop a container we use `docker stop <container>`.
To start it we use `docker start <container>`.

### Removing Containers
You cannot remove a running container with `docker rm <container>`. We have to force it with the flag -f or firstly stop it and then remove it. 

### Containers File System
Each container has its file system that is invisible to other containers.

### Persisting Data Using Volumes
A volume is a storage outside of the container, on the host or on the cloud. The command for volume is `docker volume`. We can create one with `docker volume create <name>`. We can then inspect it with `docker volume inspect <name>`. 
Once we’ve created a volume, we can use it when we run our container by the flag -v, e.g. `docker run -d -p 4000:3000 -v app-data:/app/data react-app`. With that we create a volume in the directory `/app/data`. If we do like this, however, the owner of the directory (and the only one who can write on it) is the root user, so we have to create the directory before we create this volume. We can do it by adding to our Dockerfile the command `RUN mkdir data` after the specification of the WORKDIR. With this, the directory is owned by the app user that we have already defined in the Dockerfile (but we have to rebuild our image).
Now we can run `docker run -d -p 4000:3000 -v app-data:/app/data react-app` with the permission to write on data (we can do it firstly running `docker exec -it <container> sh` and then `echo data > data.txt`).
If the container is deleted, the file still exists, because the volume is outside the container.
We can also share volumes between different containers.

### Copying Files between the Host and Containers
There is the copy command to copy files between the host and containers. 
From a container to the host: `docker cp <container>:/app/log.txt .` to copy the file "log.txt" to the current directory. 
From the host to a container: `docker cp secret.txt <container>:/app` to copy the file "secret.txt" of our current directory to the container directory "app".

### Sharing the Source Code with a Container 
For a production machine, when we update our application, we should always build a new image, tagging it properly with its version.
For development, we do not want to build a new image every time we make tiny changes to our code. Neither do we want to manually copy the file on our machine to our container.
Instead, we can create a **mapping**, or a binding, directly on the host and directly on the container, so that we can see the updates in real time. We can do it with the **creation of a volume of our current directory in the container’s app directory**: `docker run -d -p 5000:3000 -v $(pwd):/app react-app`.

---

# Running Multi-containers Apps

### Installing Docker Compose
Docker Compose is built on top of Docker Engine and makes it extremely easy to run applications with multiple containers.
On Windows, it is part of the docker installation, so you do not need to install it separately.

### Cleaning Up Our Workspace
We can get all the id of our images with `docker image ls -q`. The same happens with containers. So we can firstly do `docker container rm -f $(docker container ls -a -q)` and then `docker image rm -f $(docker image ls -q)`.
We can also delete all the images, containers and volume with the UI of Docker Desktop, with the `Clean / Purge Data` button in the troubleshoot section.

### The Sample Web Application
The docker-compose.yml file is used for composing a multi containers application. Once we have this file, we simply run `docker compose up`. This is useful with, for example, an application which is divided into backend and frontend, and you have to run `npm install` once in the backend folder and once in the frontend folder, before starting the applications (they are like two applications).

### JSON and YAML Formats
JSON is a human-readable language for representing data. 
YAML is another language for representing data, and it is easier to read than JSON. In that format, we use indentation to represent hierarchy.
The beginning of the YAML object is represented by `---`. 
An array is represented with the name and the element of the array indented with the minus sign: 
```yaml
tags:
	- software
	- devops
```
Notice how the keys are not between quotation marks, and even the string values aren’t.
Quite oftens, we use YAML files for configuration files and JSON for exchanging data between computers.

### Creating a Compose File
Let’s create the `docker-compose.yml` file. 
Firstly, we set the version of our file. We see it on https://docs.docker.com/compose/compose-file/compose-versioning/. Actually, it's 3.8 (maybe forever). So we use: `version: "3.8"`.
Then, we have the property services to define the block of our application, in this case frontend, backend and database (we can define their name as we want). The only difference between the backend and frontend dockerfile is the port: the backend is on 3001, while the frontend on 3000. In the services we have to specify all the things we usually specify when starting a container, firstly where the specific Dockerfile for the image to be built is or the image to pull which is used by that block.
We then have the port mapping with the ports attribute (host:container).
The environment property to define environment variables. In this specific application, we define `DB_URL: mongodb://db/vidly`, where db is the name of the block for the database.
In addition, we don’t want mongodb to write data to the temporary file system of the container. So, we use volume. We set a volume called vidly and map it to a directory inside the container, in "data/db". 

At the same level of services, we allocate the volume, with the property volumes that contain the property vidly without the value.
The YAML file is the following:
```dockerfile
version: "3.8"

services:
  frontend:
    build: ./frontend
    ports:
      - 3000:3000

  backend: 
    build: ./backend
    ports: 
      - 3001:3001
    environment: 
      DB_URL: mongodb://db/vidly

  db:
    image: mongo:4.0-xenial
    ports:
      - 27017:27017
    volumes:
      - vidly:/data/db

volumes:
  vidly:
```

### Building Images
All the commands we have seen with Docker are also used with docker compose. However, these commands will apply to applications as a whole and will impact multiple containers.
We can build the image of our full-stack application by `docker compose build`. 
Obviously, we get more images. In our example, we get 2 images (*why not 3, the db?*), with the name defined in the `docker-compose.yml` file, preceded by the directory name (vidly).
We can force that docker doesn’t use cached elements with the flag `--no-cache`.

### Starting and Stopping the Application
If the images are ready, we use `docker compose up`, otherwise it will build the images itself. There are a lot of useful flags, for example `--build` to force the rebuilding of the images, `-d` for the detached mode (`docker compose up -d`). 
With `docker compose ps` we can see the containers (or process) running, with port mapping.
We can stop the application with `docker compose down`.

### Docker Networking
When we run our application, Docker compose will automatically create a network and add a container on that network so the containers can talk to each other.
In fact, when we run our application with the detached mode, it is said that a network has been created (vidly_default in our case). We can run `docker network ls` to see all the network machines (every docker installation has 3 default networks, named bridge, host and none). We can see our network which has a bridge driver.
So, this network has 3 containers. Docker comes with an embedded DNS server that contains the name and IP of these containers. Inside these containers, we have a component called the DNS resolver, which talks to the DNS server to find the IP address of the target container. So, each container has an IP address as part of the network. 

### Viewing Logs
With `docker compose logs` we can see the logs of our application. For the logs of a container we use `docker logs <container>`.

### Publishing Changes
Obviously, we don’t want to rebuild an image everytime we change our code. So, we are gonna map the project directory to the up directory of our container. This way, any changes are immediately visible. For this purpose, we are creating new volumes in our YML file. For our backend service, we put the following code that maps our backend repo to the app folder: 
```yml
volumes:
      - ./backend:/app
```
If we now try to run our application, we get the error saying that nodemon was not found. The reason is that in the backend folder we don’t have the node_modules folder, because the dependencies were installed into the container. So, let’s install them by running `npm i` from inside the backend folder.
Do the same for the frontend.

### Migrating the Database
With the package `migrate-mongo`, we can create db migration scripts. In a JS file we have 2 functions, up to upgrade the db and down for downgrading it. To populate the database we can run `migrate-mongo up` on the Windows prompt. Our scripts cannot be executed twice because the database stores the changelogs that are executed. 
In our application, we can see what the computer has to do when we run the migration in the `package. json` file, where there is the script section. Here we have:
```js
 "scripts": {
    "db:up": "migrate-mongo up"
}
```
So, we can also run `npm run db:up`. We have similar concepts in all the development stacks. 
We can force the execution of the migration by adding to the docker file the following lines (in the backend code):
```dockerfile
command: migrate-mongo up && npm start
```
However, starting a database takes several seconds, so we want to wait for it. We use a waiting script. If we google for "docker wait container", we get a list of tools that provide that utility, for example there is `wait-for.sh` script. We then have: `command: ./wait-for db:27017 && migrate-mongo up && npm start`. 
This command is too long, so it is useful to use an entrypoint. So, we create a file called "docker-entrypoint.sh" and we put:
```shell
echo "Waiting for MongoDB to start..."
./wait-for db:27017 

echo "Migrating the database..."
npm run db:up 

echo "Starting the server..."
npm start 
```
All we have to do now is to run it with the command option: `command: ./docker-entrypoint.sh`. 

Now, after removing the containers and the network (`docker compose down`) and the volumes (`docker volume rm vidly_vidly`), we can compose our app and see that we have populated the database.

### Running Tests
There are a bunch of units and integration tests. To run them we can simply access the frontend or backend directory and then run `npm test`. There are people that run tests inside a docker container. However, that’s quite slow, so it is better to do as described above.
To run tests inside docker containers, we have to write a new section for the frontend tests, maybe called web-tests, in our docker-compose.yml file:
```yml
web-tests:
	image: vidly_web
	volumes:
		- ./frontend:/app
	command: npm tests
```
We can do the same for backend testing, with the addition of a section called backend-tests.

---

# Deployment

### Deployment Options
We can deploy our application to a single-host or to a cluster. The problem with the first solution is, obviously, that if our server goes offline, our application won’t be accessible. The same applies if there are a lot of users. 
With clusters, we get high availability and scalability. To run clusters we have to run orchestration tools. The most used is Kubernetes, originally developed by Google, but there is also Docker Swarm, owned by Docker.
However, Kubernetes is out of the scope of this course and it’s too difficult.

### Getting a Virtual Private Server
To deploy our application, we need a Virtual Private Server or VPS. Some options are Digital Ocean, Google Cloud Platform (GCP), Microsoft Azure and AWS. The first is the simplest, and as we go down we get more features but less simple. 

### Installing Docker Machine
Once we have a server, we need to use a tool called Docker Machine to talk to the docker engine on that server, so this way we can execute docker commands in our terminal and commands will be sent to the docker engine on the server. Here is how to install it: https://github.com/docker/machine/releases

### Provisioning a Host
After the installation, we can create a VPS on Digital Ocean with `docker machine --driver digitalocean --digitalocean-access-token $DOTOKEN vidly` (the last being the server name). There are drivers for all the hosts. And remember, on linux the backslash is used to put the code in a new line, while on the Windows’ Powershell you use backtick.So, this command will install the docker engine on the Digital Ocean server, to which we will talk remotely. 

### Connecting to the Host
With `docker-machine ls` we can see all our docker machines. Here we can see if our machine is a cluster, its IP, its driver (in this case Digital Ocean) and the version of Docker Engine running on it.
We can use `docker-machine ssh vidly` to log on the machine and run the security shell. 

### Defining the Production Configuration
For our production environment we don’t want certain features of our development one, e.g. the execution of tests and the mapping of the repository from our system to the container. So, we’re creating a new docker-compose file, named `docker-compose.prod.yml`.
Here we say that the frontend port is mapped on the port 80 of the host (default port for web traffic). We can also delete the volume property from the frontend and the backend section.
We then add a restart property, whose default value is "no". To make our container restart automatically in case of failure or crash, we use the value "unless-stopped". 
The docker-compose.prod.yml is the following:
```yml
version: "3.8"

services:
  frontend:
    build: ./frontend
    ports:
      - 80:3000
    restart: unless-stopped

  backend: 
    build: ./backend
    ports: 
      - 3001:3001
    environment: 
      DB_URL: mongodb://db/vidly
    command: ./docker-entrypoint.sh
    restart: unless-stopped

  db:
    image: mongo:4.0-xenial
    ports:
      - 27017:27017
    volumes:
      - vidly:/data/db
    restart: unless-stopped

volumes:
  vidly:
```

### Reducing the Image Size
The size of our image is quite big. We want to reduce it.
In our frontend directory, we can run the build script for creating optimized assets for production. This is part of React, but other tools have similar concepts.
We need to create a separate Dockerfile for production, because in our original Dockerfile use node and npm install, but if we provide the dependencies that won’t be necessary. Let’s create a `Dockerfile.prod`file. In this file we put:
```dockerfile
# Step 1: Build Stage
FROM node:14.16.0-alpine3.13 AS build-stage
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Step 2: Production 
FROM nginx:1.12-alpine
RUN addgroup app && adduser -S -G app app
USER app
COPY --from=build-stage /app/build /usr/share/nginx/html
EXPOSE 80
ENTRYPOINT [ "nginx", "-g", "daemon off;" ]
```
If we now build the frontend image with `docker build -t vidly_web_op -f Dockerfile.prod .` we get a much smaller image. 
In our Dockerfile we have defined two stages, one for building and one for production. In the production stage we use nginx as a web server and copy what we have built to a particular and standard directory.
In our docker-compose file we have to define that in the production we want to use the Dockerfile just created, so we add in the frontend field, on the build property, the dockerfile sub-property, with value Dockerfile.prod. We also want to use a different port of our container, which is the 80, as defined in the expose of our Dockerfile.
```dockerfile
services:
  frontend:
    build: 
      context: ./frontend
      dockerfile: Dockerfile.prod
    ports:
      - 80:80
    restart: unless-stopped
```
Now we can start our compose app, with `docker compose -f docker-compose.prod.yml build`. 

### Deploying the Application
We can see the environment variables that we have to define with the command `docker-machine env vidly`. We get there what we have to do: we can run `eval $(docker-machine env vidly)` to configure our shell. Now that these environment variables are set, any command that we type in our shell will be sent to the machine shell, e.g. with `docker image ls` we see the images of the remote shell. We can compose our application using `docker-compose -f docker-compose.prod.yml up -d`. We can get a permission error: `missing write access to /app`. That shouldn’t appear with the latest versions of the Docker engine. However, to fix it, we have to add this line of code in our Dockerfile, before the `USER app`: `RUN mkdir /app && chown app:app /app`.
We can use the --build option so that our images are rebuilt.

### Troubleshooting Deployment Issues
Our application is up, so let’s see if we can access it on the browser. We get the IP address of it when we run `docker-machine ls`. We copy it, but nothing’s there. However, if we access the api path of the backend, on port 3001, we get it. 
If we run `docker ps` we can see that our nginx frontend is restarting. Something’s wrong with the frontend container and it keeps crashing and restarting (due to our restart policy). 
If we look at the logs (`docker logs <container>`). We got a *Permission denied* message. That’s because we are running nginx with a non-root user. The use of our user when running nginx is outside the purpose of this course, so we can simply run it with the route user: we have to delete the two lines in our Dockerfile.prod where we defined the user group and run with the user app in the production stage.
Now, we can re-run our applications with `docker-compose -f docker-compose.prod.yml up -d --build`. We can verify that our containers are running with `docker ps`. 
Now, if we access the IP, we get the frontend page, but we are saying that it "Could not fetch the movies!". We need to troubleshoot once more. We can bring up Chrome Devtools and look at the request to our API. We look at the "Network" and click on "XHR", which is XMLHttpRequest. We refresh our page with CTRL+R. The problem is that the request for the movies is sent to the localhost. This is only for development! So, let’s go to our source code, inside /frontend/src/services/api.js, where we have all the code to talk to the api. In there, we have specified to use an environment variable for the url to which require the api, or, if not defined, use "http://localhost:3001/api". So, in our production dockerfile, we have to set the environment variable, called in this case "REACT_APP_API_URL". In the build step (in React environment variables are set there), we add `ENV REACT_APP_API_URL=http://<web_server_IP>:3001/api`. 
So we redeploy our application (`docker-compose -f docker-compose.prod.yml up -d --build`). Now our application is up and running on the Net. 

### Publishing Changes
Early in the course, we have seen we have to properly tag our images, according to the version of them. 
Running `docker ps` on the ssh of our remote machine, we don’t get the version of our images. 
In order to tag images, we can add an image property on our docker-compose.prod.yml, after the build one, for both backend and frontend. In the backend section, we add `image: vidly_backend:1`, and similarly, in the frontend section, we add `image: vidly_frontend:1`. Save the changes and redeploy. If we now run `docker ps`, we see the version of our image. 
If tomorrow we want to build a new image, however, we don’t want to modify the docker-compose file again. So, Continuous Integration and Deployment Tools are used instead. Using them, we can automate our deployment process: we can configure our tools to check out the latest version of our code from our GitHub repository, then it will build our image and tag it with according to the latest build number, or we can have our tools tagging our images based on the latest commit, this way we can easily trace what version we’re running in each environment.

