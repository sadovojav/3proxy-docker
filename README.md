# 3proxy-docker
Lightweight docker image with tiny 3proxy server. This configuration not has any restrictions of traffic.


### Very simple usage
When USER and PASS not specified, it will be create user with random password. You should see credentials in logs.
```console
docker pull timkolotov/3proxy-docker
docker run -d --restart always -p 1080:1080 -p 8080:8080 --name 3proxy timkolotov/3proxy-docker
docker logs 3proxy
```

### Simple usage
Run container with specified user and password
```console
docker run -d --restart always -p 1080:1080 -p 8080:8080 -e USER=userName -e PASS=secretPass --name 3proxy timkolotov/3proxy-docker
```

### Multiple users
Before run container, you should create passwd file with 3proxy users. This file has format:
```
userName1:CL:secretPass1
userName2:CL:secretPass2
```

```console
docker run -d --restart always -p 1080:1080 -p 8080:8080 -v /path_to/passwd:/etc/3proxy/passwd --name 3proxy timkolotov/3proxy-docker
```

### Example docker-compose.yml
```yaml
version: '2'

services:
  3proxy:
    image: timkolotov/3proxy-docker
    container_name: 3proxy
    restart: always
    environment:
      - HTTP_PORT=8080
      - SOCKS_PORT=1080
    volumes:
       - ./passwd:/etc/3proxy/passwd:ro
    ports:
      - "1080:1080"
      - "8080:8080"
```

### Available environment variables

* USER, PASS - preset USER with PASSword
* HTTP_PORT - set port for http proxy. Default: 8080
* SOCKS_PORT - set port for socks5 proxy. Default: 1080

