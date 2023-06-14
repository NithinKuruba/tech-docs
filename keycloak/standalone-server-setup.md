# Setup Standalone Keycloak Server

## Download Keycloak Server

```sh
sudo wget "https://github.com/keycloak/keycloak/releases/download/18.0.2/keycloak-legacy-18.0.2.tar.gz"
sudo tar xzf keycloak-18.0.2.tar.gz
```

## Postgres Setup

- Create `./modules/system/layers/keycloak/com/postgres/main` directory
- Add `module.xml` with below content

```xml
<?xml version="1.0" ?>
<module xmlns="urn:jboss:module:1.3" name="com.postgres">
 <resources>
  <resource-root path="postgresql-42.2.20.jar" />
 </resources>
 <dependencies>
  <module name="javax.api"/>
  <module name="javax.transaction.api"/>
 </dependencies>
</module>
```

- Download `postgresql-42.2.20.jar` and place it adjacent to `module.xml` file
- Add datasource and driver

```xml
<datasource jndi-name="java:jboss/datasources/KeycloakDS" pool-name="KeycloakDS" enabled="true" use-java-context="true" statistics-enabled="${wildfly.datasources.statistics-enabled:${wildfly.statistics-enabled:false}}">
    <connection-url>jdbc:postgresql://localhost:5432/keycloak</connection-url>
    <driver>postgres</driver>
    <pool>
<max-pool-size>20</max-pool-size>
</pool>
    <security>
        <user-name>postgres</user-name>
        <password>postgres</password>
    </security>
</datasource>
<drivers>
    <driver name="postgres" module="com.postgres">
        <xa-datasource-class>org.postgresql.xa.PGXADataSource</xa-datasource-class>
    </driver>
</drivers>
```

## Create Database

```
pg_ctl start

psql -U postgres -c "CREATE DATABASE keycloak"
```

## Admin User

```sh
./bin/add-user-keycloak.sh -u {{username}} -p {{password}}
```

## Keycloak Settings

- Update cache settings

```xml
<staticMaxAge>1</staticMaxAge>
<cacheThemes>false</cacheThemes>
<cacheTemplates>false</cacheTemplates>
```
