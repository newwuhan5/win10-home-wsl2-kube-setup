Instructions: Once git clone, run following two commands
$ docker build -f Dockfile_php -t my-php-app .
$ docker run -it --rm --name my-running-app my-php-app

Here is the output with above commands:
$ docker build -f Dockfile_php -t my-php-app .
Sending build context to Docker daemon  247.8kB
Step 1/4 : FROM php:7.4-cli
 ---> ed1ddce4ab77
Step 2/4 : COPY . /usr/src/myapp
 ---> b72a49473b8b
Step 3/4 : WORKDIR /usr/src/myapp
 ---> Running in df38e5069330
Removing intermediate container df38e5069330
 ---> b7296ff530ba
Step 4/4 : CMD [ "php", "./helloWorld.php" ]
 ---> Running in 1efc17570f66
Removing intermediate container 1efc17570f66
 ---> 0ff3a41c7148
Successfully built 0ff3a41c7148
Successfully tagged my-php-app:latest
$ docker run -it --rm --name my-running-app my-php-app
Hello World from Test Docker php image
$

How build a new container the image to your own hub.docker.com
$ docker run -d -t --name "MyPhp74Container" my-php-app:latest
b2b0f04b1fbdd3b1111b2a5d679ee0e4741980d9256babed5d60e9db93770db7#

Here is to find out latest built container
$ sudo docker container ls -l
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                     PORTS               NAMES
b2b0f04b1fbd        my-php-app:latest   "docker-php-entrypoi…"   6 minutes ago       Exited (0) 6 minutes ago                       MyPhp74Container

$ docker commit b2b0f04b1fbd newwuhan/php74cli-helloworld:v1
sha256:fbfe2082b0903325e343ef482d84039817aee11d5e79b25a3616b702c6821aab
$ docker image ls
REPOSITORY                     TAG                 IMAGE ID            CREATED             SIZE
newwuhan/php74cli-helloworld   v1                  fbfe2082b090        10 seconds ago      405MB



#### ############################################
newwuhan is my username in hub.docker.com
thus, login with that user name
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: newwuhan
Password:
WARNING! Your password will be stored unencrypted in /home/jacky/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

#### ############################################
Second time to login
#### ############################################
$ docker login
Authenticating with existing credentials...
WARNING! Your password will be stored unencrypted in /home/jacky/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded

#### ############################################
push the new image to hub.docker.com
################################################
$ docker push newwuhan/php74cli-helloworld
The push refers to repository [docker.io/newwuhan/php74cli-helloworld]
1e696fdd4c90: Pushed
dcf72d7b70f8: Pushed
631db6ac2b23: Pushed
71f46f59f0ec: Pushed
047d8f439303: Pushed
ad84e524b260: Pushed
710c0bd46e1d: Pushed
b62571eecc86: Pushed
62975489b8b4: Pushed
07cab4339852: Pushed
v1: digest: sha256:1c7971590401c6f9a6d64101cdfa04caaf171a7d2e6d51ecefc682dee1dfe2b5 size: 2411

The web link to view the docker image: 
https://hub.docker.com/repository/docker/newwuhan/php74cli-helloworld

docker run -it --entrypoint=/bin/bash newwuhan/php74cli-helloworld:v1