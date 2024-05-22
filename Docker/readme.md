# Tech 258 - Docker

## Installing Docker on Windows
### Prerequisites
1) Make sure `Windows Subsystem for Linux` is enabled on your Machine. <br>
To do this:
- Search for `Turn Windows features on or off` on Search bar.
- Scroll down to `Windows Subsystem for Linux` and Tick it if not already ticked. Example: <br>
![](images/enable_windows_subsystem_for_linux.png)
2) Click `OK` and Restart your machine to enable it!

### Steps to Install Docker
1) Download the installer using the download button at the top of this page [here](https://docs.docker.com/desktop/install/windows-install/).
2) Double-click `Docker Desktop Installer.exe` to run the installer.
3) When prompted, use `WSL 2` instead of `Hyper-V` option on the configuration page. (Make sure it's ticked)
4) Follow the instructions on the installation wizard to proceed with the install.
5) When the installation is successful, select `Close` to complete the installation process.
6) Restart your machine to make sure Docker is active and running from now on.
7) Check the docker version using the command:
```
docker --version
```
Example output: <br>
![docker_version.png](images/docker_version.png)

We put our images on Docker Hub so they're publicly available.

`docker pull` - Pull down an image from Docker Hub.

## How does Docker work?

### 1. Docker Client and Daemon

- **Docker Client**: The Docker client is a command-line tool that allows users to interact with Docker. It sends commands to the Docker daemon to perform actions such as building, running, and managing containers.

- **Docker Daemon**: The Docker daemon (also known as `dockerd`) is a background process responsible for managing Docker objects such as images, containers, volumes, and networks. It handles the heavy lifting of building, running, and distributing Docker containers.

### 2. Image Management

- **Image Creation**: Docker images are created using Dockerfiles, which contain instructions for building the image. When you run the `docker build` command, the Docker daemon reads the Dockerfile and builds the image layer by layer.

- **Image Storage**: Docker images are stored in a local repository on the host system. They can also be pushed to and pulled from remote registries such as Docker Hub or private registries. Images are downloaded from registries when they are needed to run a container.

### 3. Docker Compose

- **Docker Compose**: Docker Compose is a tool for defining and running multi-container Docker applications. It allows you to specify the configuration of your application in a YAML file (`docker-compose.yml`) and manage multiple containers as a single application.

### 4. Client-Server Communication

- **REST API**: The Docker client communicates with the Docker daemon using a REST API. This API allows users to perform various operations on Docker objects programmatically.

- **Communication Channels**: The Docker client and daemon can communicate over UNIX sockets (on Linux) or named pipes (on Windows) for local communication. They can also communicate over a network interface, allowing you to connect a Docker client to a remote Docker daemon.
  
![](images/docker_architecture.png)

## Interacting with a container
Firstly we have to create a container from an image. Here's an example command below to do this with the `nginx` image.
```
docker run -d -p 80:80 nginx
```
Breaking the command flags down:
- `-d` - detached mode - run in background
- `-p` - attach ports e.g. 80:80
- `<image_name>` - e.g. nginx

Example output: <br>
![](images/docker_run_nginx.png)

NOTE: We can also stop/start our container. Examples: <br>
![](images/docker_stop.png) <br>
![](images/docker_start.png)


Now that we have the container running we want to interact with it. Run this command to do so: <br>
```
docker exec -it <container_ID> sh
```

Breaking the command flags down:
- `-it` - interactive mode
- `sh` - ssh (shell)

You may run into this error: <br>
![](images/docker_tty_error.png) 

To solve this, run the below command:
```
alias docker = "winpty docker"
```
`alias docker = "winpty docker"`

This container is fresh, meaning we have to install every package from scratch (even sudo!). The steps taken to do this are:
1) Run an update - `apt-get update`
2) Now we can install sudo - `apt-get install sudo`.
3) We can also install nano so we can edit files. - `sudo apt-get install nano`.
4) Now we can edit our Nginx template. First navigate to the right place - `cd /usr/share/nginx/html`
5) Edit the `index.html` file - `sudo nano index.html`

As we are doing this in a running container our changes happen in real time! We don't need to restart the `nginx` service like we did previously.
Changed example of the template: <br>
![](images/localhost_updated_nginx_example.png)

## Creating our own image
I have created my own `index.html` file on my local machine so to copy that into the running container, run the following command: 
```
docker cp /path/to/index.html <container_ID>:/usr/share/nginx/html/index.html
```
Example output: <br>
![](images/docker_cp.png)

Now that we want to capture our running container's current state as an image. Run the following command:
```
docker commit <container_ID>
```
Example output: <br>
![](images/docker_commit.png)

We can now see this in our `images` list. Run the following command:
```
docker images -a
```
The `-a` flag shows all images. Example output: <br>
![](images/docker_images_all.png)

As you can see, our image doesnt have a `repository` name or `tag`. Let's change this with the following command:
```
docker tag <image_ID> <repository_name>:<tag_name>
```
Example output when checking image list again: <br>
![](images/docker_images_all_2.png)

Now that we have our images ready to go we have to make a DockerHub repo.
1) Go to DockerHub > Repositories > Create repository
2) Enter the required fields and make sure it is `public` so others can use it!
Example repo: <br>
![dockerhub_repo.png](images/dockerhub_repo.png)

Now that the repo is created we can push our image to DockerHub! Run the following commands:
```
docker tag <local_repository_name> <dockerhub_username>/<dockerhub_repository_name>:<tag_name>
docker push <dockerhub_username>/<dockerhub_repository_name>:<tag_name>
```

This creates a tag for our image on the DockerHub repo and pushes it!
Example on DockerHub: <br>
![dockerhub_repo_with_pushed_image.png](images/dockerhub_repo_with_pushed_image.png)

`docker logs <container_ID>`
`docker rm <container_ID>` -> if running you'll get an error. stop the container before removing/force remove
`docker rm <container_ID> -f`

## Docker Disadvantages
- **Security** - Easy to SSH in with the `docker exec` command. This means we have to deploy the container within our personal VPC to secure it.

## Building a Docker image from Dockerfile
1) Create a sample `index.html` file
2) create Dockerfile <br>
**NOTES**:
- Dockerfile should have capitalised D and no extension.
- Keywords in Dockerfile are always in capitals
3) Build an image with this tag, using the Dockerfile in the corresponding directory (".") Example command: 
```
docker build -t shafiquemahen/nginxtech258 .
```
**EXTRA NOTES**: <br>
- If we make a change to the running container, we would have to commit those changes with the `docker commit` command. Then we would have to rebuild the image with a new tag e.g. `docker build -t shafiquemahen/nginxtech258:v2` then we can push those new changes to DockerHub.
- We can only have one Dockerfile per folder. If we wanted another Dockerfile, we would have to create another directory and make it there.

We can remove images too. Example command: 
```
docker rmi <image_ID> -f
```


