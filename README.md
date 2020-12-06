# OMERO.server and OMERO.web with LDAP (docker-compose)

This is an example of running OMERO.server and OMERO.web in Docker
along with an ApacheDS container for LDAP authentication.

OMERO.server is listening on the standard OMERO ports `4063` and `4064`.
OMERO.web is listening on port `4080` (http://localhost:4080/).
ApacheDS is listening on port `10389`.

Log in as user `root` password `omero`.
The initial password can be changed in [`docker-compose.yml`](docker-compose.yml).

## Run

    docker-compose up -d
    docker-compose logs -f

For more configuration options see:
- https://github.com/ome/omero-server-docker/blob/master/README.md
- https://github.com/ome/omero-web-docker/blob/master/README.md
