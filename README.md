# PostgreSQL with ZomboDB extension

This repo contains a simple Dockerfile that builds a PostgreSQL
server with the ZomboDB extension. [zombodb](https://github.com/zombodb/zombodb) 
is a Postgresql
extension that combines Elasticsearch with Postgresql to enable
full text indexing, search, and aggregation from postgresql based 
on all the elasticsearch goodies.

```
# in psql
create index abc_zombodb_index 
  on abc using zombodb((abc.*))
  with (url='https://user:password@host:9243/');
# insert some data
...
# and query
select * from abc where abc ==> '(brown AND fox) OR (cat AND black)';
```


# Quickstart

```
git clone https://github.com/seandavi/postgresql_zombodb_docker.git
cd postgresql_zombodb_docker
docker build -t pg_zombodb .
# create a docker volume to persist data
docker volume create pgdata
docker run -p 5434:5432 -v pgdata:/var/lib/postgresql/data -d pg_zombodb
```

Assuming that `psql` is installed on your local machine and that 
docker is running locally (not `docker-machine`, for example),
connect to the running docker instance:

```
# now, connect to running database
psql -h localhost -U postgres -p 5434
```


## Building

To match postgres 11 with zombodb, one must currently (April 22, 2019)
use the `develop` branch. See the `Dockerfile` for details, but it is
pretty simple to build. 

## Creating the extension

To create the extension after successful `make` and `make install`,
run the following SQL:

```
create extension zombodb;
```

This statement can also be added to a file and dropped into the
container. See the `COPY` statement in the `Dockerfile` for details.

## Running the container

The [official postgres docker](https://hub.docker.com/_/postgres/) containers allow several environment
variables to control aspects of creation of the server.

- POSTGRES_USER (default postgres)
- POSTGRES_DB (default postgres)
- PGDATA (where the data are stored)
- POSTGRES_PASSWORD (SHOULD ALWAYS BE SET)

```
docker run -p 5433:5432 -it -e POSTGRES_PASSWORD=my_password postgres:11.2
```

Or run as a daemon:

```
docker run -p 5433:5432 -it -e POSTGRES_PASSWORD=my_password -d postgres:11.2
```

# zombodb

- [Tutorial](https://github.com/zombodb/zombodb/blob/master/TUTORIAL.md)
- [Index creation and management](https://github.com/zombodb/zombodb/blob/master/INDEX-MANAGEMENT.md)
- [Indexes in depth](https://github.com/zombodb/zombodb/blob/master/CREATE-INDEX.md)
- [Type mapping](https://github.com/zombodb/zombodb/blob/master/TYPE-MAPPING.md)
  - varchar => keyword
  - text => text

# quay.io

```
docker tag pg_zombodb quay.io/seandavi/postgres_zombodb
docker push quay.io/seandavi/postgres_zombodb
```
