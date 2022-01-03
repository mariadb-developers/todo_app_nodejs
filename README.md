# TODO

**TODO** is a web application that introduces you to the power, performance, and simplicity of [MariaDB](https://mariadb.com/products/).

<p align="center" spacing="10">
    <kbd>
        <img src="media/demo.gif" />
    </kbd>
</p>

This README will walk you through the steps for getting the TODO web application up and running using MariaDB.

# Table of Contents
1. [Requirements](#requirements)
2. [Getting started with MariaDB](#mariadb)
3. [Get the code](#code)
4. [Create the database and table](#schema)
5. [Configure, build and run the app](#app)
    1. [Configure](#configure-app)
    2. [Build and run](#build-run)
6. [Support and contribution](#support-contribution)
7. [License](#license)

## Requirements <a name="requirements"></a>

This sample application requires the following to be installed/enabled on your machine:

* [Node.js (v. 12+)](https://nodejs.org/docs/latest-v12.x/api/index.html)
* [NPM (v. 6+)](https://docs.npmjs.com/)
* [MariaDB command-line client](https://mariadb.com/products/skysql/docs/clients/mariadb-clients/mariadb-client/) (optional), used to connect to MariaDB database instances.

## 1.) Getting Started with MariaDB <a name="mariadb"></a>

[MariaDB](https://mariadb.com) is a community-developed, commercially supported relational database management system, and the database you'll be using for this application.

If you don't have a MariaDB database up and running you can find more information on how to download, install and start using a MariaDB database in the [MariaDB Quickstart Guide](https://github.com/mariadb-developers/mariadb-getting-started).

## 2.) Get the code <a name="code"></a>

Download this repo directly or use [git](git-scm.org) (through CLI or a client) to retrieve the code using `git clone`:

```
$ git clone https://github.com/mariadb-developers/todo_app_nodejs.git
```

## 3.) Create the database and table <a name="schema"></a>

[Connect to your MariaDB database](https://mariadb.com/products/skysql/docs/clients/) (from [Step #2](#mariadb)) and execute the following SQL scripts using the following options:

a.) Use the [MariaDB command-line client](https://mariadb.com/products/skysql/docs/clients/mariadb-clients/mariadb-client/) to execute the SQL contained within [schema.sql](schema.sql).

_Example command:_
```bash
$ mariadb --host HOST_ADDRESS --port PORT_NO --user USER --password PASSWORD < schema.sql
```

**OR**

b.) Copy, paste and execute the raw SQL commands contained in [schema.sql](schema.sql) using a [client of your choice](https://mariadb.com/products/skysql/docs/clients/).

```sql
CREATE DATABASE todo;

CREATE TABLE todo.tasks (
  id INT(11) unsigned NOT NULL AUTO_INCREMENT,
  description VARCHAR(500) NOT NULL,
  completed BOOLEAN NOT NULL DEFAULT 0,
  PRIMARY KEY (id)
);
```

## 4.) Configure, Build and Run the App <a name="app"></a>

This application is made of two parts:

* Client
    - communicates with the API.
    - is a React.js project located in the [client](src/client) folder.
* API
    - uses the MariaDB Node.js Connector to connect to MariaDB.
    - is a Node.js project located int the [api](src/api) folder.

### a.) Configure the app <a name="configure-app"></a>

Configure the MariaDB connection by [adding an .env file to the Node.js project](https://github.com/mariadb-corporation/mariadb-connector-nodejs/blob/master/documentation/promise-api.md#security-consideration). Add the .env file [here](src/api) (the `api` folder).

Example implementation:

```
DB_HOST=<host_address>
DB_PORT=<port_number>
DB_USER=<username>
DB_PASS=<password>
DB_NAME=todo
```

**Configuring db.js**

The environmental variables from `.env` are used within the [db.js](src/api/db.js) for the MariaDB Node.js Connector configuration pool settings:

```javascript
var mariadb = require('mariadb');
require('dotenv').config();

const pool = mariadb.createPool({
    host: process.env.DB_HOST, 
    user: process.env.DB_USER, 
    password: process.env.DB_PASS,
    port: process.env.DB_PORT,
    database: process.env.DB_NAME,
    multipleStatements: true,
    connectionLimit: 5
});
```

**Configuring db.js for the MariaDB cloud database service [SkySQL](https://mariadb.com/products/skysql/)**

MariaDB SkySQL requires SSL additions to connection. It's as easy as 1-2-3 (steps below).

```javascript
var mariadb = require('mariadb');
require('dotenv').config();

// 1.) Access the Node File System package
const fs = require("fs");

// 2.) Retrieve the Certificate Authority chain file (wherever you placed it - notice it's just in the Node project root here)
const serverCert = [fs.readFileSync("skysql_chain.pem", "utf8")];

var pools = [
  mariadb.createPool({
    host: process.env.DB_HOST, 
    user: process.env.DB_USER, 
    password: process.env.DB_PASS,
    port: process.env.DB_PORT,
    database: process.env.DB_NAME,
    connectionLimit: 5,
    // 3.) Add an "ssl" property to the connection pool configuration, using the serverCert const defined above
    ssl: {
      ca: serverCert
    }
  })
];
```

### b.) Build and run the app <a name="build-run-app"></a>

Within separate terminal windows perform the following steps:

1. Install the Node.js packages (dependendies) for the [client](src/client) and [api](src/api) apps using the `npm install` command.

_For example:_
```bash
$ npm install ./src/client
$ npm install ./src/api
```

2. Run the [client](src/client) and [api](src/api) apps using the `npm start` command.

_For example:_
```bash 
$ npm start --prefix ./src/api
$ npm start --prefix ./src/client
``` 

**Note:** You will need to use seperate terminals for the `client` and `api` apps.

## Support and Contribution <a name="support-contribution"></a>

Please feel free to submit PR's, issues or requests to this project project directly.

If you have any other questions, comments, or looking for more information on MariaDB please check out:

* [MariaDB Developer Hub](https://mariadb.com/developers)
* [MariaDB Community Slack](https://r.mariadb.com/join-community-slack)

Or reach out to us diretly via:

* [developers@mariadb.com](mailto:developers@mariadb.com)
* [MariaDB Twitter](https://twitter.com/mariadb)

## License <a name="license"></a>
[![License](https://img.shields.io/badge/License-MIT-blue.svg?style=plastic)](https://opensource.org/licenses/MIT)
