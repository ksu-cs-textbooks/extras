---
title: "Working with Docker"
pre: "5. "
weight: 50
---

Now that the project is running in Docker, let's review some helpful commands we can use to interact with the Docker containers.

## Docker Status

Query running container status

```bash
# Terminal
docker ps
```

Sample output:

```
CONTAINER ID   IMAGE                      COMMAND                  CREATED         STATUS         PORTS      NAMES
119d2cc1ae20   fullstack-project-client   "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes   3000/tcp   project-client
b5c0853fa8b2   fullstack-project-server   "docker-entrypoint.s…"   2 minutes ago   Up 2 minutes              project-server
```

## Software Output

You can view the output of programs running in a container using this command:

```bash
# Terminal
docker logs project-client
```

Replace `project-client` with the name of the container to query.

If you want to monitor the logs for new output, add the `-f` flag:

```bash
# Terminal
docker logs -f project-client
```

When following the logs, use <kbd>CTRL</kbd> + <kbd>C</kbd> to exit. 

## Terminal

To get an interactive terminal inside of the container, use this command:

```bash
# Terminal
docker exec -it project-client bash
```

Replace `project-client` with the name of the container to run the command in.

The command `bash` will get you an interactive terminal running Bash. You can replace `bash` with another command to run that command inside of the container itself. This allows you to run comands inside of the contaniner while it is running. However, your app will also be running, so you will be limited in what you can do.

## Run Commands

Alternatively, you can run some commands inside of a container using Docker compose:

```bash
#Terminal
docker compose run --rm project-client npm run test
```

This will create a new temporary container to run the command, so be aware that there may be some side effects of doing so. 

## Cleanup

Finally, Docker provides a handy command to cleanup any unused Docker resources on your system to conserve space:

```bash
# Terminal
docker system prune
```

This removes all unused containers, images, networks, and more. However, it will not remove any data volumes by default, but you can add the `--volumes` option to do just that. 

## Reference

[Docker Command Line Reference](https://docs.docker.com/engine/reference/commandline/cli/)