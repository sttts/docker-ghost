# Ghost & MySQL with Docker Compose

Ghost is an open source publishing platform which is beautifully designed, easy to use, and free for everyone.
In this project we will connect a ghost container with a persistent MySQL container.

Prerequisition: Docker version 1.11.x <

#### Create named docker volume
```
docker volume create --name mysql-volume
```

#### Check named volume
```
# cd /var/lib/docker/volumes# ls
mysql-volume
```

#### Build with Docker Compose
Check the docker-compose file for more documentation
```
$ docker-compose up --build -d
```
*It can be possible you have to run this command twice, because the ghost container will only run when the mysql container is up and the mysql service is started. A solution will be offered soon. The healthcheck will be integrated into Docker in Version 1.12.0)* 

#### Check the running containers
```
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                     NAMES
032189218b2e        my-ghost:1.0.0      "/entrypoint.sh npm s"   13 seconds ago      Up 4 seconds        0.0.0.0:2368->2368/tcp    ghost
95ede415d92e        mysql:5.7.12        "docker-entrypoint.sh"   13 seconds ago      Up 12 seconds       0.0.0.0:32768->3306/tcp   mysql
```
You're able to create your users, blogs, etc on https://localhost:2368/ghost/setup
#### Perform a docker inspect to check the volume of your mysql
```
$ docker inspect --format '{{range .Mounts }} Name: {{.Name}} + Source: {{.Source}}{{end}}' 95ede415d92e
Name: mysql-volume + Source /var/lib/docker/volumes/mysql-volume/_data
```

Now you're able to delete your ghost and mysql container without losing data. 
```
$ docker rm -fv 032189218b2e
$ docker rm -fv 95ede415d92e
```
The ```-v``` tag will not delete the ```mysql-volume```.  
So the mysql database will reconnect with the named volume after rerunning ```docker-compose up --build -d ```.  
As long as your named volume  ```mysql-volume ```- exists, it's possible to reconnect your mysql. 
Some scripts to delete dangling volumes can delete your named volume (when it isn't connected with a container). 
But here for is also a solution which you can find here. 
A mix of these solutions will give you the possibility to persist your data. 
