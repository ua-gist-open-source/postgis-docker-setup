# Assignment: PostGIS Docker Setup

## Objective: Run postgis in a docker container with sample data

In this assignment you will setup geoserver to run in a docker container on your local machine and configure the docker container to use your local disk as a volume to serve the geoserver sample data. 

## Background
PostGIS is an extension to the open source database, PostgreSQL. PostgreSQL, or “postgres”, is released under a special license called the “PostgreSQL License”:

PostgreSQL is released under the PostgreSQL License, a liberal Open Source license, similar to the BSD or MIT licenses.
```
PostgreSQL Database Management System
(formerly known as Postgres, then as Postgres95)

Portions Copyright © 1996-2018, The PostgreSQL Global Development Group

Portions Copyright © 1994, The Regents of the University of California

Permission to use, copy, modify, and distribute this software and its documentation for any purpose, without fee, and without a written agreement is hereby granted, provided that the above copyright notice and this paragraph and the following two paragraphs appear in all copies.
```
PostGIS is released under the GNU General Public License (v2 or later):

``` GNU GENERAL PUBLIC LICENSE
Version 2, June 1991

Copyright (C) 1989, 1991 Free Software Foundation, Inc.
51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA

Everyone is permitted to copy and distribute verbatim copies
of this license document, but changing it is not allowed.
```

In a typical scenario, PostGIS would be serving data static and dynamic spatial vector data, providing them to both clients (e.g., QGIS) and middleware (e.g., geoserver). It  would be run as a server on dedicated server hardware, a VM, or a container in a container management service. For the purposes of this class, you will run PostGIS in a docker container on your workstation. This has a number of benefits:
1) It is a clean copy of PostGIS for your exclusive use
2) As a container, the environment will be consistent every time
3) No need to provision dedicated server hardware to run idle when not in use
4) Gain familiarity with docker

## Prerequisites
1) Docker is installed
2) PgAdmin is installed (https://www.pgadmin.org/download/)

## Assignment

### 1) Pull the postgis docker container.

Lie geoserver, we will use the `kartoza` build:


Pull the image as in the instructions to download the latest version:

```
docker pull kartoza/postgis
```

Run the docker container to give yourself a quick test that you have a working container:

```
docker run -p 25432:5432 kartoza/geoserver
```

This will run postgis in a container and expose the port `5432` from the docker container as `25432` on your host system (i.e., your Desktop or laptop). It will take a minute for postgresql to start up. Watch the terminal output for a message indicating that Postgresql is running; it will look like this: 
```
2020-11-18 00:31:32.231 UTC [305] LOG:  database system is ready to accept connections
```

Connect to localhost, port 5432 with a postgres host. Use  `pgadmin` for this class.

### 2) Configure postgis to store data on the host rather than the container.

We want to use a local directory to serve sample data through geoserver but in order to do that, we need to do a little housecleaning and prep first.

### 3) Kill the running container from step 1). 
To kill the running container, look at the running docker processes:
```
docker ps
```
Copy the CONTAINER ID for the postgis IMAGE and use it to `rm` the container:
```
docker rm -f CONTAINER_ID
```
Use `docker ps` to see its status and make sure it is not running.

### 4) Run kartoza/geoserver with a local volume
Finally, run the docker container with the volume switch mapping your local directory to the postgis data directory on the container. The important switch here is `-v $HOME/postgres_data:/var/lib/postgresql` which maps the directory, `$HOME/postgres_data` on your local computer to the `/var/lib/postgresql` in the container, which is where postgres looks to find its configuration and data files. Your path will likely be different. If you wanted to use a shared network drive named `c:/postgres_data:/var/lib/postgresql`, for example, it might look like: `c:/postgres_data:/var/lib/postgresql`

This is the command I would use on a mac if I wanted to use my `$HOME/postgres_data` directory on my client as the postgres data volume:

```
docker run -d -v $HOME/postgres_data:/var/lib/postgresql -p 25432:5432 kartoza/postgis
```

### 5) Test it out
Refresh your postgres connection in `pgadmin` and verify that you have tables

Verify that on the Welcome screen you see that there are `19 Layers` in the Main content area. 

_Deliverable: Take a screenshot of your PgAdmin screen showing the active connection

### 6) Turn in your work via GitHub Pull Request. 

Submit a *Pull request* to merge your `solution` branch with the `master` branch. _Do not merge it yourself_

