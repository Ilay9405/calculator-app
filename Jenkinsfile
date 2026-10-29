pipeline {
    agent any

    environment {
        DOCKER_USER = "ilayoh"
    }
    tools {
        maven 'Maven 3.8.7'
        jdk 'Eclipse Temurin JDK 21'
        
    }


    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                sh 'mvn clean test' // Running tests
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml' // Saving test results
                }
                success {
                    echo 'Calculator tests passed successfully!'
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker buildx build --load -t ${env.DOCKER_USER}/calculator-app:${env.BUILD_NUMBER} ." // non generic "latest" tags
            }
        }
        
        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') { // pushing to docker hub while hiding credentials from Git by using an environemnt variable
                        sh "docker push ${env.DOCKER_USER}/calculator-app:${env.BUILD_NUMBER}" 
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}