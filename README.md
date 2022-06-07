# guacamole ARM64

Based on project https://github.com/oznu/docker-guacamole

This project use the new arm/v8 architecture rather than v6 used in based repo.

## Build guacamole-server (guacd)

```shell
docker build \
  --platform linux/arm/ \
  . \
  -f Dockerfile.guacd.arm64 \
  --platform linux/arm64/v8 \
  -t bauxogeno/guacamole-arm-build:latest
```
### Start container
```shell
docker run \
    bauxogeno/guacamole-arm-build:latest
```

----------
## Build web image (tomcat+postgres)
```shell
 docker build \
  --platform linux/arm64/v8 \
  . \
  -f Dockerfile.web.arm64 \
  -t bauxogeno/guac-web-arm64:latest
```

----------

## Start container

### Using docker
create folder ./data/config
```shell
mkdir -p ./data/config
```

Than launch

```shell
docker run \
    -p 8180:8080 \
    -v </path/to/config>:/config \
    -e "EXTENSIONS=auth-duo" \
    bauxogeno/guacamole:latest

docker run \
    -p 8180:8080 \
    -v ${PWD}/data/config:/config \
    -e "EXTENSIONS=auth-duo" \
    bauxogeno/guacamole:latest
```

#### Parameters

The parameters are split into two halves, separated by a colon, the left hand side representing the host and the right the container side.

* `-p 8180:8080` - Binds the service to port 8080 on the Docker host, **required**
* `-v </path/to/config>:/config` - The config and database location, **required**
* `-e EXTENSIONS` - See below for details.

### Using docker-compose

##### full run
```shell
docker-compose -f docker-compose-full.yaml up
```
---

create docker-compose.yaml file and add the line below

```docker
version: '3.5'

## proxynet attached to nginx network
networks:
  proxynet:
    name: nginx_default

services: 
  guacamole:
    container_name: compose_guacamole  
    image: bauxogeno/guacamole:latest
    ports: 
      - 8180:8080
    volumes: 
      - ./data/config/:/config/:rw
    environment: 
      - EXTENSIONS=auth-duo
    networks:
      - proxynet

```
Than run it with

```shell
docker-compose -up
```