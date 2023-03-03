# Week 1 — App Containerization

There are various ways to run applications or software, including deploying them on a physical machine with an operating system installed, using virtual machines or cloud-based IaaS instances, opting for PaaS offerings in a Public Cloud, or utilizing containers. The choice of approach depends on individual or company’s objectives. In week one, we are going to containerizing our applications, push to DockerHub and run our container in an EC2 instance.  

However, before I went on with my task, there were few things we needed to put in place. Following the Livestream, I was able to:
 - implement a Backend endpoint
- implement a Frontend UI
- document a new API endpoint in OpenAPI


![Screenshot 2023-03-03 115051](https://user-images.githubusercontent.com/47522955/222762466-4310f18e-3d70-451e-8705-78af04d15dab.png)


Before diving into this week’s homework It’ll help that I talk a bit about containerization and [Docker](https://www.docker.com).

## Containerisation
Containerisation is a process of packaging code along with the libraries and dependencies so that the application can run quickly, seamlessly and reliably from one environment to another. There are several container management software but the popular ones are [Podman](https://podman.io/), [Containard](https://containerd.io/), [Buildkit](https://docs.docker.com/build/), [LXD](https://linuxcontainers.org/lxd/introduction/), and [Docker](https://www.docker.com).  For this week’s assignment, I am going to use Docker in completing my tasks.

## Docker
Docker is a software tool used for building, deploying, and managing containers. I will be using Docker to build and run both our Frontend and Backend images and then push these Docker images to a public repository on DockerHub. [Dockerhub](https://hub.docker.com/) is a container registry for hosting Docker images. 
frontend and Backend


## Writing a Dockerfile for the Frontend and Backend Applications

According to [Docker’s documentation](https://docs.docker.com/engine/reference/builder/), “ A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image.” With this understanding, I created a `Dockerfile` inside of the `backend-flask` and `frontend-react-js` folders. These files would be used by Docker to build our images automatically.

The images below shows the dockerfiles for both applications.



![Screenshot 2023-03-03 165150](https://user-images.githubusercontent.com/47522955/222765838-5de6dcd8-8ad9-477a-816f-13b14a347dbc.png)
Backend Dockerfile



![Screenshot 2023-03-03 165128](https://user-images.githubusercontent.com/47522955/222765748-a50650aa-707b-48b2-9c43-d3a17ddc070b.png)
Frontend Dockerfile



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
