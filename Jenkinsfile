pipeline {
    agent { 
        label 'slave_container'
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
