# [EspoCRM with Docker](https://docs.espocrm.com/administration/docker/installation/#espocrm-with-docker)

In this article:

- [Installing with Docker](https://docs.espocrm.com/administration/docker/installation/#install-espocrm-with-docker)
- [Installing with Docker Compose](https://docs.espocrm.com/administration/docker/installation/#install-espocrm-with-docker-compose)
- [Installing with Traefik](https://docs.espocrm.com/administration/docker/installation/#install-espocrm-with-traefik)
- [Upgrading](https://docs.espocrm.com/administration/docker/installation/#upgrading)
- [Shutdown and cleanup](https://docs.espocrm.com/administration/docker/installation/#shutdown-and-cleanup-containers)
- [Running a shell](https://docs.espocrm.com/administration/docker/installation/#running-a-shell)
- [Environments](https://docs.espocrm.com/administration/docker/installation/#installation-environments)
- [Config Environments](https://docs.espocrm.com/administration/docker/installation/#config-environments)
- [Image Variants](https://docs.espocrm.com/administration/docker/installation/#image-variants)



# [HubDocker](https://hub.docker.com/r/espocrm/espocrm/tags)

- Docker-compose.yaml

```yaml
version: '3.8'

services:

  mysql:
    image: mysql:8
    container_name: mysql
    command: --default-authentication-plugin=mysql_native_password
    environment:
      MYSQL_ROOT_PASSWORD: root_password
      MYSQL_DATABASE: espocrm
      MYSQL_USER: espocrm
      MYSQL_PASSWORD: database_password
    volumes:
      - mysql:/var/lib/mysql
    restart: always

  espocrm:
    image: espocrm/espocrm
    container_name: espocrm
    environment:
      ESPOCRM_DATABASE_PLATFORM: Mysql
      ESPOCRM_DATABASE_HOST: mysql
      ESPOCRM_DATABASE_USER: espocrm
      ESPOCRM_DATABASE_PASSWORD: database_password
      ESPOCRM_ADMIN_USERNAME: admin
      ESPOCRM_ADMIN_PASSWORD: password
      ESPOCRM_SITE_URL: "http://localhost:8080"
    volumes:
      - espocrm:/var/www/html
    restart: always
    ports:
      - 8080:80

  espocrm-daemon:
    image: espocrm/espocrm
    container_name: espocrm-daemon
    volumes:
      - espocrm:/var/www/html
    restart: always
    entrypoint: docker-daemon.sh

  espocrm-websocket:
    image: espocrm/espocrm
    container_name: espocrm-websocket
    environment:
      ESPOCRM_CONFIG_USE_WEB_SOCKET: "true"
      ESPOCRM_CONFIG_WEB_SOCKET_URL: "ws://localhost:8081"
      ESPOCRM_CONFIG_WEB_SOCKET_ZERO_M_Q_SUBSCRIBER_DSN: "tcp://*:7777"
      ESPOCRM_CONFIG_WEB_SOCKET_ZERO_M_Q_SUBMISSION_DSN: "tcp://espocrm-websocket:7777"
    volumes:
      - espocrm:/var/www/html
    restart: always
    entrypoint: docker-websocket.sh
    ports:
      - 8081:8080

volumes:
  mysql:
  espocrm:
```

