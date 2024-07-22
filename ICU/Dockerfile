# Dockerfile For JAVA APPLICATION

FROM openjdk:11
LABEL maintainer="Naresh <naresh03@gmail.com>"
LABEL version="1.0"
LABEL description="Dockerfile example for a Java application"
WORKDIR /app
COPY sample.jar /app/sample.jar
RUN echo "Building the Java application..."
RUN mvn clean package
EXPOSE 8080
ENTRYPOINT ["java", "-jar"]
CMD ["sample.jar"]


--------------------------------------------------------
# Dockerfile For Nodejs APPLICATION
FROM node:latest
LABEL maintainer="Naresh <naresh03@gmail.com>"
RUN echo " Try to build my application"
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
EXPOSE 3000
ENTRYPOINT ["npm","start"]

---------------------------------------------------------
# Dockerfile For Python APPLICATION

FROM python:3.11.1
LABEL maintainer="Naresh <naresh03@gmail.com>"
WORKDIR /code
COPY ./requirements.txt /code/requirements.txt
RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt
COPY ./main.py /code/main.py
COPY ./form.html /code/form.html
CMD ["uvicorn","main:app","--host","0.0.0.0","--port","80"]

--------------------------------------------------------------------
#CMD& Entrypoint

CMD: Defines the default command to run when the container starts
"/bin/bash": Invokes the Bash shell.
"-c": Instructs Bash to read commands from the following string.

cmd -- shell,exec(json) 
DOCKER pull ubuntu ===> curl,vim,git,snapd
#shell
FROM ubuntu
RUN apt-get update -y
RUN apt-get install nginx -y
RUN 
CMD service nginx start && bash

#exec-Json format

FROM ubuntu
RUN apt-get update -y
RUN apt-get install nginx -y
CMD ["service", "nginx"," start","&& bash"]


# Override CMD
FROM myubuntu:v3
RUN apt-get update -y
RUN apt-get install nginx -y
CMD service nginx start && bash
# docker run -it <container id> (any linux command top,ls,ls -a,sleep 5)
------------------------------------------------------------------------
# Entrypoint

FROM ubuntu
RUN apt-get update -y
RUN apt-get install nginx -y
ENTRYPOINT  service nginx start && bash 
# override Entrypoint 
# command to override Entrypoint
# docker run --entrypoint -it <container id> (any linux command top,ls,ls -a,sleep 5)
# docker run -it --entrypoint top 87234

--------------------------------------------------------------
# Multi Shell command

FROM ubuntu
RUN apt-get update \
    && apt-get install -y nginx \
    && apt-get install -y curl \
    && mkdir -p /app \
    && touch /app/new.file

CMD ["sh", "-c", "echo 'Hello world' > app/new.file  && ls /app"]
--------------------------------------------------------------
# Shell Scripting
#vi setup.sh

#!/bin/bash
mkdir -p /app
touch /app/new.file
echo 'Hello this is docker day two' > /app/new.file



FROM ubuntu
COPY setup.sh /setup.sh
RUN apt-get update \
    && apt-get install -y nginx \
    && apt-get install -y curl \
    && chmod +x /setup.sh
CMD ["/setup.sh"]

--------------------------------------------------------------------
# Multi Stage Dockerfile

FROM maven:latest AS build
WORKDIR /app
COPY . /app
RUN mvn clean package

# Stage 2: Production Stage
FROM tomcat:latest
COPY --from=build /app/target/myweb.war /usr/local/tomcat/webapps/myweb.war
EXPOSE 8080
CMD ["catalina.sh", "run"]
===========================================

Docker compose install
======================
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
docker-compose --version

#docker-compose.yaml --- yaml
docker run -itd --name -p -v "opt:var" image name

version: "3"

services:
  sonarqube:
    image: sonarqube:community
    container_name: sonarqube
    depends_on:
      - db
    environment:
      SONAR_JDBC_URL: jdbc:postgresql://db:5432/sonar
      SONAR_JDBC_USERNAME: sonar
      SONAR_JDBC_PASSWORD: sonar
    volumes:
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_logs:/opt/sonarqube/logs
    ports:
      - "9000:9000"
    restart: always

  db:
    image: postgres:12
    container_name: postgres12
    environment:
      POSTGRES_USER: sonar
      POSTGRES_PASSWORD: sonar
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data
    restart: always

volumes:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_logs:
  postgresql:
  postgresql_data:


Docker-Compose
==============
docker-compose up -d



vm.max_map_count is greater than or equal to 524288

fs.file-max is greater than or equal to 131072

the user running SonarQube can open at least 131072 file descriptors

the user running SonarQube can open at least 8192 threads

You can see the values with the following commands:


sysctl vm.max_map_count
You can set them dynamically for the current session by running the following commands as root:


sysctl -w vm.max_map_count=524288


pipeline {
    agent any
    stages {
        stage ('SCM checkout') {
            steps {
                script{
                  git credentialsId: 'git-cred', url: 'https://github.com/naresh26git/helm-node.git'
                }
            }
        }
        stage ('SonarQube Code analysis'){
            steps {
                script{
                    def scannerHome = tool 'sonarscanner4';
                    withSonarQubeEnv('sonar-pro') {
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=rocket-nodejs"
                    }
                }
            }
        }
        stage ('Build') {
            steps {
                script{
                     sh 'npm install'
                }
            }
        }
        stage('Docker Build Images') {
            steps {
                script {
                    sh 'docker build -t naresh2603/helm-rocket:v1 .'
                    sh 'docker images'
                }
            }
        }
        stage('Trivy scan') {
            steps {
                script {
                    sh 'trivy image naresh2603/helm-rocket:v1 > scan.txt'
                    
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerPass', variable: 'dockerPassword')]) {
                        sh "docker login -u naresh2603 -p ${dockerPassword}"
                        sh 'docker push naresh2603/helm-rocket:v1'
                        # sh 'trivy image naresh2603/helm-rocket:v1 > scan.txt'
                    }
                }
            }
        }
        stage('Deploy on k8s') {
            steps {
                script {
                    withKubeCredentials(kubectlCredentials: [[ credentialsId: 'kubernetes' ]]) {
                        sh 'kubectl create secret generic helm --from-file=.dockerconfigjson=/opt/docker/config.json  --type kubernetes.io/dockerconfigjson --dry-run=client -oyaml > secret.yaml'
                        sh 'kubectl apply -f secret.yaml'
                        sh 'helm package ./Helm'
                        sh 'helm install myrocket ./myrocketapp-0.1.0.tgz'
                        sh 'helm ls'
                        sh 'kubectl get pods -o wide'
                        sh 'kubectl get svc'
                    }
                }
            }
        }
    }
}