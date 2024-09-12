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

## 📷 Screenshots
