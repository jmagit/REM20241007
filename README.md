# Curso de DevOps (Jenkins)

## Instalación Docker Desktop

- [WSL 2 feature on Windows](https://learn.microsoft.com/es-es/windows/wsl/install)
- [Docker Desktop](https://www.docker.com/get-started/)

### Configuración de puertos dinámicos en Windows

    netsh int ipv4 set dynamic tcp start=51000 num=14536

### Alternativas a Docker Desktop

- [Podman](https://podman.io/docs/installation)
- [Rancher Desktop](https://rancherdesktop.io/)

## Entorno CI/CD

### Crear una red en Docker

    docker network create jenkins

### Desplegar contenedores

#### Servidor de correo electrónico

    docker run -d --name mailhog -p 1025:1025 -p 8025:8025 mailhog/mailhog

    docker network connect jenkins mailhog

#### Control de calidad

    docker run -d --name sonarQube --publish 9000:9000 --network jenkins sonarqube:latest

#### Docker in Docker

    docker run -d --name jenkins-docker-in-docker --detach --privileged --network jenkins --network-alias docker --env DOCKER_TLS_CERTDIR=/certs --volume jenkins-docker-certs:/certs/client --volume jenkins-data:/var/jenkins_home --publish 2376:2376 docker:dind --storage-driver overlay2

#### Jenkins

    docker run --name jenkins-host --network jenkins --env DOCKER_HOST=tcp://docker:2376 --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 --publish 50080:8080 --publish 50000:50000 --volume %cd%/jenkins_home:/home --volume %cd%/data:/var/jenkins_home --volume jenkins-docker-certs:/certs/client:ro --env JAVA_OPTS="-Dhudson.plugins.git.GitSCM.ALLOW_LOCAL_CHECKOUT=true" jenkins/jenkins:lts

#### Jenkins personalizado

- Descargar Dockerfile: [cicd/Dockerfile](./cicd/Dockerfile)
- Crear imagen

      docker build -t jenkins-maven-docker .

- Crear contenedor

        docker run --name jenkins --network jenkins --env DOCKER_HOST=tcp://docker:2376 --env DOCKER_CERT_PATH=/certs/client --env DOCKER_TLS_VERIFY=1 --publish 50080:8080 --publish 50000:50000 --volume %cd%/jenkins_home:/home --volume jenkins-data:/var/jenkins_home --volume jenkins-docker-certs:/certs/client:ro --env JAVA_OPTS="-Dhudson.plugins.git.GitSCM.ALLOW_LOCAL_CHECKOUT=true" jenkins-maven-docker

## Kata

- https://github.com/emilybache/GildedRose-Refactoring-Kata/blob/main/GildedRoseRequirements_es.md

## Repositorios

- https://github.com/jmagit/demos-devops
- https://github.com/jmagit/demos-frontend-angular
- https://github.com/jmagit/MOCKWebServer
- https://github.com/spring-projects/spring-petclinic

## Comandos

    docker run --rm -it --name maven -v %cd%:/local -v maven-repository:/root/.m2 maven:3.8.6-eclipse-temurin-11 sh
