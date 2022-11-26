For your information, today we will deal with basic containers and commands. We won't talk about best practices regarding permissions and access rights which are notions very imporant in Docker though to build secure environnement.
## Docker CLI
- docker pull container_name
- docker run -it container_name name
- docker inspect container_name (gives information about a container)
- docker run -dit container_name name (-d for detach)
- docker ps -q (output id's of containers running)
- docker run container_name (run a new container) and docker exec container_name cmd (exec cmd in a container running)
- docker exec -it container_name bash (open a bash in a running container)
- docker history container_name
- docker info (gives info on the host)
- docker top container_id (same as doing ps aux in the container)
- docker ps --all (shows meta data of executed container)
- docker logs container_id
- docker rm container_id
- docker rmi container_name (remove the image)
- docker container prune (remove all stopped container)
- docker image prune
- docker image list
- docker restart container_id

## Information point
```docker run``` has more options than any other docker commands because it gives to the operator the ability to override image and Docker runtime defaults set by the developer. This command is runned in the foreground by default.
## Information point
Each lines are called instructions. They don't technically have to be all caps but it's convention to do so so that the file is easier to read. Each one of these instruction incrementally changes the container from the state it was in previously, adding what we call a layer.
# Basic node container

First task will be to write a Dockerfile for building a node container based on Debian stretch version 12.
Run a simple command in there and then build the image out of the Dockerfile.
Run the container from the image.

Launch a command inside the container from a docker command
Build the container by giving a name to image

## Try and check out the solution afterwards
node -e, if you don't know, will run whatever is inside of the quotes with Node.js. In this case, we're logging out hi lol to the console.

## Informatoin point
Layers : we have to think of containers like being onions. Each instruction is built depending on the previous one. The order really matters here. One instruction is executed only if the cache gets invalidated. So the clever way here is to reinstall the node dependencies only if a change appears there and not for every change on the js files.

# Build a Node.js App
Create a file with in there :
```const http = require("http");

http
  .createServer(function(request, response) {
    console.log("request received");
    response.end("omg hi", "utf-8");
  })
  .listen(3000);
console.log("server started");
```
Write a Dockerfile that conteneurize the application and run the app for us.
Open the according port.
COPY the index.js in the home/src directory

## Try and check out the solution afterwards

BTW : Does someone know why there is two requests ?

Several ID's on the output logs of ```docker build```, basic they are all valid containers and could be launched if we wish. That's the way Docker caches it

```docker run --init``` (exce TINI which allows control C to kill the container running)

```docker run [...] -p 3000:3000 ``` The publish part allows you to forward a port out of a container to the host computer. In this case we're forwarding the port of 3000 (which is what the Node.js server was listening on) to port 3000 on the host machine. The 3000 represents the port on the host machine and the second 3000 represents what port is being used in the container. If you did docker run --publish 8000:3000 my-node-app, you'd open localhost:8000 to see the server (running on port 3000 inside the container).

The COPY instruction copy by default in the root of the project.

Commands are runned by default with root access right.
# Exercise : more complicated node app

Time to pratice ! Have fun by doing a similar task then before but with this new server.

```// more-or-less the example code from the hapi-pino repo
const hapi = require("@hapi/hapi");

async function start() {
  const server = hapi.server({
    host: "0.0.0.0",
    port: process.env.PORT || 3000
  });

  server.route({
    method: "GET",
    path: "/",
    handler() {
      return { success: true };
    }
  });

  await server.start();

  return server;
}

start().catch(err => {
  console.log(err);
  process.exit(1);
});
```
Build the container for CI.
Get rid of the --publish flag and EXPOSE instead with the according flag.

## Try and check out the solution afterwards
```docker run [...] -P``` this flag is actually used for port mapping

IP : 0.0.0.0 and not localhost to be able to get outside the container.

npm ci : refers to the exact version of the package.json.
## Information point
Alpine is about 5 Mb and is the most barebone Linux distribution.
The issue with this one is that it's required more work to work.
But on the other side only what you really need it's installed, that means that a bunch of security vulnerabilies won't appear.
Example : Python isn't there
# Introduction to tiny containers
Let's built a more secure container for production, more cheaper and more faster.

First task would be to do the same application runned into a pre-built node container based on alpine.
Then we will built or own node container still based on the alpine distribution. (NOTE: I'd suggest always using the official one)

## Information point
Let's pull several images and compare the size
docker pull --quiet node:12-stretch
docker pull --quiet node:12-alpine
docker images
Docker inspect image_id

On production never use node:latest since it will pull a new version depending on the release. That may break dependencies which won't be compatible with the next version.


# Exercise : Build your own nginx container

Go to the according folder, every files that you need are already there.
Requirements :
- Base your container on debian:buster
- install what you need :)
- copy the configuration file to /etc/nginx/sites-enabled/.
- copy the file html to a folder named "template"
- copy the file html to the path "/etc/nginx/sites-available/"
- the nginx container will run thank the command : nginx -g daemon off
- the nginx server is running on port 443, so set up the according rule to access from the host


## Information point
### .dockerignore
To ignore what you copy inside your container : .git (security vulnerability to have that in your production server : git issues are sensitive information).
.git/
.node_modules/

### Prune unused Docker objects
Garbage collections : images, containers, volumes, networks
# Useful commands if you are playing around
Delete images created over the last 24 hours :
docker image prune -a --force --filter "until=24h"

Delete all stopped containers
docker rm $(docker ps -q -a)

# Next topics
Volumes / Network / Docker compose
