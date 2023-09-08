---
title: "Database"
pre: "7. "
weight: 70
---

Now let's add a database to our project. 

## Postgres Container

First, we can add an entry to our Docker Compose file to create a Postgres database, and also an entry for the volume to store the data:

```yml {hl_lines="4-27 37-39 71-72"}
# docker-compose.yml
services:

  # database
  project-db:
    # Use an existing image from DockerHub
    image: postgres:15
    container_name: project-db
    # Automatically restart the container as needed
    restart: unless-stopped
    networks:
      - project-network
    volumes:
      # Persist database data in a Docker volume
      - project_db_data:/var/lib/postgresql/data:rw
    environment:
      # Configure environment using .env file
      POSTGRES_USER:
      POSTGRES_PASSWORD:
      POSTGRES_DB:
    healthcheck:
      # check if database is running and healthy
      test: ["CMD-SHELL", "pg_isready -d $${POSTGRES_DB} -U $${POSTGRES_USER}"]
      interval: 30s
      timeout: 5s
      retries: 5
      start_period: 10s

  # server
  project-server:
    build:
      # location of Dockerfile
      context: ./server
    container_name: project-server
    # set the user ID to match our user
    user: "1000"
    depends_on:
      # requires database to start
      - project-db
    networks:
      - project-network
      # allow external connections (remove this in production)
      - default
    volumes:
      # mount code into container
      - ./server:/app/server
      # maintain existing node_modules in container
      - /app/server/node_modules
  
  # client
  project-client:
    build:
      # location of Dockerfile
      context: ./client
    container_name: project-client
    # set the user ID to match our user
    user: "1000"
    depends_on:
      # requires server to start
      - project-server
    networks:
      - project-network
      # allow external connections
      - default
    volumes:
      # mount code into container
      - ./client:/app/client
      # maintain existing node_modules in container
      - /app/client/node_modules

volumes:
  project_db_data:

networks:
  # internal Docker network for project
  project-network:
    name: project-network
    internal: true
```

Refer to the [Postgres Docker Image](https://hub.docker.com/_/postgres) documentation for more details.

## Environment File

For this project, we are going to use a single global environment file that is shared across all containers. For this, create a file named `.env` in the root of the project and add the following contents:

```bash
# .env
POSTGRES_HOST="project-db"
POSTGRES_PORT="5432"
POSTGRES_USER="projectuser"
POSTGRES_PASSWORD="projectpass"
POSTGRES_DB="projectdb"
```

{{% notice tip %}}

Customize the user, password, and database variables as desired. The host should match the name of the database container, and the port should be the default port for the chosen database engine.

{{% /notice %}}

You should also create a file named `.env.example` that has the same contents. 

{{% notice warning %}}

You should never commit the `.env` file to GitHub, since it contains all of the secret information for your project. Instead, it is recommended to create a sample file such as `.env.example` that contains the same variables but with default values, and also some explanation of what each variable does. 

{{% /notice %}}

In the Docker Compose file above, we defined several variables in the `project-db` service that match variables in the `.env` file:

```yaml
    environment:
      # Configure environment using .env file
      POSTGRES_USER:
      POSTGRES_PASSWORD:
      POSTGRES_DB:
```

By including blank variables here, the values from the `.env` file for those variables will be shared with the container. Other variables will not be shared. 

## Start Container

Once we are ready, we can use Docker Compose to update our running containers and start the database container:

```bash
# Terminal
docker compose up -d
```

You can confirm that the database is up and running using `docker ps` or checking the logs using `docker logs project-db`. 

## Commit

This is a great place to stop and commit your work to GitHub. 