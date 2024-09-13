pipeline {
    agent { 
        label 'slave_container'
    }

    environment {
        DOCKER_USER = credentials('DOCKER_USER')
        DOCKER_PASS = credentials('DOCKER_PASS')
    }
    
    parameters {
        choice(name: 'NAMESPACE', choices: ['dev', 'prod'], description: 'Select Kubernetes NameSpace')
    }
    
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'dev', url: 'https://github.com/Nada-Khater/Deploy-SpringBoot-App-to-K8s-using-CI-CD.git'
            }
        }

        stage('Lint Stage') {
            steps {
                script {
                    dir('spring-boot-app') {
                        sh './gradlew check'
                    }
                }
            }
        }
        
        stage('Unit Test Stage') {
            steps {
                script {
                    dir('spring-boot-app') {
                        sh './gradlew test'
                    }
                }
            }
        }
        
        stage('SonarQube Stage') {
            steps {
                script {
                    dir('spring-boot-app') {
                        withSonarQubeEnv('SonarQube') {
                            sh './gradlew sonar'
                        }
                    }
                }
            }
        }
        
        stage('Build Image Stage') {
            steps {
                script {
                    dir('spring-boot-app') {
                        sh 'docker build -t nadakhater/spring-app:lts .'
                    }
                }
            }
        }
        
        stage('Push Image to Registry') {
            steps {
                script {
                    sh 'docker login -u $DOCKER_USER -p $DOCKER_PASS'
                    sh 'docker push nadakhater/spring-app:lts'
                }
            }
        }
        
        stage('Pull Image from Registry') {
            steps {
                script {
                    sh 'docker pull nadakhater/spring-app:lts'
                }
            }
        }
        
        stage('Verify and Create Namespace') {
            steps {
                script {
                    withKubeConfig(credentialsId: 'mykubeconfig') {
                        def namespace = params.NAMESPACE
    
                        // Check if the namespace exists and create it if not
                        sh """
                            if ! kubectl get namespace ${namespace} > /dev/null 2>&1; then
                                echo "Namespace '${namespace}' does not exist. Creating..."
                                kubectl create namespace ${namespace}
                            else
                                echo "Namespace '${namespace}' already exists."
                            fi
                        """
                    }
                }
            }
        }
        
        stage('Deploy to Minikube') {
            steps {
                script {
                    dir('k8s') {
                        withKubeConfig(credentialsId: 'mykubeconfig') {
                            sh """
                                kubectl apply -f spring_deploy.yml -n ${params.NAMESPACE}
                                kubectl apply -f spring_service.yml -n ${params.NAMESPACE}
                                kubectl apply -f spring_ingress.yml -n ${params.NAMESPACE}
                            """
                        }
                    }
                }
            }
        }
    }

    // post {
    //     success {
    //         echo 'Pipeline completed successfully'
    //     }
    //     failure {
    //         echo 'Pipeline failed'
    //     }
    // }
}
