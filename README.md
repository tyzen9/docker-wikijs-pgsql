# docker-wikijs-pgsql
Docker container for wiki.js and PostgreSQL

### Wiki.js
- The 2.x image of Wiki.js
- Wiki.js will be accessable on the specified PORT number (8081)

```
    ports:
      - "8081:3000"
```
Wiki.js itself runs on port 3000.  This container listens to requests on port 8081 and then routes the request to port 3000. 

**Note**
Ideally, a reverse-proxy sits infront of the container routing traffic to the 8081 port.