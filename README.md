# Postgres Docker Project Template, Step-CA Compatible

___

## Prerequisite

Understand the process of creating docker containers using the [docker-image](https://github.com/design-group/ignition-docker).

This project is compatible *with* and *without* Traefik. You can set up the Traefik Proxy using [this repository.](https://github.com/design-group/traefik-proxy)

___

## Setup

1. Follow [this guide from Github](https://docs.github.com/en/repositories/creating-and-managing-repositories/creating-a-repository-from-a-template) to create a new repository from the template.
2. Create a new directory for your project and clone the repository into it.

    ```sh
    mkdir <project-name>
    cd <project-name>
    git clone https://github.com/design-group/ignition-postgres-architecture-template.git .
    ```

3. Rename the vscode workspace file to match your project name.

    ```sh
    mv ignition-project-template.code-workspace <project-name>.code-workspace
    ```

4. Review the `docker-compose.yml` file to verify the container structure is correct
5. If you are using Traefik, go through the `docker-compose.traefik.yml` file and change all instances of `<desired-address>` to your desired web address.

   ```sh
     services:
       gateway:
         healthcheck:
           disable: true
         labels:
           traefik.enable: "true"
           traefik.hostname: <desired-address>
         environment:
           GATEWAY_SYSTEM_NAME: <desired-address>
         networks:
           - default
           - proxy
	```
6. Review the `.gitignore` file to add any additional directories and contents to ignore.
7. To name the compose project that will be built, edit the `.env` file and set the `COMPOSE_PROJECT_NAME` variable to the name of your project.

   	```sh
    COMPOSE_PROJECT_NAME=<project-name>
	```

   If you are using Traefik, uncomment the following lines in the `.env` file:

	```sh
	COMPOSE_PATH_SEPARATOR=:
	COMPOSE_FILE=docker-compose.yml:docker-compose.traefik.yml
	```

8. If mounting the `workdir` volume on a non-MacOS device, make sure to create the directory first so that it is owned by the user running the container.

	```sh
	mkdir ignition-data
	```

9. Pull any changes to the docker image and start the container.
      
    On Mac:
    
	```sh
    docker compose pull && docker compose up -d
    ```
    
	On WSL
    
	```sh
    docker-compose pull && docker-compose up -d
    ```

10. Whether or not you are using Traefik, you can access the gateway at [http://localhost:9080/](http://localhost:9080/)

11. If you are using Traefik, you can also access the gateway at `http://<desired-address>.localtest.me`


___

## Pre-Configured Database

The template is pre-configured with a postgres database, that can be setup with an `init.sql` script to create the database and tables.

To setup the database, add your `init.sql` script to the `ddl` directory, and uncomment the volume mount in the database. The script will be run when the container is first created, and not as it is stopped and started.

___

## Gateway Config

The gateway configuration is stored in stripped gateway backups, that have all projects removed. In order to get these backups run the following command:

```sh
bash download-gateway-backups.sh
```

The backups are stored in the `backups` directory, and if configured in the compose file will automatically restore when the container is initially created. 

If you are preparing this repository for another user, after configuration of the gateway is complete, run the above backup command, and then uncomment the volume and command listed in the `docker-compose.yml` file.
