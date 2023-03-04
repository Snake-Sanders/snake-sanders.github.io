# Replacing the local installation of PostgreSQL with Docker

Here we are going to stop using the local installation of Postgresql and insted 
we are going to use a Docker container with Postgresql.
This will allow us to dynamically switch to different version of Postgresql without having to uninstall, install, and configure it each time.

### Install Docker on Linux Mint

Before installing Docker is a good moment to watch this excellent [Docker tutorial](https://www.youtube.com/watch?v=pg19Z8LL06w).

Now you are redy to install Docker.

`sudo apt install docker.io`

Then add yourself to the docker group:

`sudo usermod -a -G docker $(whoami)`

restart Docker service (restart)

`sudo systemctl status docker`

The next command should show a list of containers running:

`docker ps`

Having issues at this point? check the [original guide](https://gist.github.com/cstroe/d56256091f98e836c1a3de59a66aaa72).

Try to download and run a Docker image:

`sudo docker run hello-world`

### Download a Docker image

In this guide postgis will be use instead of Postgresql, but the process is the same:

1. Go to Docker Hub and search for the `image` and select a `tag`

https://hub.docker.com/r/postgis/postgis

2. download the image with the formula `docker pull <name>:<tag>`:

`docker pull postgis/postgis:13-3.3-alpine`

3. check the image is avalable with the command: `docker images`

4. Try to run it by using the port binding (-p), detached (-d)

`docker run -d
  -p 4444:5432 
  -e POSTGRES_PASSWORD=postgres 
  postgis/postgis:13-3.3-alpine`

Here we expose the postgresql port `5432` as `4444` to avoid conflict with the local postgresql instance which also runs on `5432`. 

5. check the process is running with `docker ps`

### Stop the local instance of PostgreSQL to automatically start at boot

On Ubuntu 16.04 (or Mint Cinammon 20.x) or later (which use systemd) you'd use this command to **disable** it:

`sudo systemctl disable postgresql`

If you want to **re-enable**, you'd do

`sudo systemctl enable postgresql`

**restart the OS**

### Connect to Docker

Now that the port `5432` is free we can run Docker using Postgresql default port `5432`:

`docker run -d
  -p 5432:5432 
  -e POSTGRES_PASSWORD=postgres 
  postgis/postgis:13-3.3-alpine`

You can use a Databaser manager like [DBeaver](https://dbeaver.io/) to connect to the database via Docker.

## Resources

### Learn about Docker

https://www.youtube.com/watch?v=pg19Z8LL06w

### Install Docker on Linux Mint

https://gist.github.com/cstroe/d56256091f98e836c1a3de59a66aaa72
