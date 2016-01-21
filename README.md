# Minecraft - Vanilla Server

Forked from dlord/minecraft

* * *


## About this image

This Docker image allows you to run a Vanilla Minecraft server quickly. It
also serves as the base image for some of my Modded Minecraft server images.


## Base Docker image

* java:8


## How to use this image

### Starting an instance

    docker run -d \
        --name minecraft-instance \
        -p 25565:25565 \
        -e DEFAULT_OP=username \
        -e MINECRAFT_EULA=true \
        -v /srv/minecraft-map:/opt/minecraft/world
        max13fr/minecraft

By default, this starts up a Minecraft 1.8.1 server instance. If you wish to
start a different Minecraft server version, you need to set the
`MINECRAFT_VERSION` variable to the appropriate version.

You must set the `DEFAULT_OP` variable on startup. This should be your
Minecraft username. The container will fail to run if this is not set.

When starting a Minecraft server, you must agree to the terms stated in
Minecraft's EULA. This can be done by setting the `MINECRAFT_EULA` variable
to `true`. Without this, the server will not run.

This image exposes the standard minecraft port (25565).

When starting a container for the first time, it will check for the existence of
the Minecraft Server jar file, and will download from Mojang when necessary. As
much as I want to package the Minecraft server jar in this image (to also save
on time and the hassle of an extra step), I cannot due to the [Minecraft EULA][]. 


### Data volumes

This image has one data volume: `/opt/minecraft`. This volume contains world
data. This is a deliberate decision in order to support building Docker images
with a world template (useful for custom maps).

Please do a chmod 700 & chown 1000 on volume to allow writing.


### Environment Variables

The image uses environment variables to configure the JVM settings and the
server.properties.

**MINECRAFT_EULA**

`MINECRAFT_EULA` is required when starting creating a new container. You need to
agree to Minecraft's EULA before you can start the Minecraft server.

**DEFAULT_OP**

`DEFAULT_OP` is required when starting creating a new container.

**MINECRAFT_OPTS**

You may adjust the JVM settings via the `MINECRAFT_OPTS` variable.

**server.properties**

Each entry in the `server.properties` file can be changed by passing the
appropriate variable. To make it easier to remember and configure, the variable
representation of each entry is in uppercase, and uses underscore instead
of dash.

The server port cannot be changed. This has to be remapped when starting an
instance.

For reference, here is the list of environment variables for `server.properties`
that you can set:

* GENERATOR_SETTINGS
* OP_PERMISSION_LEVEL
* ALLOW_NETHER
* LEVEL_NAME
* ENABLE_QUERY
* ALLOW_FLIGHT
* ANNOUNCE_PLAYER_ACHIEVEMENTS
* LEVEL_TYPE
* ENABLE_RCON
* FORCE_GAMEMODE
* LEVEL_SEED
* SERVER_IP
* MAX_BUILD_HEIGHT
* SPAWN_NPCS
* WHITE_LIST
* SPAWN_ANIMALS
* SNOOPER_ENABLED
* ONLINE_MODE
* RESOURCE_PACK
* PVP
* DIFFICULTY
* ENABLE_COMMAND_BLOCK
* PLAYER_IDLE_TIMEOUT
* GAMEMODE
* MAX_PLAYERS
* SPAWN_MONSTERS
* VIEW_DISTANCE
* GENERATE_STRUCTURES
* MOTD


## Extending this image

This image is meant to be extended for packaging custom maps and modpacks as
Docker images.

If you wish to do so, here are some things you will need to know:

### `MINECRAFT_HOME`

This is where all server related artifacts go, and its default location is
`/opt/minecraft`. If you are building a Docker image for a modpack, you will
want to copy the modpack distribution here.

This Docker image supports the use of world templates, which is useful for
custom maps. You will want to copy your world template to `MINECRAFT_HOME/world`.
During startup, it will check if `/var/lib/minecraft` is empty. If so, it will
create a copy of the world template on this folder.

### `MINECRAFT_VERSION`

Modpacks will require a specific Minecraft version in order to work. This can
be done setting the `MINECRAFT_VERSION` in your Dockerfile.

### `MINECRAFT_STARTUP_JAR`

When packaging a modpack, you will need to start the server using a different
jar file. To specify the startup jar, set the `MINECRAFT_STARTUP_JAR` variable
in your Dockerfile.

### `MINECRAFT_OPTS`

Some modpacks have their own recommended JVM settings. You can include them
via the `MINECRAFT_OPTS` variable in your Dockerfile.

### Data Volumes

It is good practice to declare the `MINECRAFT_HOME` folder as a data volume on
your Dockerfile. This makes it easier for server admins to customize the
modpack/custom map.

