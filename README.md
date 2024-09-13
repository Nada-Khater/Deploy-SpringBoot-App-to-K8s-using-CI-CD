# Deploy-SpringBoot-App-to-K8s-using-Jenkins

## 📝 Overview
This project demonstrates how to dockerize a Spring Boot application and deploy it to a Kubernetes cluster (Minikube) using a CI/CD pipeline. The solution utilizes Jenkins as the CI/CD tool and automates the process across multiple stages, including linting, testing, image building, and deployment on development and production environments.

## 🚀 Features
- **Dockerization of Spring Boot Application:** Using Docker to containerize the Spring Boot app.
- **CI/CD Pipeline Automation:** Automated stages for linting, unit testing, building Docker images, and deploying the app.
- **SonarQube Integration:** Static code analysis for quality assurance.
- **Kubernetes Deployment:** Automated deployment to a K8s cluster with `dev` and `prod` environments.
- **Ingress Configuration:** Set up Ingress with an exposed /live endpoint for health checks.

## 📁 Directory Structure
- `k8s/`
  - **spring_deploy.yml**: K8s deployment file for the Spring Boot application.
  - **spring_ingress.yml**: Ingress configuration for routing external traffic to the app, including the `/live` endpoint.
  - **spring_service.yml**: K8s service file for exposing the Spring Boot application within the cluster.
- `spring-boot-app/`
  - `src/`: Contains the Spring Boot application source code (main and test files).
  - **Dockerfile**: Dockerfile to containerize the Spring Boot application for deployment.
- `Dockerfile_slave`:
  - Dockerfile for configuring the Jenkins slave agent, including necessary tools like Docker and `kubectl` to interact with the K8s cluster.
- `Jenkinsfile`:
  - Jenkins script defining CI/CD stages such as linting, testing, building Docker images, and deploying to K8s.

## 📋 Prerequisites
#### Note: We specify the same network `minikube` for the 3 containers so they can communicate with each other on the same isolated network.
1. Create docker container for jenkins master.
    ```python
      docker run -d --name jenkins_master -p 8082:8080 -v jenkins2:/var/jenkins_home --network minikube jenkins/jenkins:lts
    ```
2. Build jenkins slave [image](https://github.com/Nada-Khater/Deploy-SpringBoot-App-to-K8s-using-CI-CD/blob/dev/Dockerfile_slave) and create docker container from it.
    ```python
      docker build -t jenkins_slave -f Dockerfile_slave .
      docker run -d -v /var/run/docker.sock:/var/run/docker.sock --network minikube jenkins_slave
    ```
3. Create docker container for SonarQube.
    ```python
      docker run -d --name sonarqube -p 9000:9000 --network minikube sonarqube
    ```
 <img src="" width="920">

## 📷 Screenshots
#### 1. Add node in jenkins master to act as `jenkins slave`.
  <img src="https://github.com/user-attachments/assets/6897c958-ee5c-4db8-b3a1-e75467ba8768" width="920">
  
#### 2. Add credentials and the private key:
  The key already created in the dockerfile then copy the private key into the slave node using:
  ```python
    docker exec -it <slave_container_name> bash
    cat /root/.ssh/id_rsa
  ```
   <img src="https://github.com/user-attachments/assets/c0fdacb2-b3a2-473b-89b1-32169234a23e" width="920">
   
#### 3. Get the host ip (slave node) by running
  ```python
    docker inspect <slave_container_name>
  ```
   <img src="https://github.com/user-attachments/assets/8a483f4e-d5ea-4c25-8e14-1636c46e886c" width="920">

#### SSH Authenticated Successfully.
   <img src="https://github.com/user-attachments/assets/029d5ef5-23b0-43d0-a59e-b24aefd0914c" width="920">
   <img src="https://github.com/user-attachments/assets/1447b25f-a67a-43e8-bbd4-d711ceaef006" width="920">

#### 4. Install SonarQube & SonarQube Scanner plugin from jenkins.
   <img src="https://github.com/user-attachments/assets/ce2b05e3-3a8c-4e1d-a0dc-ce66af7eeb57" width="920">

#### Configure SonarQube server in jenkins
- Generate SonarQube token
   <img src="https://github.com/user-attachments/assets/1df2483f-f813-4f7f-ba88-2a51b97282a2" width="920">
   <img src="https://github.com/user-attachments/assets/5b4bb3c9-76d2-4720-b864-c0cab46c040d" width="920">
- Install SonarQube Scanner & configure it
   <img src="https://github.com/user-attachments/assets/1594e211-e156-4839-82a1-d760a5bc8d17" width="920">

#### Create a new project in SonarQube
  <img src="https://github.com/user-attachments/assets/2048f13c-e7a7-4dc9-bb91-8dab5b9f5947" width="920">
  <img src="https://github.com/user-attachments/assets/88c674c9-9cc2-4486-b8e0-cd81a940c7b9" width="920">
  
- In spring app add the following in `build.gradle`
  ```python
    plugins {
      id "org.sonarqube" version "5.0.0.4638"
    }
    sonar {
      properties {
        property "sonar.projectKey", "demo-key"
        property "sonar.projectName", "demo"
      }
    }
  ```
#### 5. Install kubernetesCLI plugin from jenkins.
  <img src="https://github.com/user-attachments/assets/45b3fdf9-9833-49ca-aba8-1500d65ba539" width="920">
  <img src="" width="920">
  <img src="" width="920">
   








