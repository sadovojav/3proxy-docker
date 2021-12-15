# 3proxy-docker
Lightweight docker image with tiny 3proxy server. This configuration doesn't have any restrictions on traffic.


### Very simple usage
When USER and PASS are not specified, it will create a user with a random password. You will see the credentials in the logs.
```console
docker pull timkolotov/3proxy-docker
docker run -d --restart always -p 1080:1080 -p 8080:8080 --name 3proxy timkolotov/3proxy-docker
docker logs 3proxy
```

### Simple usage
Run a container with a specified user and password
```console
docker run -d --restart always -p 1080:1080 -p 8080:8080 -e USER=userName -e PASS=secretPass --name 3proxy timkolotov/3proxy-docker
```

### Multiple users
Before running a container, you must create a passwd file with 3proxy users. This file has the following format:
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
      - SOCKS_PORT=7835
    volumes:
       - ./passwd:/etc/3proxy/passwd:ro
    ports:
      - "7835:7835"
      - "8080:8080"
```

### Available environment variables

* USER, PASS - preset USER with PASSword
* HTTP_PORT - set port for http proxy. Default: 8080
* SOCKS_PORT - set port for socks5 proxy. Default: 7835

### Disable ping responses from CentOS/RHEL system?

1. On the server where you need to disable ping responses, review the current setting related to responses to ping messages with the following command:
```
cat /proc/sys/net/ipv4/icmp_echo_ignore_all
0
```
2. Assuming the output is a 0, try the ping localhost command. You should get a response to the ping requests. Don’t forget to press CTRL-C to exit from the output stream. If the output is 1, skip to Step 3.
```
ping localhost
PING localhost (127.0.0.1) 56(84) bytes of data. 
64 bytes from localhost (127.0.0.1): icmp_seq=1 ttl=64 time=0.060 ms
```
3. If the output to above command is not 1 then, change the kernel setting described in Step 1 with the following command:
```
echo "1" > /proc/sys/net/ipv4/icmp_echo_ignore_all
```

#### Making the changes persistent
```
vi /etc/sysctl.conf 
# Ignore all to ICMP (ping) 
net.ipv4.icmp_echo_ignore_all = 1
```
To verify the parameter set use the below command:
```
sysctl -a | grep "icmp_echo_ignore_all"
net.ipv4.icmp_echo_ignore_all = 1
```
