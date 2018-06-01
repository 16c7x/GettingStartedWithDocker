## Starting up a development environment.
You can start up a temporary development environment by going to [Play with Docker](https://labs.play-with-docker.com/#), you'll need to setup an account.
Your session will last 4 hours before it's reset, you can drag files onto the session or if you have an external repository such as [gitlab.com](https://about.gitlab.com/) or [github.com](https://github.com/) you can run a ```git clone``` to pull any projects in. 

## 1. Run an image.
A Docker container must have something to do, if the last instruction you give a docker container is to upgrade a package it would run the upgrade and then shut down. 
I'll use this image as an example [nginx:alpine](https://hub.docker.com/_/nginx/). If you look at it's Docker file the last command is to start up nginx ```CMD ["nginx", "-g", "daemon off;"]```.
In the play with docker console run ```docker run -p 8080:80 nginx:alpine```.
Near the top you should see the port number 8080 appear, if you click on it you'll get to the nginx test page.
* ```docker run``` - Tells Docker to run a command.
* ```-p 8080:80``` - Maps the docker containers port 80 to the hosts port 8080.
* ```nginz:alpine``` - The container to use.


## 2. Getting into the running image.
Start the above docker container but daemonize it with a ```-d``` so ```docker run -p 8080:80 -v $(pwd):/usr/share/nginx/index -d nginx:alpine```.  
Run ```docker ps``` under *NAMES* you should see the name of the container.  
Run ```docker exec -it <container name> /bin/sh``` you should now find yourself in the shell of the running container.  
Make a change to the web page displayed on port 8080 using vi ```vi /usr/share/nginx/html/index.html``` and change something, for example: 
```
change
<h1>Welcome to nginx!</h1>
to
<h1>Hello, Welcome to nginx!</h1>
```

## 3. Copying a file in
Create a new index.html file for nginx;
```
<!DOCTYPE html>
<html>
<body>
<h1>Welcome to my nginx page!</h1>
</body>
</html>
```
Save it to the play with Docker console and run ```docker run -p 8080:80 -v $(pwd)/index.html:/usr/share/nginx/html/index.html -d nginx:alpine```  
The web page that is displayed should now show your message. 

## 4. Using a Docker file
A docker file can be used to create a new docker container.
make a new file called ```dockerfile``` and add the following:  
```
FROM nginx:apline

COPY ./index.html /usr/share/nginx/html/index.html

CMD ["nginx", "-g", "daemon off;"]
```
Create the ```index.html``` file that docker is going to add to the new image:  
```
<!DOCTYPE html>
<html>
<body>
<h1>Welcome to my nginx page!</h1>
</body>
</html>
```
Now build the new image ```docker build -t "test:dockerfile"``` and run it ```docker run -p 8080:80 -d test:dockerfile```.

## 5. Using a Docker compose
Rather than dealing with increasingly long command lines to more complex Docker containers everything can be put into a docker compose file e.g.
```
version: '3'
services:
  nginx:
    image: nginx:alpine
    ports:
      - 8001:80
```


## 5. Local monitoring
This is publicly available project that can creates some containers on a docker host to monitor other docker containers, possibly useful in helping debug more complex testing developement environments [github.com/stefanprodan](https://github.com/stefanprodan/dockprom.git).
