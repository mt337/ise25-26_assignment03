# CampusCoffee (WS 25/26)

## Prerequisites

* Install [Docker Desktop](https://www.docker.com/products/docker-desktop/) or a compatible open-source alternative such as [Rancher Desktop](https://rancherdesktop.io/).
* Install the [Temurin JDK 21](https://adoptium.net/temurin/releases/?version=21&os=any&arch=any) and [Maven 3.9](https://maven.apache.org/install.html) either via the provided [`mise.toml`](mise.toml) file (see [getting started guide](https://mise.jdx.dev/getting-started.html) for details) or directly via your favorite package manager.
* Install a Java IDE. We recommend [IntelliJ](https://www.jetbrains.com/idea/), but you are free to use alternatives such as [VS Code](https://code.visualstudio.com/) with suitable extensions.

## Build application

First, make sure that the Docker daemon is running.
Then, to build the application, run the following command in the command line (or use the Maven integration of your IDE):

```shell
mvn clean install
```
**Note:** In the `dev` profile, all repositories are cleared before startup, the initial data is loaded (see [`LoadInitialData.java`](application/src/main/java/de/seuhd/campuscoffee/LoadInitialData.java)).

You can use the quiet mode to suppress most log messages:

```shell
mvn clean install -q
```

## Start application (dev)

First, make sure that the Docker daemon is running.
Before you start the application, you first need to start a Postgres docker container:

```shell
docker run -d -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 postgres:17-alpine
```

Then, you can start the application:

```shell
cd application
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```
**Note:** The data source is configured via the [`application.yaml`](application/src/main/resources/application.yaml) file.

## REST API

You can use `curl` in the command line to send HTTP requests to the REST API.

### POS endpoint

#### Get POS

All POS:
```shell
curl http://localhost:8080/api/pos
```
POS by ID:
```shell
curl http://localhost:8080/api/pos/1 # add valid POS id here
```

#### Create POS

```shell
curl --header "Content-Type: application/json" --request POST --data '{"name":"New Café","description":"Description","type":"CAFE","campus":"ALTSTADT","street":"Hauptstraße","houseNumber":"100","postalCode":69117,"city":"Heidelberg"}' http://localhost:8080/api/pos
```

#### Update POS

Update title and description:
```shell
curl --header "Content-Type: application/json" --request PUT --data '{"id":4,"name":"New coffee","description":"Great croissants","type":"CAFE","campus":"ALTSTADT","street":"Hauptstraße","houseNumber":"95","postalCode":69117,"city":"Heidelberg"}' http://localhost:8080/api/pos/4 # set correct POS id here and in the body
```

## Commands we used
#### Send a JSON Payload via curl using a POST request
We used/tested 2 ways how you can do it:

1. Directly with just one command:
```shell
curl --header "Content-Type: application/json" --request POST --data "{\"name\":\"Bäckerei Kohlmann\",\"description\":\"Campus Bäckerei\",\"type\":\"CAFE\",\"campus\":\"INF\",\"street\":\"Im Neuenheimer Feld\",\"houseNumber\":\"370\",\"postalCode\":69120,\"city\":\"Heidelberg\"}" http://localhost:8080/api/pos
```
>Note that on windows when using cmd you need to escape the double quotes inside the JSON string with backslashes

2. or create a [name].json file with the payload
```json
{
  "name": "Bäckerei Kohlmann",
  "description": "Campus Bäckerei",
  "type": "CAFE",
  "campus": "INF",
  "street": "Im Neuenheimer Feld",
  "houseNumber": "370",
  "postalCode": 69120,
  "city": "Heidelberg"
}
```
  
  and send it like this:
```shell
curl --header "Content-Type: application/json" --request POST --data @[name].json http://localhost:8080/api/pos
```
> IT's a much better approach on windows beacuse of readibility

#### GET request - confirm that entry was succesfully saved
After creating POS you will get a id in your answer - e. g.:
```
{"id":5,"createdAt":"2025-11-03T11:42:14.8909882","updatedAt":"2025-11-03T11:42:14.8909882","name":"Bäckerei Kohlmann","description":"Campus Bäckerei","type":"CAFE","campus":"INF","street":"Im Neuenheimer Feld","houseNumber":"370","postalCode":69120,"city":"Heidelberg"}
```

If you got the id you can confirm that your entry was successfully saved by using this command:
```shell
curl http://localhost:8080/api/pos/5
```

Or if you don't have the id, use this instead, it will display all POS and you can manually search for your entry:
```shell
curl http://localhost:8080/api/pos
```
