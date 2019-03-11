# Setting up a Postgresql Server

## Heroku Set-up

1. Create a new Heroku App
2. Install the add-on: `Heroku Postgres`
3. Open the Heroku Postgres add-on once installed.
4. In the add-on go to: Settings -> Database Credentials -> and Copy the URI. This will be your DATABASE_URL that you will use later on.
5. Inside your Heroku App(Not the Heroku add-on) go to settings -> Config Vars.
6. Set your DATABASE_URL to the Heroku Add-on URI
7. Any other variables you'll need such as JWT_SECRET will go here as well.

## Server Set-up

1. Install necessary dependencies

```
yarn add pg dotenv knex
```

2. Create a .env file and Set-up your .env variables

```
JWT_SECRET=there is no secret, it's all a lie
PORT=8000
DATABASE_URL='postgres://hshwwvecwtcttu:824db3ab6ec36681ee1e8a06879dfa3ba73e9f0dabc2745e6fd06430a01626de@ec2-54-235-134-25.compute-1.amazonaws.com:5432/d4atmhj2jqlram
'
```

3. Create a knexfile.js

```
knex init
```

4. Reference the knexfile.js config snippet below to see how your code should look. There are differences between development and production so keep that in mind.
5. Add boilerplating to the top of your knexfile.js

```
//Inside knexfile.js
require('dotenv').config();
const pg = require('pg');
pg.defaults.ssl = true;
```

6. Create your dbConfig.js file and add boilerplating.

```
//Inside dbConfig.js
require('dotenv').config();

const knex = require('knex');
const knexConfig = require('../knexfile.js');

const environment = process.env.ENVIRONMENT || 'development';

module.exports = knex(knexConfig[environment]);
```

Note: Any models you have will reference this dbConfig file and it will point to the heroku `DATABASE_URL` we established earlier.

### Snippets

- index.js config

```
require('dotenv').config();

const port = process.env.PORT || 5000;
server.listen(port, () => console.log(`\n** Running on port ${port} **\n`));
```

- .env config

```
JWT_SECRET=there is no secret, it's all a lie
PORT=8000
DATABASE_URL='postgresurl goes here'
'
```

- knexfile.js config

```
require('dotenv').config();
const pg = require('pg');
pg.defaults.ssl = true;

module.exports = {
  development: {
    client: 'sqlite3',
    useNullAsDefault: true,
    connection: {
      filename: './database/auth.db3',
    },
    pool: {
      afterCreate: (conn, done) => {
        conn.run('PRAGMA foreign_keys = ON', done);
      },
    },
    migrations: {
      directory: './database/migrations',
    },
    seeds: {
      directory: './database/seeds',
    },
  },
  production: {
    client: 'pg',
    connection: process.env.DATABASE_URL,
    pool: {
      min: 2,
      max: 10,
    },
    migrations: {
      tableName: 'knex_migrations',
      directory: './database/migrations',
    },
    seeds: {
      directory: './database/seeds',
    },
  },
};

```
