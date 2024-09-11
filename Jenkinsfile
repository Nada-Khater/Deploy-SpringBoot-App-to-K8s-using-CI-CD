pipeline {
    agent { 
        label 'slave_container'
    }
    
    environment {
        DOCKER_USER = credentials('DOCKER_USER')
        DOCKER_PASS = credentials('DOCKER_PASS')
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
