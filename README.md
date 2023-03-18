# docker-wikijs-pgsql
Linux based Docker container for Wiki.js and PostgreSQL

Use this command to start the Docker compose
```
docker-compose up
```

> **Note**
> The Wiki and PostgreSQL containers are set to restart on server reboot so long as they were not stopped before the reboot takes place

## Wiki.js
- Wiki.js image from Docker Hub 
- Wiki.js will be accessable on the specified PORT number (8081)

```
    ports:
      - "8081:3000"
```
Wiki.js itself runs on port 3000 within the container.  This container listens to requests on port 8081 and then routes the request to port 3000. 

> **Note**
> Ideally, a reverse-proxy sits infront of the container routing traffic to the 8081 port.


## PostgreSQL
- PostgreSQL image from Docker Hub

> **Note**
>  Consider a schedueld task to backup the data to the host computer, and then onto a cloud backup provider.

### Backup DB Command
This command can be used to obtain PostgreSQL database dumps where `docker-wikijs-pgsql-db-1` is the "docker name". 

```
   > docker exec -i docker-wikijs-pgsql-db-1 pg_dump -U wikijs -F t wiki > wiki-pgsql-backup.tar
```

### Restore DB Commands

1. Drop the existing database, and create a new one
``` 
    > docker exec -it docker-wikijs-pgsql-db-1 dropdb -U wikijs wiki
    > docker exec -it docker-wikijs-pgsql-db-1 createdb -U wikijs wiki
```
2. Make a directory in the container to hold the backup
``` 
    > docker exec -i docker-wikijs-pgsql-db-1 mkdir /backups
```
3. Copy the dump file to the container
```
    > docker cp .\wikijs_pg_dump.tar docker-wikijs-pgsql-db-1:/backups/.
```
4. Restore the backup in the container
```
    > docker exec -i docker-wikijs-pgsql-db-1 pg_restore -U wikijs -d wiki /backups/wikijs_pg_dump.tar
```