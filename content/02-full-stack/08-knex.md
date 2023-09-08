---
title: "Knex"
pre: "8. "
weight: 80
---

We're going to use the [Knex](https://knexjs.org/) library to connect to our database from the server. We'll also use it to handle database migrations and seeding.

## Install Knex

We can install Knex using `npm` inside the server container:

```bash
# Terminal
docker exec -it project-server bash
```

Then we can use `npm` to install the `knex` library, along with the `pg` library for connecting to Postgres databases. This should be done in the `/app/server` directory inside the container:

```bash
# Docker project-server Terminal
npm install knex pg
```

{{% notice tip %}}

You can replace `pg` with the database engine of your choice. Modifications must be made below to match that engine; refer to the [Knex](https://knexjs.org/guide/) documentation.

{{% /notice %}}

Once Knex is installed, we'll run a few additional commands inside of the container to configure it.

## Create Knexfile

The Knex library uses a special file called a `knexfile` to store some configuration information. We can create it using this command:

```bash
# Docker project-server Terminal
npx knex init
```

{{% notice tip %}}

In the documentation for Knex it will reference these commands as just `knex`. However, since they are installed by an `npm` package, they are not directly accessible in the system path inside the container. So, to get around that, we can prefix the command with `npx` to run commands from within an `npm` package. [npx documentation](https://docs.npmjs.com/cli/v8/commands/npx)

{{% /notice %}}

This should create a file `server/knexfile.js`.

Once that is done, we can close the terminal connected to the container using the `exit` command.

We'll replace the default contents of the `server/knexfile.js` with the following:

```js
// server/knexfile.js

/**
 * @type { Object.<string, import("knex").Knex.Config> }
 */
module.exports = {
  development: {
    client: 'pg',
    connection: {
      host: process.env.POSTGRES_HOST,
      port: process.env.POSTGRES_PORT,
      user: process.env.POSTGRES_USER,
      password: process.env.POSTGRES_PASSWORD,
      database: process.env.POSTGRES_DB,
    },
    migrations: {
      tableName: 'migrations',
    },
  },

  production: {
    client: 'pg',
    connection: {
      host: process.env.POSTGRES_HOST,
      port: process.env.POSTGRES_PORT,
      user: process.env.POSTGRES_USER,
      password: process.env.POSTGRES_PASSWORD,
      database: process.env.POSTGRES_DB,
    },
    migrations: {
      tableName: 'migrations',
    },
  },
}
```

This will use environment variables to configure the database in both development and production modes.

## Pass Environment to Containers

Next we'll need to reconfigure our containers to pass the environment from the `.env` file. To do this, we'll edit the server service in the Docker Compose file:

```yaml {hl_lines="21-23"}
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
    env_file:
      # load environment file into container environment
      - .env
```

This will load the entire contents of the `.env` file into the container's environment. This is a bit different than how we handled the database container, since we only passed it a few variables. Refer to [Docker Compose Environment Documentation](https://docs.docker.com/compose/environment-variables/set-environment-variables/) for details.

## Recreate Container

Once we've made that change, we need to recreate our container with the new environment.

```bash
# Terminal
docker compose up -d
```

This will give us access to the information needed to connect to the database.

## Create Migration

We can use Knex to create a migration for our database. First, we must connect to it:

```bash
# Terminal
docker exec -it project-server bash
```

Then we can create a migration:

```bash
# Docker project-server Terminal
npx knex migrate:make users
```

{{% notice tip %}}

Use the same user ID and group ID found earlier when setting up the projects.

{{% /notice %}}

This will create a file in the `migrations` directory that includes a timestamp and the name of the migration. The timestamp is included to ensure the migrations are applied in the correct order. 

## Add Users Table

Now, open the file created in the `migrations` directory and add the following content:

```js
/**
 * @param { import("knex").Knex } knex
 * @returns { Promise<void> }
 */
exports.up = function(knex) {
  return knex.schema
    .createTable('users', function (table) {
      table.increments('id')
      table.string('username', 20).unique().notNullable()
      table.string('passhash', 60).notNullable()
      table.string('name', 255).notNullable()
      table.string('email', 255).notNullable()
      table.timestamps()
    })
};

/**
 * @param { import("knex").Knex } knex
 * @returns { Promise<void> }
 */
exports.down = function(knex) {
  return knex.schema
    .dropTable('users')
};

```

This will create a `users` table with a few columns. You can find more information about creating migrations with Knex in the [Schema Builder Documentation](https://knexjs.org/guide/schema-builder.html).

Once we've created that file, we can apply the migration using the `knex` command line tool:

```bash
# Docker project-server Terminal
npx knex migrate:up
```

If everything works correctly, you'll get a success message. You can check that the table exists in the database using various tools to query a Postgres database. 

## Create Seed

You can also use Knex to seed some initial data into the database. First, create a seed file from inside the project-server Docker container:

```bash
# Docker project-server Terminal
npx knex seed:make users_seed
```

This will create a file `users_seed.js` in the `seeds` directory. Add the following content to that file:

```js
/**
 * @param { import("knex").Knex } knex
 * @returns { Promise<void> } 
 */
exports.seed = async function(knex) {
  // Get current date
  const now = new Date().toISOString().slice(0, 19).replace('T', ' ')

  // Deletes ALL existing entries
  await knex('users').del()
  await knex('users').insert([
    {
      id: 1,
      username: 'admin',
      passhash: '$2y$10$41NXCocgYzuOGgOB4aL1M.wg7mZT.P6NY9D9EbfdKu8xbHU8x0b3O',
      name: "Administrator",
      email: "admin@local.local",
      created_at: now,
      updated_at: now,
    },
    {
      id: 2,
      username: 'user',
      passhash: '$2y$10$41NXCocgYzuOGgOB4aL1M.wg7mZT.P6NY9D9EbfdKu8xbHU8x0b3O',
      name: "User",
      email: "user@local.local",
      created_at: now,
      updated_at: now,
    }
  ]);
};
```

We can now apply that seed file using the `knex` command line tool:

```bash
# Docker project-server Terminal
npx knex seed:run
```

You should now be able to see a couple of entries in the `users` table of the database. You can check that these exist in the database using various tools to query a Postgres database. 

## Helpful Scripts

It is very helpful to have a script that will allow you to reset your database while working on it. I recommend adding the following `reset` to your `package.json` file for the server:

```json {hl_lines="4"}
  "scripts": {
    "dev": "nodemon ./bin/www",
    "start": "NODE_ENV=production node ./bin/www",
    "reset": "knex migrate:rollback --all && knex migrate:latest && knex seed:run"
  }
```

With that script in the `package.json` file, you can use the following command to fully reset the database back to the default state:

```bash
# Docker project-server Terminal
npm run reset
```

This will undo all database migrations, redo them, and seed the database with your initial seed data.

{{% notice note %}}

You may also wish to look into [Objection.js](https://vincit.github.io/objection.js/), which is a great ORM that works well with Knex. 

{{% /notice %}}