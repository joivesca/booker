# booker
# booker

Si usas WildFly localmente

Configuracion de usuario para administrar

#Agregar usuario administrador
Nos dirigimos al directorio bin de nuestro Wildfly descargado y ejecutamos
sh add-user.sh

ingresamos admin y admin como password y continuamos.

#Crear Datasource

#Postgres
Descargamos el jar de postgres en este caso la version 42.7.4

Nos dirigmos al directorio modules/system/layers/base/org y creamos el directorio postgresql y dentro el directorio main.
Agregamos el jar y creamos un archivo module.xml con lo siguiente

<?xml version="1.0" encoding="UTF-8"?>
<module xmlns="urn:jboss:module:1.1" name="org.postgresql">
    <resources>
        <resource-root path="postgresql-42.7.4.jar"/>
    </resources>
    <dependencies>
        <module name="javax.api"/>
        <module name="javax.transaction.api"/>
    </dependencies>
</module>


#MariaDB
Descargamos el jar de mariadb en este caso la version 3.5.0

Nos dirigmos al directorio modules/system/layers/base/org y creamos el directorio mariadb y dentro el directorio main.
Agregamos el jar y creamos un archivo module.xml con lo siguiente

<?xml version="1.0" encoding="UTF-8"?>
<module xmlns="urn:jboss:module:1.1" name="org.mariadb">
    <resources>
        <resource-root path="mariadb-java-client-3.5.0.jar"/>
    </resources>
    <dependencies>
        <module name="javax.api"/>
        <module name="javax.transaction.api"/>
    </dependencies>
</module>

#Add DataSources
Nos dirigimos al directorio standalone/configuration de nuestro Wildfly y abrimos standalone.xml o standalone-full.xml depende el caso.

#Postgres
En la seccion de Datasources agregamos, cambiando tus credenciales y database

<datasource jndi-name="java:/Postgresql" pool-name="Postgresql" enabled="true" use-java-context="true">
	<connection-url>jdbc:postgresql://localhost:5432/dev</connection-url>
    <driver>postgresql</driver>
    <pool>
       	<min-pool-size>10</min-pool-size>
       	<max-pool-size>100</max-pool-size>
        <prefill>true</prefill>
    </pool>
    <security user-name="postgres" password="secret"/> 
    <!-- this is the old format, in case you have a precedent version of WildFly --> 
    <!-- 
    <security> 
    	<user-name>postgres</user-name> 
    	<password>secret</password> 
   </security> --> 
</datasource>


En la seccion Drivers dentro de datasources, agregar.

<driver name="postgresql" module="org.postgresql">
	<xa-datasource-class>org.postgresql.xa.PGXADataSource</xa-datasource-class>
</driver>

#MariaDB
En la seccion de Datasources agregamos, cambiando tus credenciales y database

<datasource jndi-name="java:/jdbc/MariaDS" pool-name="MariaDS" enabled="true" use-java-context="true">
	<connection-url>jdbc:mariadb://localhost:3306/mydb</connection-url>
    <driver>mariadb</driver>
    <security>
    	<user-name>root</user-name>
        <password>password</password>
    </security>
</datasource>

En la seccion Drivers dentro de datasources, agregar.

<driver name="mariadb" module="org.mariadb">
	<driver-class>org.mariadb.jdbc.Driver</driver-class>
</driver>


#Ejecucion postgres docker

docker run --name postgres-booker -e POSTGRES_USER=booker -e POSTGRES_PASSWORD=booker -e POSTGRES_DB=booker -p 5432:5432 -d postgres:latest
