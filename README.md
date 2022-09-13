# README

My note for [Docker & Kubernetes: The Practical Guide](https://www.udemy.com/course/docker-kubernetes-the-practical-guide/) course

# 1. Getting Start

- Sample `Dockerfile`

```dockerfile
FROM node

WORKDIR /app

# a little optimization install  before copy entire code
# to be cache when source code is changed
COPY package.json /app

RUN npm install

# COPY <host> <container>
# in container we have workdir = /app
# thus, /app = ./
# COPY . ./
COPY . /app

# declare argument in dockerfile
ARG DEFAULT_PORT=80

# declare environment variable
# ENV PORT 80

# use argument in docker file
ENV PORT $DEFAULT_PORT

# docker listened port
# EXPOSE 80
EXPOSE $PORT

# anonymous volume to handle persistent data in container
# this will be override by volume specified at  docker run
VOLUME ["/app/feedback"]

# run execute when docker image is built
# CMD execute when docker container is started
CMD ["node", "server.js"]

```

- Note that: every instruction in dockerfile will add one more layer to the image.

- when the above instruction change. cache will not be work and docker will re-build the new layer for the child instruction inseand of cache.

- Thus, apply the frequently change instruction at lower line to use cache as much as possible when `docker build` the image

# 2. Docker Images & Container

- `docker build .` : build docker image
- `docker run -p <host-port>:<container-port> (-d) <image-key>`
  `docker run -p 3000:80 -d --rm 5144f068c3ddcc2e11a7a7a12672360d041a8fe537a2ddf2a0aba282996e5acd`
  - `-p` : publish
  - `3000` : match with http://localhost:3000/
  - `80` : match `EXPOSE` in Dockerfile
  - `-d` : detach mode. (default is attach mode)
  - `--rm` : automatically remove docker container after stop
- `docker ps` (-a): list all running containers (-a include the stopped ones)
- `docker run -it <image-key>` : run docker container with interactive mode (There is python example in section 2)
- `docker start -a <container-name>` : start docker in attached mode
- Attached mode : run in terminal (block terminal), get output cannot pass input
- Detached mode : run in background, not block terminal, not get logs in terminal
- Interactive mode : combine with attached mode and can get output and pass input
- `docker start -a -i <container-name>` : start docker in interactive mode
- `docker stop <container-name> <container-name> ...` : stop containers
- `docker rm <container-name> <container-name> ... ` : remove container
- `docker
- `docker images` : list all docker image
- `docker rmi <image-id> <image-id> <image-id>` : remove images
- `docker image prune` : remove all image
- `docker image prune -a` : remove all image including tagged images
- `docker cp <origin-location> <destionation-location>` : copy file from host to container (vice versa if swap position)
  - e.g. `docker cp dummy/. boring_vaughan:/test` (boring_vaughan is container name)
- `docker run -p 3000:80 -d --rm --name docker-container-name 5144f068c3ddcc` : `--name docker-container-name` add name to container
- `docker build -t name:tag .` : assign name:tags to docker container (tags is like a version)
- `docker tag <old-image-name>:<old-image-tag> <new-image-name>:<new-image-tag>` : change image name:tag
- `docker push academind/node-hello-world` push images to remote (DockerHub) (`academind/node-hello-world` == name of local image)
- `docker pull academind/node-hello-world` pull images from remote (DockerHub)

# 3. Managing Data & Volume

- `docker run -d -p 3000:80 --rm --name feedback-app -v <volume-name>:<path> feedback-node:volumes` : run container with named volume

  - `<path>` : path inside container that we want to save
  - e.g. `docker run -d -p 3000:80 --rm --name feedback-app -v feedback:/app/feedback feedback-node:volumes`
  - for bind mounts: replace `<volume-name>` with full host path (`D:\sample\folder`)
    - shorten by:
      - macOS / Linux: `-v $(pwd):/app`
      - Windows: `-v "%cd%":/app`

- `docker volume rm <VOL_NAME>` : remove volume
- `docker volume prune` : remove all vol

- `docker run -d --rm -p 3000:80 --name feedback-app -v feedback:/app/feedback -v "full-path-on-host-machine>:app" -v /app/node_modules feedback-node:volumes`
  - `-v feedback:/app/feedback` : create named vol
  - `-v "full-path-on-host-machine>:app"` : create bind mount to update any source code change to container
  - `-v /app/node_modules` : create anonymous volume to override the above bind mount not to delete "node_modules" folder in the container. It delete because we do not have node_modules folder in host machine and bind-mount try to update the host machine to container. Thus, it delete node_modules folder in container
- `-v "full-path-on-host-machine>:app:ro"` : create read-only volume

- use `.dockerignore` file to ignore copy some file

- `docker run -d --rm -p 3000:80 (specify env) --name feedback-app -v feedback:/app/feedback feedback-node:volumes`

  - `(specify env)` = `--env-file./.env` : read environment variable from `.env`
  - `(specify env)` = `--env PORT=8000` : assign variable
  - `(specify env)` = `-e PORT=8000 -e VAR=1` : assign multiple variables

- Arguments
  - dockerfile `ARG DEFAULT_PORT=80` : assign argument in dockerfile
  - dockerfile `ENV PORT $DEFAULT_PORT` : use argument in dockerfile
  - command line `docker build -t feedback-node:dev --build-arg DEFAULT_PORT=8000 .` : override argument when creating image

# 4. Networking: Container Communication

- `docker network create favorites-net` : create network

- `docker network create --driver bridge favorites-net` : create network with bridge driver (no need to specify because bridge driver is default value)

  - bridge : default driver and most common in majority of scenarios
  - host : For standalone containers, isolation between container and host system is removed (i.e. they share localhost as a network)
  - overlay : Multiple Docker daemons (i.e. Docker running on different machines) are able to connect with each other. Only works in "Swarm" mode which is a dated / almost deprecated way of connecting multiple containers
  - macvlan : You can set a custom MAC address to a container - this address can then be used for communication with that container
  - none : All networking is disabled.
  - Third-party plugins : You can install third-party plugins which then may add all kinds of behaviors and functionalities

# 5. Building Multi-Container Application with Docker

- `docker run --name mongodb --rm -d --network goals-net mongo` : create container for mongodb (from docker hub)
- `docker build -t goals-node .` : build container image for backend
- `docker build -t goals-react .` : build container image for front end
- `docker run --name goals-backend --rm -d -p 80:80 goals-node` : run docker container
- `docker run --name goals-frontend --rm -p 3000:3000 -it goals-react` : run docker container for frontend (don't forget -it for React)

- put them in network
- `docker network create goals-net` : create network
- `docker run --name mongodb --rm -d --network goals-net mongo` : create container for mongodb (from docker hub)
- `docker run --name goals-backend --rm -d -p 80:80 --network goals-net goals-node` : run docker container. publish port (-p 80:80) to communication with react
- `docker run --name goals-frontend --rm -p 3000:3000 -it goals-react` : run docker container for frontend (don't forget -it for React). we still need to publish port (-p 3000:3000) because we want frontend to be connected from host

add persistent volume

- `docker run --name mongodb -v data:/data/db --rm -d --network goals-net mongo` : create volume for persistent data

add variable for security in mongodb

- `docker run --name mongodb -v data:/data/db --rm -d --network goals-net -e MONGO_INITDB_ROOT_USERNAME=max -e MONGO_INITDB_ROOT_PASSWORD=secret mongo`

add volume for backend

- `docker run --name goals-backend -v <local-backend-repo-path>:/app -v logs:/app/logs -v /app/node_modules --rm -d -p 80:80 --network goals-net goals-node` this is not work with windows due to `<local-backend-repo-path>` issue
- `docker run --name goals-backend -v logs:/app/logs --rm -d -p 80:80 --network goals-net goals-node`

add variable

- `docker run --name goals-backend -v logs:/app/logs -e MONGODB_USERNAME=max --rm -d -p 80:80 --network goals-net goals-node`

# 6. Docker Compose

- `docker-compose up -d` : start compose in detach mode
- `docker-compose down` : stop compose

# 7. Utility Containers

- `docker run -it -d node` : run node from docker hub image
- `docker exec <container-name> npm init` : execute command inside container
- `docker run -it node npm init` : run node + execute command

# 8. Complex Project
