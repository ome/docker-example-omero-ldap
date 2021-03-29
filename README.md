# OMERO.server and OMERO.web with LDAP (docker-compose)

[![Actions Status](https://github.com/ome/docker-example-omero-ldap/workflows/Build/badge.svg)](https://github.com/ome/docker-example-omero-ldap/actions)

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

## User management

In the `ldap` container, you can use the pre-installed `ldapmanager` tool
from the [apacheds-docker](https://github.com/ome/apacheds-docker/) repository
to set up users and groups:

```
docker-compose exec ldap bash
ldapmanager init
ldapmanager user u1 --password u1
ldapmanager group u1 --member u1
```

Additionally, in the `ldap` container, you can modify a user using `ldapmodify`:

```
ldapmodify -H ldap://localhost:10389 -D uid=admin,ou=system -W < update.ldif
```
with this being an example of `update.ldif` file:

```
version: 1
dn: uid=t,ou=Users,dc=openmicroscopy,dc=org
changetype: modify
replace: givenName
givenName: X.
```

## Testing with omero-ldaptool

[omero-ldap](https://github.com/glencoesoftware/omero-ldaptool) provides a way to check if your
OMERO configuration is talking to LDAP correctly.

Then from the `omeroserver` container, download the configuration:

```
docker-compose exec omeroserver /opt/omero/server/venv3/bin/omero config get --show-password > cfg
```

Then, run the build and the tool:

```
./gradlew installDist
build/install/omero-ldaptool/bin/omero-ldaptool cfg u1
```

You should see output like this:

```
/opt/omero-ldaptool $ build/install/omero-ldaptool/bin/omero-ldaptool cfg u1
2020-06-11 14:55:54,728 [main] INFO  com.glencoesoftware.ldaptool.Main - Loading LDAP configuration from: /opt/omero-ldaptool/cfg
... skip bunch of lines ...
2020-06-11 14:55:55,347 [main] INFO  com.glencoesoftware.ldaptool.Main - Experimenter field mappings id=null email=null firstName=J. lastName=Doe institution=null ldap=true middleName=null omeName=u1
2020-06-11 14:55:55,348 [main] INFO  c.g.ldaptool.MockSimpleRoleProvider - Would have created ExperimenterGroup id=1 name=MyData perms=null strict=false isLdap=true
2020-06-11 14:55:55,348 [main] INFO  com.glencoesoftware.ldaptool.Main - Would be member of Group IDs=[1]
```
