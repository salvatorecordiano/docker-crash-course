# Docker Crash Course

## Session 1

- docker run hello-world
- docker ps
- docker inspect
- docker run 
- docker stop

## Exercise

```
docker run --rm --entrypoint sh --name my-node-container -v $(pwd):/tmp -p 8000:80 node:17-alpine3.14 -c "sleep infinity"
```

## Extras

```
docker run -d -p 9000:9000 --rm -v /var/run/docker.sock:/var/run/docker.sock --name=portainer portainer/portainer
```

```
docker system prune -a -f
docker volume prune -f
docker network prune -f
docker ps -a | grep 'Exited' | awk '{ print $1 }' | xargs -r docker rm
docker run --rm -v /var/run/docker.sock:/var/run/docker.sock -e MINIMUM_IMAGES_TO_SAVE=1 -e REMOVE_VOLUMES=1 -v /etc:/etc:ro spotify/docker-gc
docker rmi spotify/docker-gc
```

## References

- [Containerization Explained](https://www.youtube.com/watch?v=0qotVMX-J5s)
- [Docker Cheat Sheet 1](https://github.com/wsargent/docker-cheat-sheet)
- [Docker Cheat Sheet 2](https://collabnix.com/docker-cheatsheet/)
- [Use the Docker command line](https://docs.docker.com/engine/reference/commandline/cli/)
