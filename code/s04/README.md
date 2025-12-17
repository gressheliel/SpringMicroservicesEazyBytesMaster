## Contenido de la Sección 04
- Java 21 y Spring Boot 4.0.0
- Enfoques para generar un contenedor Docker
  - Dockerfile -> accounts
  - Buildpacks -> loans
  - Google Jib -> cards

### Enfoques para generar un contenedor Docker
1. Dockerfile -> accounts
  - Crear un Dockerfile con las instrucciones
2. Buildpacks -> loans
  - Simplifica la contenerizacion, no requiere Dockerfile
3. Google Jib -> cards
  - Es una herramienta de codigo abierto, se carga como un plugin de maven

### Accounts Dockerfile
- Agregar en pom.xml : <packaging>jar</packaging>
- ...accounts>mvn clean install
- ...accounts>mvn spring-boot:run
- ...accounts>java -jar .\target\accounts-0.0.1-SNAPSHOT.jar
- ...accounts>docker build -t gresshel/accounts:s4 .
- ...accounts>docker run -d -p 8080:8080 gresshel/accounts:s4
- ...accounts>docker exec -ti ID_CONTAINER bash

### Loans Buildpacks
- http://buildpacks.io
- Atrás de escena utiliza Paketo buildpacks
- Agregar en pom.xml :
```
...
<packaging>jar</packaging>
...
<configuration>
    <image>
		<name>gresshel/${project.artifactId}:s4</name>
	</image>
</configuration>
```
- ...loans>mvn spring-boot:build-image
- Para evitar el error :
```
Execution default-cli of goal org.springframework.boot:spring-boot-maven-plugin:4.0.0:build-image failed: 'username' must not be null
```
- ...loans>docker login
- ...loans>mvn spring-boot:build-image
- ...loans>docker run -d -p 8090:8090 gresshel/loans:s4

- Marca error, para loans no funciona bien buildpacks
- En el curso vamos a usar Jib
```
 Execution default-cli of goal org.springframework.boot:spring-boot-maven-plugin:4.0.0:build-image failed: Builder lifecycle 'creator' failed with status code 51
```

### Cards Google Jib
- https://github.com/GoogleContainerTools/jib
- Solo funciona con Java
- Agregar en pom.xml : <packaging>jar</packaging>
- Agregar plugin en pom.xml
```
<plugin>
				<groupId>com.google.cloud.tools</groupId>
				<artifactId>jib-maven-plugin</artifactId>
				<version>3.4.1</version>
				<configuration>
					<to>
						<image>gresshel/${project.artifactId}:s4</image>
					</to>
				</configuration>
			</plugin>
```
- ...cards>mvn compile jib::dockerBuild
- ...cards>docker run -d -p 9000:9000 gresshel/cards:s4

### Construir imagen y subir a Docker Hub
- ...cards>mvn compile jib::build
- log [Built and pushed image as gresshel/cards:s4]

### Pushing images en Docker Hub
- [gresshel@gmail.com, elieta103]
- docker image push docker.io/gresshel/accounts:s4
- docker image push docker.io/gresshel/cards:s4
- docker image push docker.io/gresshel/loans:s4

### Docker Compose
- ...s04>docker compose up -d     RECREA E INICIA CONTAINERS
- ...s04>docker compose down      DETIENE Y ELIMINA
- ...s04>docker compose start     BUSCA CONTAINERS EXISTENTES E INTENTA LANZARLOS DE NUEVO
- ...s04>docker compose stop      DETIENE LOS CONTAINERS

### Comandos docker utiles
- docker images
- docker image inspect ID_IMAGEN
- docker image rm ID_IMAGEN
- docker build -t NOMBRE_IMAGEN .
- docker run -d -p PUERTO_HOST:PUERTO_CONTAINER NOMBRE_IMAGEN
- docker ps
- docker ps -a
- docker container start ID_CONTAINER
- docker container pause ID_CONTAINER
- docker container unpause ID_CONTAINER
- docker container stop ID_CONTAINER
- docker container kill ID_CONTAINER
- docker container restart ID_CONTAINER
- docker container inspect ID_CONTAINER
- docker container logs ID_CONTAINER
