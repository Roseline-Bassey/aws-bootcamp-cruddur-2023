# Week 1 — App Containerization

There are various ways to run applications or software, including deploying them on a physical machine with an operating system installed, using virtual machines or cloud-based IaaS instances, opting for PaaS offerings in a Public Cloud, or utilizing containers. The choice of approach depends on individual or company objectives. In week one, we are going to containerise our applications, push to DockerHub and run our container in an EC2 instance.  

However, before I went on with my task, there were few things we needed to put in place. Following the Livestream, I was able to:
 - implement a Backend endpoint
- implement a Frontend UI
- document a new API endpoint in OpenAPI


![Screenshot 2023-03-03 115051](https://user-images.githubusercontent.com/47522955/222762466-4310f18e-3d70-451e-8705-78af04d15dab.png)


Before diving into this week’s homework It’ll help that I talk a bit about containerization and [Docker](https://www.docker.com).

## Containerization
Containerization is a process of packaging code along with the libraries and dependencies so that the application can run quickly, seamlessly and reliably from one environment to another. There are several container management software but the popular ones are [Podman](https://podman.io/), [Containard](https://containerd.io/), [Buildkit](https://docs.docker.com/build/), [LXD](https://linuxcontainers.org/lxd/introduction/), and [Docker](https://www.docker.com).  For this week’s assignment, I am going to use Docker in completing my tasks.

## Docker
Docker is a software tool used for building, deploying, and managing containers. I will be using Docker to build and run both our Frontend and Backend images and then push these Docker images to a public repository on DockerHub. [Dockerhub](https://hub.docker.com/) is a container registry for hosting Docker images. 



## Writing a Dockerfile for the Frontend and Backend Applications

According to [Docker’s documentation](https://docs.docker.com/engine/reference/builder/), “ A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.” With this understanding, I created a `Dockerfile` inside of the `backend-flask` and `frontend-react-js` folders. These files would be used by Docker to build our images automatically.

The images below show the dockerfile(s) for both applications.



![Screenshot 2023-03-03 165150](https://user-images.githubusercontent.com/47522955/222765838-5de6dcd8-8ad9-477a-816f-13b14a347dbc.png)
Backend Dockerfile



![Screenshot 2023-03-03 165128](https://user-images.githubusercontent.com/47522955/222765748-a50650aa-707b-48b2-9c43-d3a17ddc070b.png)
Frontend Dockerfile

## Docker Compose
Docker compose, a container orchestration tool for running multiple containers on a single host machine. In order to instruct Docker on how and what it should do with our applications, I am going to create a docker-compose.yml file containing instructions needed for running our application. The `docker compose.yml` file makes it possible to link multiple containers so that containers can communicate with each other.

```yaml
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    healthcheck:
      test: ["CMD", "curl", "f","http://localhost:4567/api/activities/home"]
      interval: 30s
      timeout: 10s
      retries: 3
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js
  dynamodb-local:
    # https://stackoverflow.com/questions/67533058/persist-local-dynamodb-data-in-volumes-lack-permission-unable-to-open-databa
    # We needed to add user:root to get this working.
    user: root
    command: "-jar DynamoDBLocal.jar -sharedDb -dbPath ./data"
    image: "amazon/dynamodb-local:latest"
    container_name: dynamodb-local
    ports:
      - "8000:8000"
    volumes:
      - "./docker/dynamodb:/home/dynamodblocal/data"
    working_dir: /home/dynamodblocal    
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
volumes:
  db:
    driver: local
```
Our `docker-compose.yml` file consists of four `services`, `backend-flask`, at line 2, `frontend-react-js`, at line 17, `dynamodb-local`, at line 25, and `postgres`, at line 38 respectively. These services are isolated under `services`. However, because the frontend-react-js exposes port `3000` at line 22, users can access the frontend service container via port 3000. The `dynamodb-local` and `postgres` are services hosted on DockerHub and the `docker-compose.yml` file has the ability to pull and run these container images in our application.

## Implement a healthcheck in the V3 Docker compose file
Healthcheck instruction tells Docker how to test a container to check that it is still working. This can detect cases such as a web server that is stuck in an infinite loop and unable to handle new connections, even though the server process is still running.

```yaml
healthcheck:
      test: ["CMD", "curl", "f","http://localhost:4567/api/activities/home"]
      interval: 30s
      timeout: 10s
      retries: 3
 ```
 
 ## Use multi-stage building for a Dockerfile build
The purpose of multistage build is to reduce the size of the final image by only copying the necessary files from the build stage into the production stage or final stage. After implementing multi-stage build in the backend, I observed a slight reduction in the size of the original image. However, I was uncertain if I had done everything correctly, so I sought help from "bootcampers" in the Discord server. One of the responses I received suggested that the decrease in size was likely because it was a backend image, and I might have achieved better results by implementing multi-stage build for the frontend. I have included the multistage file below, and I welcome any suggestions you may have for a better solution in the comments.

```yaml
# Stage 1: Build stage 


FROM python:3.10-slim-buster AS build 
WORKDIR /backend-flask 
COPY requirements.txt requirements.txt 
RUN pip3 install --user --no-cache-dir -r requirements.txt 
COPY . .


 # Stage 2: Production-ready stage 
FROM python:3.10-slim-buster AS final
WORKDIR /backend-flask 
COPY –from=build /root/.local /usr/local 
COPY . . ENV FLASK_ENV=production 
EXPOSE ${PORT} 
CMD [ "python3", "-m", "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

## Push and tag an image to DockerHub
Once I finished building the Docker images, I uploaded them to DockerHub. To push an image to DockerHub, you must first create an account and a repository where the image will be hosted. I logged into my Docker account from the terminal using the command `docker login` and provided my Docker ID. Then, I entered the command `docker build -t <hub-user>/<repo-name>[:<tag>]` to tag the image and specify the repository. Finally, I used the command `docker push <hub-user>/<repo-name>:<tag>` to push the images to DockerHub.


![Screenshot 2023-03-02 204036](https://user-images.githubusercontent.com/47522955/222779568-658aec8a-cc88-4d68-83a7-a9e44c4408e0.png)

## Running Docker Containers on Amazon EC2
Amazon EC2 allows you to create virtual machines, or instances, that run on the AWS Cloud
To launch an instance on AWS: 

Go to EC2 Dashboard
Click on `Launch Instance`
Give the instance a name the instance, in my case I named my instance `AWS Cloud Project bootcamp-Frontend`
Select free tier eligible Linux2 AMI instance 
Choose the t2.micro instance type
Added a key pair; Key pair to securely connect to your instance. Ensure that you have access to the selected key pair before you launch the instance.

Under Network Settings
I created a new security group and allow traffic from SSH, and HTTP from `Anywhere 0.0.0.0/0` 
Left the storage volume at default `1 volume(s) - 8 GiB`
Click on `Launch Instance`
To connect to our running instance, click on the `connect` button on the top right to connect to our instance. To connect to the instance via an SSH Client, I used PuTTY, a stand alone SSH client. 


I ran the commands:
``` sh
// to update packages
sudo yum update -y 

//to install docker
sudo amazon-linux-extras install docker -y 

//to start Docker
sudo service docker start 

//to add the ec2-docker user to the group
sudo usermod -a -G docker ec2-user

// I needed to logout to take affect
Logout

// login again
ssh -i "ec2-docker.pem" ec2-user@ec2-54-90-143-164.compute-1.amazonaws.com

// check the docker version
docker --version

// pull the image
docker pull roselinebb/aws_cloud_bootcamp_repo:latest

// list the images
docker images

// run the conatiner
docker run -d -p 80:3000 --name frontend roselinebb/aws_cloud_bootcamp_repo:latest

// check the running container
docker ps

// exec into docker container
docker exec -it frontend /bin/sh
```

I can now access my frontend instance on my web browser via the public DNS address.




![Screenshot 2023-03-02 203703](https://user-images.githubusercontent.com/47522955/222780887-6e9cf3c5-3bca-416d-bf0f-a880a5c1d0cc.png)
