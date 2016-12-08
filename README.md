## Angular 2 Frontent with SpringBoot (Java) Backend
Application to demonstrate various parts of a service oriented RESTfull application. 

This application adopts a microservice architecture. The api and the webui are served from 
seperate webservers from seperate ports. 
- API server (port 9119)
- WebUI server (port 3000)
- Gateway (you can find gateway config at [config/application.properties](https://github.com/mrin9/Angular2_SpringBoot/blob/master/config/application.properties) file. In short the gateway redirects all the requests to port 3000 if the url starts with '/webui' else the request is processed at port 9119)

### Technology Stack
Component         | Technology
---               | ---
Frontend          | [Angular 2](https://github.com/angular/angular) , [Material-Design](https://github.com/angular/material2),[ReactiveX]()  
Backend (REST)    | [SpringBoot 1.4](https://projects.spring.io/spring-boot) (Java)
Security          | Token Based (Spring Security and [JWT](https://github.com/auth0/java-jwt) )
REST Documentation| [Swagger UI / Springfox](https://github.com/springfox/springfox) and [ReDoc](https://github.com/Rebilly/ReDoc)
REST Spec         | [Open API Standard](https://www.openapis.org/) 
In Memory DB      | H2 
Persistence       | JPA
Client Build Tools| [angular-cli](https://github.com/angular/angular-cli), Webpack, npm
Server Build Tools| Maven(Java)
Gateway Service   | [Netflix zuul](https://github.com/Netflix/zuul)
Localization      | <Pending>     


## Build Instruction
Prerequisite (Ensure you have this installed before proceeding further)
- Java 8
- Maven 3.3.9
- Node 7.2.1, npm 3.9.5
- Angular-cli
- local-web-server ( install using `npm install -g local-web-server`)

Clone the repo in a folder


### Install REST Backend

```bash
#navigate to the root folder where pom.xml is present 
mvn clean install
```

### Install Frontend (if not yet installed)

Install/Upgrade angular-cli Global package:

```bash
npm uninstall -g angular-cli
npm cache clean
npm install -g angular-cli@latest
```

Local project package:

```bash
#navigate to the root folder where package.json is present
rm -rf node_modules dist tmp
npm install --save-dev angular-cli@latest
npm install
```
### Install a static WebUI server (if not yet installed) 
we are using [local-web-server](https://github.com/75lb/local-web-server)
```bash
#configuration for local-web-server is stored in .local-web-server.json
npm install -g local-web-server
```

### Running the app

**Server:** @port 9119

to start the server go to the folder where pom.xml is present

```bash
# build: should be executed from the folder where pom.xml is present 
mvn clean install

# Start the server (runs on port 9119)
# port and other configurations are inside ./cofig/application.properties file
java -jar ./target/sonicwall-1.0.0.jar
```

**Client:** @port 3000


```bash
# Get and install all the packages 
npm install
# build the project (this will put the files under dist folder)
ng build

# Start the client webserver (serve files from dist folder )
ws 
```

- Access Server at <http://localhost:9119/index.html>
- Access Client at <http://localhost:3000>

**Login Credentials**
```
demo:demo
admin:admin
inactive:inactive
```

**To get an authentication token** 

```bash
curl -X POST --header 'Content-Type: application/json' --header 'Accept: application/json' -d '{"username": "demo", "password": "demo" }' 'http://localhost:9119/session'
```
or POST the username and password to http://localhost:9119/session


after you get the authentication token you must provide this in the header for all the protected urls 

```bash
curl -X GET --header 'Accept: application/json' --header 'Authorization: [replace this with token ]' 'http://localhost:9119/version'
```


## Building docker images

if you want to host the application in a container you can use the Dockerfile provided here 
to build a docker image (you should have docker installed)
```bash
# execute this from a folder where Dockerfile is present
docker build -t msw:node_java_python .

# msw:node_java_python is just a tag name to identify the image
# after the image is built , you can run the container as follows
docker run -e "SPRING_PROFILES_ACTIVE=dev" -p 9119:9119 -p 3000:3000 -t msw:node_java_python
```
now you can visit http://localhost:9119
