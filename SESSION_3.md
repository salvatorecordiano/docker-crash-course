# Docker Crash Course

## Session 3

- Container composition
- Docker CLI, Daemon, Registry, Compose
- Anatomy of a `docker-compose.yaml` file
- Build context and other commands
- Final exercise

## Container composition

Open the `section_03/demo-app` folder.

We have a [Symfony demo app](https://github.com/symfony/demo) packed in the image defined in `php/Dockerfile`. 
It's already tagged and pushed as `ilariopierbattista/demo-app:1`, so you can just pull it from Dockerhub.

> Exercise 1: open the Dockerfile and describe what each line does.

The demo app will need a database to connect to.
We create a container with the `mysql:8` image which will run an instance of mysql. 
How to connect this containers to each other?

    docker network create demo-net
    docker network inspect demo-net

Now we have the virtual network `demo-net`.

    docker run --rm \
        --net demo-net \
        --net-alias mysql.demo-app \
        -e MYSQL_DATABASE=db_demo \
        -e MYSQL_ROOT_PASSWORD=secret-password \
        --mount type=bind,src=$(pwd)/dump.sql,dst=/docker-entrypoint-initdb.d/dump.sql \
        --name demo-app_mysql \
        mysql:8
    
    docker network inspect demo-net
    docker inspect demo-app_mysql

We have the container `demo-app_mysql` up, running and attached to the `demo-net` network.
From the inside of this network, `mysql.demo-app` resolves the `demo-app_mysql` container's IP.

    docker run --net demo-net alpine nslookup mysql.demo-app  

Now we can start the `ilariopierbattista/demo-app:1` image.

    docker run --rm \
        --net demo-net \
        -p 10088:80 \
        -e DATABASE_URL=mysql://root:secret-password@mysql.demo-app/db_demo \
        --name demo-app_php \
        ilariopierbattista/demo-app:1

    docker network inspect demo-net
    docker inspect demo-app_php

Then open http://localhost:10088. 

> Exercise 2: run the docker commands above from the inside of the `session_03/demo-app` folder. 
> Describe each command and analyse its meaing.

This is container composition.
It can be done with Docker CLI, but there's a simpler way.


## Docker CLI, Daemon, Registry, Compose

Recall the differences between Docker CLI, the Docker Engine (the daemon), and the Docker registries.

    docker run hello-world

The Docker Engine is responsible for managing containers, volumes and networks.

Docker CLI interacts with the Engine via HTTP APIs. The CLI is a client tool that consumes the Engine's APIs.

Docker Compose is a **client-side tool** like Docker CLI, it actually uses Docker CLI, but it's designed specifically to make easier the container composition.

Compose reads a configuration file, that describes a container composition in a declarative way, and then is runs **the needed** CLI commands to run the composition as depicted.

Be aware: 
- Compose it's not for production.
- Compose it's not a container orchestration runtime (unlike Docker Swarm or Kubernetes).


## Anatomy of a docker-compose.yaml

- version
- services
- networks

Validate and print the configuration Compose had read.

    docker-compose config

Start the composition.

    docker-compose up

Start it in detached mode.

    docker-compose up -d
    docker-compose down

> Exercise 3: read and describe the `section_03/demo-app-compose/docker-compose.yaml`.
> Visualize the connection between the content of the file and the parameters of the Docker CLI commands described in the previous paragraph.

> Exercise 4: start the composition and check the app is working correctly.


## Build context and other commands

Compose let you define also the build context for the image of the services.

    docker-compose build php
    docker-compose push php

The complete reference of Compose CLI can be found [here](https://docs.docker.com/compose/reference/).

Read the reference documentation to figure out how to complete the following exercises.

> Exercise 5: start the demo app composition in detached mode. Find a way to visualize, in two different terminals, the logs from the mysql service and the logs from the php service.

> Exercise 6: containers in Compose can be reached from the insided of their network by referring to the service name.
> - Start the composition.
> - Run `nslookup mysql.` in the php container.
> - Run also `nslookup mysql.demo-app` and then verify are pointing to the same IP.
> - Verify with the network inspector that it's the mysql container's IP.


## Final exercise

Create a container that connects to the right network and makes a CURL to the web server (recall it's the php service).

Constraints:
- Use the `php:8.1-alpine` image.
- Use the `curl -vI <URL>` command.
- After having tested that the CURL is working, use `watch curl -vI <URL>` to make it call the web server every two seconds.
- Convert the Docker CLI command in a new service in the `docker-compose.yml` file.
- Use logs to check that everything is working fine.

Hints:
- `php:8.1-alpine` specifies an entrypoint. Use `--entrypoint ''` to override it.


## References

- [Compose documentation](https://docs.docker.com/compose/)
    - [Compose CLI reference](https://docs.docker.com/compose/reference/)
    - [Compose file reference](https://docs.docker.com/compose/compose-file/)
