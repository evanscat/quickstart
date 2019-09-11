#MYSQL 

##build image with init script

e.g.

```dockerfile
FROM mysql:latest
MAINTAINER evanscat(evanscat@163.com)
COPY init/* /docker-entrypoint-initdb.d/
```

```shell
docker build -t  evanscat/mysql .
```

##docker 

- **create and run container with docker** 

```shell
docker run \
-p 3066:3066 \
-v $PWD/mysql/data:/var/lib/mysql \
-v $PWD/mysql/conf:/etc/mysql \
-e MYSQL_ROOT_PASSWORD=admin  \
--name mysql \
-d my/mysql
```

- **stop  container ** 

```shell
docker stop `docker ps|grep mysql|cut -d ' ' -f 1`
```

##docker-compose 

- **create and run container with docker-compose**

```shell
docker-compose up -d
```

## Other 

- **change password encrypt ** 

```sql
CREATE USER 'root'@'%' IDENTIFIED BY '123456';

ALTER USER 'root'@'%' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER; 

ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';

flush privileges;

```

### 
