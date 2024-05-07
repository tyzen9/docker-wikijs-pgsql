<p align="right" width="100%">
    <img src="https://drive.usercontent.google.com/download?id=1KbYhPopR37y50wHRMne7FRKLLUN-usi1" height="100">
</p>

# Tyzen9's Wiki.js and PostgreSQL Docker Multi-Container
Linux based Docker container for [Wiki.js](https://js.wiki/) and Postgres. Wiki.js was written and is maintained by [Nicolas Giard](https://github.com/NGPixel) and community members.  

## Prerequisites
Install [Docker Engine](https://docs.docker.com/get-docker/) or [Docker Desktop](https://docs.docker.com/desktop/) if you require the Docker user interface.  In production it's generally best to use [Docker Engine](https://docs.docker.com/get-docker/) on a Linux host operating system.

This documentation assumes you have a working knowledge of [Docker](https://www.docker.com/), [Postgres](https://www.postgresql.org/), and [Wiki.js](https://js.wiki/).

## Configuration
This `docker-compose` implementation is configured using a `.env` file.  
The `.env` file contains comments explaining what each configuration value does.

1. Rename the `.env.sample` file to `.env`
2. Edit the `.env` file and populate the parameters according to your environment.

> :point_right:
> The `.env` file is where you can declare what versions of Wiki.js, and Postgres you desire.

If you desire the ability to connect to the Postgres instance from another machine (for example with pgAdmin), then uncomment the following lines in the `docker-compose.yml` file by removing the `#` characters:

```yaml
    # ports:
    #  - ${POSTGRES_EXTERNAL_PORT}:5432
```

## Start the Container (Development/Testing)
1. Clone this repository: `git clone https://github.com/stheisen/docker-wikijs-pgsql.git`
1. Rename the resulting directory to reflect the name of your site
1. Once the `.env` file is created and populated according to the needs of your development environment, using the command prompt navigate to the project's root directory and run the following command:

```
docker compose up
```

Visit the wiki at: `http://host_name::WIKIJS_EXTERNAL_PORT`;

## Postgres Backups
When running Postgres from a docker container, it is especially important to have regularly scheduled backups.  The following command, executed from the Docker host can be used to obtain a Postgres database dumps where `docker-wikijs-pgsql-db-1` is the "docker ID" of the Postgres instance. 

> :point_right:
> To get the Wiki.js container `DOCKER_CONTAINER_ID`, run this command from the host: `docker ps`

```
docker exec -i <DOCKER_CONTAINER_ID> pg_dump -U <POSTGRES_USER> -F t <POSTGRES_DATABASE> > db_backup.sql.tar
```

- Create a shell script containing the properly formatted command for your environment, and use a `root` cronjob to schedule these backups.
- The resulting `db_backup.sql.tar` file should be stored on a routinely backed up remove filesystem.

## Postgres Restore
1. From the host machine, rop the existing database, and create a new/empty database with the same name
``` 
docker exec -it <DOCKER_CONTAINER_ID> dropdb -U <POSTGRES_USER> <POSTGRES_DATABASE>
docker exec -it <DOCKER_CONTAINER_ID> createdb -U <POSTGRES_USER> <POSTGRES_DATABASE>
```

> :point_right:
> If you get an error that the database is being accessed by other users, then close all web browsers, and use these commands and try again:
>```
>docker container stop <DOCKER_CONTAINER_ID>
>docker container start <DOCKER_CONTAINER_ID>
>```

2. From the host, `cat` the database backup in to the pg_restore command
```
cat db_backup.sql.tar | docker exec -i <DOCKER_CONTAINER_ID> pg_restore -U <POSTGRES_USER> -d <POSTGRES_DATABASE>
```
3. Restart the compose instance
```
docker compose down
docker compose up
```
4. Visit the wiki at: `http://host_name::WIKIJS_EXTERNAL_PORT`;
