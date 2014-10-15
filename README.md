# mds Dockerized #

Images and scripts for running mds in Docker containers.
Props to Chris Beer, whose [DSpace dockerization](https://github.com/cbeer/docker-dspace)
at OR 2014 inspired and informed this.

## Basic Design ##

Application functionality is divided among 3 containers: a PostgreSQL
database ('mds-db'), a bitstream storage service ('mds-bits'),
and a servlet container (Tomcat) running mds webapp(s) ('mds-web').
An optional command-line environment container ('mds-cli') may be used for
non-web-based operations. The current mds-web webapps include a REST API,
and a OAI-PMH service but any web UI, etc will work roughly the same way.
The command-line environment container is not strictly needed, and does not
stay resident the way the other three do, but it is a useful tool for performing simple operations.

This design permits a modest degree of horizontal scalability, 
in the sense that additional webapp containers could be added on demand
(provided there were reverse proxies to load-balance, etc).

## Docker Commands ##

We start the Postgres container for metadata (nothing sacred about the one chosen), also declaring
the database, user/password for the mds installation:

    docker run -d \
               --name="mds-db" \
               -p 5432:5432 \
               -e DB="dspace" \
               -e USER="super" \
               -e PASS="dspace" \
               paintedfox/postgresql

Next up, the storage service for Bitstreams:

    docker run -d \
               --name="mds-bits" \
               -p 9333:9333 -p 8080:8080
               modrepo/mds-bits


Next, we run the webapp container to register with the instance. This should only
be done once, and the container will exit when the script has run. Note that
in addition to linking to the postgres container, we pass in a file of environment
variables specific to the site. This invocation essentially creates the mds instance:

    docker run --rm \
               -p 8080:8080 \
               --link mds-db:db \
               --env-file=./setenv.sh \
               modrepo/mds-web /mds/stage/bin/dspace register mds-web -s /mds/stage

We can now run our command-line container to create an administrator:

    docker run --rm \
               --link mds-db:db \
               --env-file=./setenv.sh \
               modrepo/mds-cli create-administrator-env

Finally, we start the webapp container which will fire up Tomcat:

    docker run -d \
               -p 8080:8080 \
               --link mds-db:db \
               --link mds-bits:bits \
               --env-file=./setenv.sh \
               modrepo/mds-web

We can now access the webapp at http://localhost:8080/webapi
