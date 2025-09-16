---
title: "Instructions"
pre: "1. "
weight: 10
---

## Setup Dev Container

Follow the steps on this page to create a new devcontainer and GitHub Codespace: https://textbooks.cs.ksu.edu/cis526/x-examples/01-express-starter/01-codespace/index.html.

Choose the **Node.js & PostgreSQL** container type with Node version **22-bookworm**. 

Also consider adding the `docker-outside-of-docker` feature by selecting it from the list or adding it directly to your `devcontainer.json` file. This allows you to see and control the PosgreSQL container. See [this link] for details.

## Setup Server

{{% notice warning %}}

All commands and files in this section should be performed in the `server` directory that is created as part of the first step. 

{{% /notice %}}

Follow the steps on this page to create a new Express server: https://textbooks.cs.ksu.edu/cis526/x-examples/01-express-starter/02-express-starter/index.html

Use the `npm-check-updates` comamnd to update all installed packages. 

### Environment

Install dotenvx as shown on this page: https://textbooks.cs.ksu.edu/cis526/x-examples/01-express-starter/07-environment/index.html

Add to top of `app.js`

```js
require('@dotenvx/dotenvx').config()
```

Create `.env` file with these contents:

```env
port=3000
```

### Database

Install libraries:

```bash
$ npm install sequelize umzug pg pg-hstore
```

Database configuration file `config/database.js`:

```js
const { Sequelize } = require('sequelize');

const sequelize = new Sequelize({
// Supports "sqlite" or "postgres"
  dialect: "postgres",
  host: process.env.DATABASE_HOST || "db",
  port: process.env.DATABASE_PORT || 5432,
  username: process.env.DATABASE_USERNAME || "postgres",
  password: process.env.DATABASE_PASSWORD || "postgres",
  database: process.env.DATABASE_NAME || "postgres",
})

module.exports = sequelize;
```

Update `.env` file with environment:

```env
PORT=3000
DATABASE_HOST=db
DATABASE_PORT=5432
DATABASE_USERNAME=postgres
DATABASE_PASSWORD=postgres
DATABASE_NAME=postgres
```

### Database Migrations

Create migration configuration `config/migrations.js`

```js
const { Umzug, SequelizeStorage } = require('umzug');

const database = require('./database')

const umzug = new Umzug({
    migrations: {glob: 'migrations/*.js'},
    context: database.getQueryInterface(),
    storage: new SequelizeStorage({
        sequelize: database,
        modelName: 'migrations'
    }),
})

module.exports = umzug;
```

Create script `migrate.js`

```js
require('@dotenvx/dotenvx').config()
const migrations = require('./config/migrations')
migrations.runAsCLI()
```

Create migration `migrations/00_users.js`

```js
const { Sequelize } = require('sequelize');

async function up({context: queryInterface}) {
    await queryInterface.createTable('users', {
        id: {
            type: Sequelize.INTEGER,
            primaryKey: true,
            autoIncrement: true,
        },
        username: {
            type: Sequelize.STRING,
            unique: true,
            allowNull: false,
        },
        createdAt: {
            type: Sequelize.DATE,
            allowNull: false,
        },
        updatedAt: {
            type: Sequelize.DATE,
            allowNull: false,
        },
    })
}

async function down({context: queryInterface}) {
    await queryInterface.dropTable('users');
} 

module.exports = {up, down}
```

Run migration

```bash
node migrate up
```

### Seeds

Create seed configration `config/seeds.js`

```js
const { Umzug, SequelizeStorage } = require('umzug');

const database = require('./database')

const umzug = new Umzug({
    migrations: {glob: 'seeds/*.js'},
    context: database.getQueryInterface(),
    storage: new SequelizeStorage({
        sequelize: database,
        modelName: 'seeds'
    }),
})

module.exports = umzug;
```

Create script `seed.js`

```js
require('@dotenvx/dotenvx').config()
const seeds = require('./config/seeds')
seeds.runAsCLI()
```

Create seed file `seeds/00_users.js`

```js
const { Sequelize } = require('sequelize');

// Timestamp in the appropriate format for the database
const now = new Date().toISOString().slice(0, 23).replace("T", " ") + " +00:00";

// Array of objects to add to the database
const users = [
    {
        id: 1,
        username: 'admin',
        createdAt: now,
        updatedAt: now
    },
    {
        id: 2,
        username: 'contributor',
        createdAt: now,
        updatedAt: now
    },
    {
        id: 3,
        username: 'manager',
        createdAt: now,
        updatedAt: now
    },
    {
        id: 4,
        username: 'user',
        createdAt: now,
        updatedAt: now
    },
];

async function up({context: queryInterface}) {
    await queryInterface.bulkInsert('users', users);
    await queryInterface.sequelize.query("SELECT setval('users_id_seq', max(id)) FROM users;");
}

async function down({context: queryInterface}) {
    await queryInterface.bulkDelete("users", {}, { truncate: true });
} 

module.exports = {up, down}
```

### Automation

Update `bin/www` file to automate migration and seeding:

```js
#!/usr/bin/env node

/**
 * Module dependencies.
 */

var app = require('../app');
var debug = require('debug')('server:server');
var http = require('http');
var database = require('../config/database')
var migrations = require('../config/migrations')
var seeds = require('../config/seeds')

/**
 * Get port from environment and store in Express.
 */

var port = normalizePort(process.env.PORT || '3000');
app.set('port', port);

/**
 * Create HTTP server.
 */

var server = http.createServer(app);

/**
 * Listen on provided port, on all network interfaces.
 */

// server.listen(port)
server.on('error', onError);
server.on('listening', onListening);

// Call startup function
startup();

function startup() {
  try {
    // Test database connection
    database.authenticate().then(() => {
      console.log("Database connection successful")
      // Run migrations
      migrations.up().then(() => {
        console.log("Database migrations complete")
        if (process.env.SEED_DATA === 'true') {
          console.log("Database data seeding is enabled!")
          seeds.up().then(() => {
            console.log("Database seeding complete")
            server.listen(port)
          })
        } else {
          // Listen on provided port, on all network interfaces.
          server.listen(port)
        }
      })
    })
  } catch (error){
    logger.error(error)
  }
}

// ======= other code here ========

```

Update `.env` file with environment:

```env
PORT=3000
DATABASE_HOST=db
DATABASE_PORT=5432
DATABASE_USERNAME=postgres
DATABASE_PASSWORD=postgres
DATABASE_NAME=postgres
SEED_DATA=true
```


### Database Models

Create model `models/models.js`

```js
const { Sequelize } = require('sequelize');
const database = require('../config/database')

const UserSchema = {
    id: {
        type: Sequelize.INTEGER,
        primaryKey: true,
        autoIncrement: true,
    },
    username: {
        type: Sequelize.STRING,
        unique: true,
        allowNull: false,
    },
    createdAt: {
        type: Sequelize.DATE,
        allowNull: false,
    },
    updatedAt: {
        type: Sequelize.DATE,
        allowNull: false,
    },
}

const User = database.define(
    // Model Name
    'User',
    // Schema
    UserSchema,
    // Other options
    {
        tableName: 'users'
    }
)

module.exports = { User }
```

### Create API Endpoint

Edit `routes/users.js`

```js
var express = require('express');
var router = express.Router();
const { User } = require('../models/models')

/* GET users listing. */
router.get('/', async function(req, res, next) {
    const users = await User.findAll()
    res.json(users)
});

module.exports = router;
```

Start the app and navigate to the `/users` route:

```bash
$ npm start
```

Should see API output. Great - server is working!

To prepare for Vue, update `app.js` to remove the `index` router. 

## Vue

{{% notice warning %}}

All commands and files in this section should be performed in the `client` directory that is created as part of the first step.

{{% /notice %}}

Create a Vue starter project as described here: https://textbooks.cs.ksu.edu/cis526/x-examples/05-vue-starter/01-vue-starter/index.html

Can omit Pinia and Vitest features. Choose to skip all example code and start with a blank Vue project.

Npm install and check for updates

### Proxy

Update `vite.config.js` to add proxy:

```js
// https://vite.dev/config/
export default defineConfig({
  plugins: [
    vue(),
    vueDevTools(),
  ],
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url))
    },
  },
  server: {
    proxy: {
      '/users': {
        target: 'http://localhost:3000',
        changeOrigin: true,
        secure: false
      }
    }
  }
})
```

### API Call

Update `src/App.vue` to call API

```vue
<script setup>
import { ref } from 'vue'

const users = ref([])

fetch('/users/').then(response => response.json())
.then((response) => {
  users.value = response
}).catch((e) => {
  console.error(e)
})
</script>

<template>
  <h1>List of Users</h1>
  <ul>
    <li v-for="user in users" :key="user.id">{{ user.username }}</li>
  </ul>
</template>

<style scoped></style>
```

In two terminals, start server and client and open browser. Should see a list of users

```bash
$ cd server
$ npm start
```

```bash
$ cd client
$ npm run dev
```

## Prepare Deployment

{{% notice warning %}}

All commands and files in this section should be performed in the base directory for the project (outside of `client` and `server`).

{{% /notice %}}

### Dockerfile

Create `Dockerfile`

```Dockerfile
# Node Version
ARG NODE_VERSION=22

###############################
# STAGE 1 - BUILD CLIENT      #
###############################

# Client Base Image
# See https://hub.docker.com/_/node/
FROM node:${NODE_VERSION}-alpine as client

# Use production node environment by default
ENV NODE_ENV production

# Store files in /usr/src/app
WORKDIR /usr/src/app

# Download dependencies as a separate step to take advantage of Docker's caching.
# Leverage a cache mount to /root/.npm to speed up subsequent builds.
# Leverage a bind mounts to package.json and package-lock.json to avoid having to copy them into
# into this layer.
# See https://docs.docker.com/build/cache/optimize/
RUN --mount=type=bind,source=client/package.json,target=package.json \
    --mount=type=bind,source=client/package-lock.json,target=package-lock.json \
    --mount=type=cache,target=/root/.npm \
    npm ci --include=dev

# Copy the rest of the source files into the image.
COPY ./client .

# Build the client application
RUN npm run build

###############################
# STAGE 2 - BUILD SERVER      #
###############################

# Server Base Image
# See https://hub.docker.com/_/node/
FROM node:${NODE_VERSION}-alpine as server

# Use production node environment by default
ENV NODE_ENV production

# Store files in /usr/src/app
WORKDIR /usr/src/app

# Download dependencies as a separate step to take advantage of Docker's caching.
# Leverage a cache mount to /root/.npm to speed up subsequent builds.
# Leverage a bind mounts to package.json and package-lock.json to avoid having to copy them into
# into this layer.
# See https://docs.docker.com/build/cache/optimize/
RUN --mount=type=bind,source=server/package.json,target=package.json \
    --mount=type=bind,source=server/package-lock.json,target=package-lock.json \
    --mount=type=cache,target=/root/.npm \
    npm ci --omit=dev

# Copy the rest of the source files into the image
COPY ./server .

# Copy the built version of the client into the image
COPY --from=client /usr/src/app/dist ./public

# Make a directory for the database and make it writable
RUN mkdir -p ./data
RUN chown -R node:node ./data

# Make a directory for the uploads and make it writable
RUN mkdir -p ./public/uploads
RUN chown -R node:node ./public/uploads

# Run the application as a non-root user.
USER node

# Expose the port that the application listens on.
EXPOSE 3000

# Command to check for a healthy application
HEALTHCHECK CMD wget --no-verbose --tries=1 --spider http://localhost:3000/users || exit 1

# Run the application.
CMD npm run start
```

### GitHub Action Workflow

Create `.github/workflows/build_docker.yml`

```yml
# Workflow name
name: Build Docker

# Run only on new tags being pushed
# https://docs.github.com/en/actions/using-workflows/triggering-a-workflow
on:
  push:
    tags:
      - 'v*.*.*'

# Define a single job named build
jobs:
  build:
    # Run job on Ubuntu runner
    runs-on: ubuntu-latest

    # Job Steps
    steps:
      # Step 1 - Checkout the Repository
      # https://github.com/actions/checkout
      - name: 1 - Checkout Repository
        uses: actions/checkout@v4

      # Step 2 - Log In to GitHub Container Registry
      # https://github.com/docker/login-action
      - name: 2 - Login to GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.repository_owner }}
          password: ${{ secrets.GITHUB_TOKEN }}
      
      # Step 3 - Build and Push Docker Image
      # https://github.com/docker/build-push-action
      - name: 3 - Build and Push Docker Image
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: |
            ghcr.io/${{ github.repository }}:${{ github.ref_name }}
            ghcr.io/${{ github.repository }}:latest

      # Step 4 - Make Release on GitHub
      # https://github.com/softprops/action-gh-release
      - name: 4 - Release
        uses: softprops/action-gh-release@v2
        with:
          generate_release_notes: true
```

### Create Compose File

Create `compose.yml`

```yml
services:
  
  ######################################
  # Deployment Example
  #
  # Repository:
  # https://github.com/cis526-codio/ex-deploy
  example-deploy:
    
    # Docker Image
    # !!TODO - Update this to match your repository !!
    image: ghcr.io/cis526-codio/ex-deploy:latest

    # Container Name
    container_name: example-deploy

    # Restart Container Unless Stopped
    restart: unless-stopped

    # Networks
    networks:
      - default
      - example-deploy-network

    # Network Ports
    ports:
      - "3000:3000"

    # Environment Variables
    environment:
      # Seed initial data on first startup
      SEED_DATA: 'true'
      # Database
      DATABASE_HOST: example-deploy-db
      DATABASE_PORT: 5432
      DATABASE_USERNAME: example-deploy
      DATABASE_PASSWORD: example-deploy
      DATABASE_NAME: example-deploy

  ######################################
  # Postgres Database
  #
  # Image Location:
  # https://hub.docker.com/_/postgres
  example-deploy-db:
    # Docker Image
    image: postgres:17-alpine

    # Container Name
    container_name: example-deploy-db

    # Restart Container Unless Stopped
    restart: unless-stopped

    # Networks
    networks:
      - example-deploy-network

    # Volumes
    volumes:
      - example-deploy-db-data:/var/lib/postgresql/data:rw

    # Environment Variables
    environment:
      POSTGRES_USER: example-deploy
      POSTGRES_PASSWORD: example-deploy
      POSTGRES_DB: example-deploy

volumes:
  example-deploy-db-data:
networks:
  example-deploy-network:
    internal: true
```

Commit a `v0.0.1` tag, deploy, and enjoy!