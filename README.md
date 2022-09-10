# README

My note for [Docker & Kubernetes: The Practical Guide](https://www.udemy.com/course/docker-kubernetes-the-practical-guide/) course

# 2. Docker Images & Container

- `docker build .` : build docker image
- `docker run -p <local-port>:<container-port> (-d) <image-key>`
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
- `docker rm <container-name> <container-name> ... ` : remove container
- `docker
- `docker images` : list all docker image
- `docker rmi <image-id> <image-id> <image-id>` : remove images
- `docker image prune` : remove all image
- `docker image prune -a` : remove all image including tagged images
- `docker cp <origin-location> <destionation-location>` : copy file from local to container (vice versa if swap position)
  - e.g. `docker cp dummy/. boring_vaughan:/test` (boring_vaughan is container name)
- `docker run -p 3000:80 -d --rm --name docker-image-name 5144f068c3ddcc` : `--name docker_image_name` add name to container
- `docker build -t name:tag .` : assign name:tags to docker container (tags is like a version)
- `docker tag <old-image-name>:<old-image-tag> <new-image-name>:<new-image-tag>` : change image name:tag
- `docker push academind/node-hello-world` push images to remote (DockerHub) (`academind/node-hello-world` == name of local image)
- `docker pull academind/node-hello-world` pull images from remote (DockerHub)
