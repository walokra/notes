# Docker

## Cleanup

https://github.com/moby/moby/issues/18867

**remove exited containers**

    $ docker rm $(docker ps -q -f 'status=exited')

**remove dangling images**

    $ docker rmi $(docker images -q -f=dangling=true)

**remove dangling volumes**

    $ docker volume rm $(docker volume ls -qf dangling=true)
    $ docker system prune -a
    $ docker rmi $(docker images | grep "^<none>" | awk '{print $3}')

    docker images | grep search | awk '{ print $3 }' | xargs docker rmi -f
    docker ps -a | grep search | awk '{ print $1 }' | xargs docker rm -f

# delete containers

    sudo docker rm -v $(sudo docker ps -a -q -f status=created)

Volumes

    $ docker volume ls -qf dangling=true

Cleanup:

    $ docker volume rm $(docker volume ls -qf dangling=true)

https://github.com/wsargent/docker-cheat-sheet
https://github.com/konstruktoid/Docker/blob/master/Security/CheatSheet.md
https://github.com/docker/docker-bench-security
http://crosbymichael.com/dockerfile-best-practices-take-2.html


**Build images**

    $ docker build -t <image name and tag> .

**Get IP address**

    docker inspect `dl` | grep IPAddress | cut -d '"' -f 4

**Get port mapping**

    docker inspect -f '{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -> {{(index $conf 0).HostPort}} {{end}}' <containername>

**List containers**

    docker ps -a

**List images**
    docker images

**Remove images**

    docker rmi [image ID]

**Untagged images (dangling)**

    docker images --filter "dangling=true"
    docker rmi $(docker images -f "dangling=true" -q)

**Delete stopped containers**

    docker rm -v `docker ps -a -q -f status=exited`

**Create container**

    docker run -d <image name>
    docker run -t -i <image name> /bin/bash
    docker run -it --rm -p 8080:80 --name <image name> <image name>

    $ docker-compose up -d mysql
    $ docker run -ti -e MYSQL_ROOT_PASSWORD=mysql -e MYSQL_DATABASE=piwik -p 3306:3306 mysql

Get image

    $ docker pull <image>

**Inspecting image**

    $ docker exec -it <image name> bash

See logs

    $ docker logs piwik
    $ docker-compose logs mysql

Kill running containers:

    docker kill $(docker ps -q)

Monitor system resource utilization for running containers

    docker stats <container>
    docker stats $(docker ps -q)
    docker stats $(docker ps --format '{{.Names}}')

Image dependency diagram

    docker images -viz | dot -Tpng -o docker.png
    python -m SimpleHTTPServer
    http://machinename:8000/docker.png
