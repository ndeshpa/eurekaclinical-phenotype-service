# eurekaclinical-phenotype-service
[Atlanta Clinical and Translational Science Institute (ACTSI)](http://www.actsi.org), [Emory University](http://www.emory.edu), Atlanta, GA

Write a description here

Latest release: [![Latest release](https://maven-badges.herokuapp.com/maven-central/org.eurekaclinical/eurekaclinical-phenotype-service/badge.svg)](https://maven-badges.herokuapp.com/maven-central/org.eurekaclinical/eurekaclinical-phenotype-service)

No final releases yet

* [Oracle Java JDK 8](http://www.oracle.com/technetwork/java/javase/overview/index.html)
* [Maven 3.2.5 or greater](https://maven.apache.org)

* [Oracle Java JRE 8](http://www.oracle.com/technetwork/java/javase/overview/index.html)
* [Tomcat 7](https://tomcat.apache.org)
* One of the following relational databases:
  * [Oracle](https://www.oracle.com/database/index.html) 11g or greater
  * [PostgreSQL](https://www.postgresql.org) 9.1 or greater
  * [H2](http://h2database.com) 1.4.193 or greater (for testing)


Manages registering a user with this service for authorization purposes.

Call-dependent

Yes

Properties:
* `id`: unique number identifying the user (set by the server on object creation, and required thereafter).
* `username`: required username string.
* `roles`: array of numerical ids of roles.

All calls use standard names, return values and status codes as specified in the [Eureka! Clinical microservice specification](https://github.com/eurekaclinical/dev-wiki/wiki/Eureka%21-Clinical-microservice-specification)

Returns an array of all User objects. Requires the `admin` role.

Returns a specified User object by the value of its id property, which is unique. Requires the `admin` role to return any user record. Otherwise, it will only return the user's own record.

Returns a specified User object by its username, which is unique. Requires the `admin` role to return any user record. Otherwise, it will only return the user's own record.

Returns the User object for the currently authenticated user.

Creates a new user. The User object is passed in as the body of the request. Returns the URI of the created User object. Requires the `admin` role.

Updates the user object with the specified id. The User object is passed in as the body of the request. Requires the `admin` role.

Manages roles for this service. It is read-only.

No.

Yes

Properties:
* `id`: unique number identifying the role.
* `name`: the role's name string.

All calls use standard names, return values and status codes as specified in the [Eureka! Clinical microservice specification](https://github.com/eurekaclinical/dev-wiki/wiki/Eureka%21-Clinical-microservice-specification)

Returns an array of all User objects.

Returns a specified Role object by the value of its id property, which is unique.

Returns a specified Role object by its name, which is unique.

The project uses the maven build tool. Typically, you build it by invoking `mvn clean install` at the command line. For simple file changes, not additions or deletions, you can usually use `mvn install`. See https://github.com/eurekaclinical/dev-wiki/wiki/Building-Eureka!-Clinical-projects for more details.

You can run this project in an embedded tomcat by executing `mvn tomcat7:run -Ptomcat` after you have built it. It will be accessible in your web browser at https://localhost:8443/eurekaclinical-phenotype-service/. Your username will be `superuser`.

A [Liquibase](http://www.liquibase.org) changelog is provided in `src/main/resources/dbmigration/` for creating the schema and objects. [Liquibase 3.3 or greater](http://www.liquibase.org/download/index.html) is required.

Perform the following steps:
1) Create a schema in your database and a user account for accessing that schema.
2) Get a JDBC driver for your database and put it the liquibase lib directory.
3) Run the following:
```
./liquibase \
      --driver=JDBC_DRIVER_CLASS_NAME \
      --classpath=/path/to/jdbcdriver.jar:/path/to/eurekaclinical-phenotype-service.war \
      --changeLogFile=dbmigration/changelog-master.xml \
      --url="JDBC_CONNECTION_URL" \
      --username=DB_USER \
      --password=DB_PASS \
      update
```
4) Add the following Resource tag to Tomcat's `context.xml` file:
```
<Context>
...
    <Resource name="jdbc/PhenotypeService" auth="Container"
            type="javax.sql.DataSource"
            driverClassName="JDBC_DRIVER_CLASS_NAME"
            factory="org.apache.tomcat.jdbc.pool.DataSourceFactory"
            url="JDBC_CONNECTION_URL"
            username="DB_USER" password="DB_PASS"
            initialSize="3" maxActive="20" maxIdle="3" minIdle="1"
            maxWait="-1" validationQuery="SELECT 1" testOnBorrow="true"/>
...
</Context>
```

The validation query above is suitable for PostgreSQL. For Oracle and H2, use
`SELECT 1 FROM DUAL`.

This service is configured using a properties file located at `/etc/ec.phenotype/application.properties`. It supports the following properties:
* `eurekaclinical.phenotype.callbackserver`: https://hostname:port
* `eurekaclinical.phenotype.url`: https://hostname:port/eurekaclinical-phenotype-service
* `cas.url`: https://hostname.of.casserver:port/cas-server

A Tomcat restart is required to detect any changes to the configuration file.

1) Stop Tomcat.
2) Remove any old copies of the unpacked war from Tomcat's webapps directory.
3) Copy the warfile into the tomcat webapps directory, renaming it to remove the version. For example, rename `eurekaclinical-phenotype-service-1.0-SNAPSHOT.war` to `eurekaclinical-phenotype-service.war`.
4) Start Tomcat.

```
<dependency>
    <groupId>org.eurekaclinical</groupId>
    <artifactId>eurekaclinical-phenotype-service</artifactId>
    <version>1.0-SNAPSHOT</version>
</dependency>
```

## REST APIs

### `/api/protected/frequencytypes`
Read-only, enumerates the allows types of frequency phenotypes.

#### Role-based authorization
None

#### Requires successful authentication
Yes

#### FrequencyType object
Names and describes a frequency type.

Properties:
* `id`: unique numerical id of the frequency type.
* `name`: unique name of the frequency type.
* `description`: unique description of the frequency type.

#### Calls
Uses status codes as specified in the [Eureka! Clinical microservice specification](https://github.com/eurekaclinical/dev-wiki/wiki/Eureka%21-Clinical-microservice-specification).

##### GET `/api/protected/frequencytypes`
Returns a list of possible frequency types in ascending order.

##### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/frequencytypes

Returns:
```
[
  { "id":1,
    "name":"at least",
    "description":"at least",
    "rank":1,
    "default":true},
  { "id":2,
    "name":"first",
    "description":"first",
    "rank":2,
    "default":false}
]
```

##### GET `/api/protected/frequencytypes/{id}`
Returns the frequency type with the specified numerical unique id.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/frequencytypes/1

Returns:
```
{ "id":1,
  "name":"at least",
  "description":"at least",
  "rank":1,
  "default":true }
```

##### GET `/api/protected/frequencytypes/byname/{name}`
Returns the frequency type with the specified unique name.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/frequencytypes/byname/at%20least

Returns:
```
{ "id":1,
  "name":"at least",
  "description":"at least",
  "rank":1,
  "default":true }
```

### `/api/protected/relationops`
Read-only, enumerates the allows types of relation operations.

#### Role-based authorization
None

#### Requires successful authentication
Yes

#### RelationOp object
Names and describes a relation operator.

Properties:
* `id`: unique numerical id of the relation operator.
* `name`: unique name of the relation operator.
* `description`: unique description of the relation operator.

#### Calls
Uses status codes as specified in the [Eureka! Clinical microservice specification](https://github.com/eurekaclinical/dev-wiki/wiki/Eureka%21-Clinical-microservice-specification).

##### GET `/api/protected/relationops`
Gets a list of the possible temporal relation operators in rank order.
###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/relationops

Returns: 
```
[
  { "id":1,
    "name":"before",
    "description":"before",
    "rank":1,
    "type":"SEQUENTIAL",
    "default":true},
  { "id":2,
    "name":"after",
    "description":"after",
    "rank":2,
    "type":"SEQUENTIAL",
    "default":false},
  { "id":3,
    "name":"around",
    "description":"around",
    "rank":3,
    "type":"OVERLAPPING",
    "default":false}
]
```

##### GET `/api/protected/relationops/{id}`
Returns the temporal relation operator with the specified numerical unique id.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/relationops/1

Returns:
```
{"id":1,"name":"before","description":"before","rank":1,"type":"SEQUENTIAL","default":true}
```

##### GET /api/protected/relationops/byname/{name}
Returns the temporal relation operator with the specified unique name.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/relationops/byname/before

Returns: 
```
{"id":1,"name":"before","description":"before","rank":1,"type":"SEQUENTIAL","default":true}
```
### `/api/protected/thresholdops`
Read-only, enumerates the allows types of value threshold operations.

#### Role-based authorization
None

#### Requires successful authentication
Yes

#### ThresholdOp object

#### Calls
Uses status codes as specified in the [Eureka! Clinical microservice specification](https://github.com/eurekaclinical/dev-wiki/wiki/Eureka%21-Clinical-microservice-specification).

##### GET `/api/protected/thresholdops`
Gets a list of the supported value threshold operators.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/thresholdsops

Returns:
```
[{"id":1,"name":"any","description":"any"},{"id":2,"name":"all","description":"all"}]
```

##### GET `/api/protected/thresholdsops/{id}`
Returns the value threshold operator with the specified numerical unique id.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/thresholdsops/1

Returns:
```
{"id":1,"name":"any","description":"any"}
```

##### GET `/api/protected/thresholdsops/byname/{name}`
Returns the value threshold operator with the specified unique name.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/thresholdsops/byname/any

Returns:
```
{"id":1,"name":"any","description":"any"}
```

### `/api/protected/valuecomps`
Read-only, enumerates the allows types of value comparators.

#### Role-based authorization
None

#### Requires successful authentication
Yes

#### ValueComparator object
Properties:
* `id`: unique numerical id of the relation operator.
* `name`: unique name of the relation operator.
* `description`: unique description of the relation operator.

#### Calls
Uses status codes as specified in the [Eureka! Clinical microservice specification](https://github.com/eurekaclinical/dev-wiki/wiki/Eureka%21-Clinical-microservice-specification).

##### GET `/api/protected/valuecomps`
Gets a list of the supported value comparators in rank order.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/valuecomps

Returns:
```
[
  { "id":5,
    "name":"<",
    "description":"less than",
    "threshold":"UPPER_ONLY","rank":1}, 
  { "id":6,
    "name":"<=",
    "description":"less than or equal to",
    "threshold":"UPPER_ONLY",
    "rank":2},
  { "id":1,
    "name":"=",
    "description":"equals",
    "threshold":"BOTH",
    "rank":3},
  { "id":2,
    "name":"not=",
    "description":"not equals",
    "threshold":"BOTH",
    "rank":4},
  { "id":3,
    "name":">",
    "description":"greater than",
    "threshold":"LOWER_ONLY",
    "rank":5},
  { "id":4,
    "name":">=",
    "description":"greater than or equal to",
    "threshold":"LOWER_ONLY",
    "rank":6}
]
```

##### GET `/api/protected/valuecomps/{id}`
Returns the value comparator operator with the specified numerical unique id.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/valuecomps/1

Returns:
```
{"id":1,"name":"=","description":"equals","threshold":"BOTH","rank":3}
```

##### GET `/api/protected/valuecomps/byname/{name}`
Returns the value comparator operator with the specified unique name.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/valuecomps/byname/=

Returns:
```
{"id":1,"name":"=","description":"equals","threshold":"BOTH","rank":3}
```

### `/api/protected/timeunits`
Read-only, enumerates the allowed time units.

#### Role-based authorization
None

#### Requires successful authentication
Yes

#### TimeUnit object
Properties:
* `id`: unique numerical id of the time unit.
* `name`: unique name of the time unit.
* `description`: unique description of the time unit.

#### Calls
Uses status codes as specified in the [Eureka! Clinical microservice specification](https://github.com/eurekaclinical/dev-wiki/wiki/Eureka%21-Clinical-microservice-specification).

##### GET `/api/protected/timeunits`
Gets an array of the supported time units in rank order.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/timeunits

Returns:
```
[
  {"id":3,"name":"minute","description":"minutes","rank":1,"default":false},
  {"id":2,"name":"hour","description":"hours","rank":2,"default":false},
  {"id":1,"name":"day","description":"days","rank":3,"default":true}
]
```

##### GET /api/protected/timeunits/{id}
Returns the time unit with the specified numerical unique id.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/timeunits/1

Returns:
```
{"id":1,"name":"day","description":"days","rank":3,"default":true}
```

##### GET /api/protected/timeunits/byname/{name}
Returns the time unit with the specified unique name.

###### Example:
URL: https://localhost:8443/eurekaclinical-analytics-service/api/protected/timeunits/byname/day

Returns:
```
{"id":1,"name":"day","description":"days","rank":3,"default":true}
```

* [Javadoc for latest development release](http://javadoc.io/doc/org.eurekaclinical/eurekaclinical-phenotype-service) [![Javadocs](http://javadoc.io/badge/org.eurekaclinical/eurekaclinical-phenotype-service.svg)](http://javadoc.io/doc/org.eurekaclinical/eurekaclinical-phenotype-service)

Feel free to contact us at help@eurekaclinical.org.

