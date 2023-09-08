---
title: "Nodemon"
pre: "6. "
weight: 60
---

At this point we probably want to install the [Nodemon](https://www.npmjs.com/package/nodemon) tool in our backend.

## Install Nodemon

First, we'll get a terminal inside of the container

```bash
# Terminal
docker exec -it project-server bash
```

Then we can use the usual process to install Nodemon using `npm`. Make sure this is done inside of the `/app/server` directory:

```bash
# Docker project-server Terminal
npm install nodemon
```

Once Nodemon is installed, we can close the terminal connected to the container using the `exit` command.

{{% notice tip %}}

This same basic process can be used to install any library using `npm` inside of the container.

{{% /notice %}}

## Update Dockerfile

To use Nodemon inside of our container, we need to update the `scripts` section of the `package.json` file for the server:

```json {hl_lines=[5,6]}
/* server/package.json */
{
  /* other contents */
  "scripts": {
    "dev": "nodemon ./bin/www",
    "start": "NODE_ENV=production node ./bin/www"
  },
  /* other contents */
}
```

This adds a new `dev` command that uses Nodemon to run the server in development mode, and also changes the `start` command to run the server in production mode.

We also need to update the `CMD` entry in the server's Dockerfile to use the new `dev` command:

```dockerfile {hl_lines=[8]}
# server/Dockerfile
# For running in development mode only
FROM node:18
WORKDIR /app/server
COPY package*.json ./
RUN npm install
EXPOSE 3000
CMD ["npm", "run", "dev"]
```

With those changes, we need to rebuild and restart our containers:

```bash
# Terminal
docker compose up -d --build
```

The `--build` will build any changed containers.