# Setting up local dev environment

## Prerequisites

Install the following components:

- docker
- docker-compose
- npm
- jq

To install `npm` on `linux` you may do the following:

```bash
npm i -g @nestjs/cli
```

## Init a NestJS project (test purposes)

In all the following the `REPO_DIR` env var will represent
the root of the `NestJS GIT repository`.

```bash
export PROJECT_NAME="test"
export REPO_PARENT_DIR="/work"
export REPO_DIR="/work/$PROJECT_NAME"
export PACKAGE_MANAGER="npm"
```

Now init a `NestJS` project:

```bash
cd $REPO_PARENT_DIR
nest new -p $PACKAGE_MANAGER $PROJECT_NAME
# -> It will automatically create a new dir
```

## Use your project

We will use the following variables:

```bash
export PROJECT_NAME="test"
export REPO_DIR="/work/$PROJECT_NAME"
export REPO_DIR_GAIA="/work/gaia"
export DB_NAME="myDB"
```

First copy docker-related files to your `NestJS` repo:

```bash
mkdir $REPO_DIR/docker
cp $REPO_DIR_GAIA/app/node/Dockerfile $REPO_DIR/docker/
cp $REPO_DIR_GAIA/app/node/docker-compose.yaml $REPO_DIR/docker/

sed -i "s:<CUSTOM_DB>:$DB_NAME:g" $REPO_DIR/docker/docker-compose.yaml
```

Then add the following commandto your `package.json`:

```bash
jq --arg "compose" "$cmd" '. * {"scripts": {"start:compose":$compose}}' $REPO_DIR/package.json > $REPO_DIR/package_2.json
mv $REPO_DIR/package_2.json $REPO_DIR/package.json
```

And then use it:

```bash
cd $REPO_DIR
npm run start:compose
```

## Troubleshoot your project

### Host test

You can try to build and launch your NestJS project on your host:

```bash
npm run build
node dist/main.js
```

And then request it:

```bash
curl http://localhost:3000
```

### Build the Docker image

```bash
docker build -t test:1 -f docker/Dockerfile .
```

### Launch the Docker image manually

```bash
docker run -p 3000:3000 test:1
```

### Launch docker-compose manually

```bash
docker-compose -f docker/docker-compose.yaml --project-directory . up
```

### Test database connection

Launch `docker-compose`.

Then get the api container id:

```bash
export API_ID=$(docker ps | grep api | cut -d ' ' -f 1)
```

**Careful:** If you have more than one **api** running the command won't work,
just do it by hand in that case.

```bash
docker exec -it $API_ID /bin/sh

$ nc -vv $DATABASE_HOST $DATABASE_PORT
#  -> Should answer something like "*open*"
```

### Test database setup

Launch `docker-compose`.

Then get the database container id:

```bash
export DATABASE_ID=$(docker ps | grep postgres | cut -d ' ' -f 1)
```

**Careful:** If you have more than one **database** running the command won't work,
just do it by hand in that case.

```bash
docker exec -it $DATABASE_ID /bin/bash

$ psql -U $POSTGRES_USER  $POSTGRES_DB
#  -> Should log you in the custom database
```
