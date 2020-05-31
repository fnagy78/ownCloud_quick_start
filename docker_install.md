Installing the ownCloud server with docker
===

This quick start procedure is a short version of [Docker installation](https://doc.owncloud.org/server/10.4/admin_manual/installation/docker/).

The official ownCloud docker image works standalone, but it is designed to work with a data volume in the host filesystem and with separate MariaDB and Redis containers.

The configuration exposes port 8080 for HTTP connections, and mounts the data and MySQL data directories on the host for persistent storage.

To install ownCloud with docker on a local machine, follow these steps:

1. Create a new project directory and enter the directory. For example:

    ```
    mkdir owncloud-docker-server  
    cd owncloud-docker-server 
    ```
2. Download ownCloud’s [docker-compose.yml](https://raw.githubusercontent.com/owncloud/docs/master/modules/admin_manual/examples/installation/docker/docker-compose.yml) file into the new directory.
    ```
    wget https://raw.githubusercontent.com/owncloud/docs/master/modules/admin_manual/examples/installation/docker/docker-compose.yml
    ```
3. Create a _.env_ envrionment configuration file with your required configuration settings:

    | Setting Name     | Description               | Example   |
    |------------------|---------------------------|-----------|
    | OWNCLOUD_VERSION | The ownCloud version      | latest    |
    | OWNCLOUD_DOMAIN  | The ownCloud domain       | localhost |
    | ADMIN_USERNAME   | The admin username        | admin     |
    | ADMIN_PASSWORD   | The admin user’s password | admin     |
    | HTTP_PORT        | The HTTP port to bind to  | 8080      |

    For example:

    ```
    cat << EOF > .env
    OWNCLOUD_VERSION=10.5
    OWNCLOUD_DOMAIN=localhost
    ADMIN_USERNAME=admin
    ADMIN_PASSWORD=admin
    HTTP_PORT=8080
    EOF
    ```

4. Build and start the container. For example: 
    ```
    docker-compose up -d
    ```
5. Check that all the containers have started.
    ```
    docker-compose ps
    ```
    Check that the command returns an output similar to the following:
    ```
    Name                Command                       State             Ports
    __________________________________________________________________________________________
    server_db_1         /usr/bin/entrypoint/bin/s …   Up                3306/tcp
    server_owncloud_1   /usr/local/bin/entrypoint …   Up                0.0.0.0:8080->8080/tcp
    server_redis_1      /bin/s6-svscan /etc/s6        Up                6379/tcp  
    ```
