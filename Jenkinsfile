pipeline {
    agent any

    environment {
        DOCKER_USER = "ilayoh"
        JAVA_HOME   = "C:\\Program Files\\Eclipse Adoptium\\jdk-25.0.5.8-hotspot"
        PATH        = "C:\\Program Files\\Eclipse Adoptium\\jdk-25.0.5.8-hotspot\\bin;${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Test') {
            steps {
                bat 'mvn clean test' // Running tests
            }
            post {
                always {
                    junit allowEmptyResults: true, testResults: '**/target/surefire-reports/*.xml' // Saving test results
                }
                success {
                    echo 'Calculator tests passed successfully!'
                }
            }
        }

        stage('Docker Build') {
            steps {
                bat "docker buildx build --load -t ${env.DOCKER_USER}/calculator-app:${env.BUILD_NUMBER} ." // non generic "latest" tags, alternative to using HASH as seen in the other project.
            }
        }

        stage('Docker Push') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-hub-credentials') {
                        bat "docker push ${env.DOCKER_USER}/calculator-app:${env.BUILD_NUMBER}" 
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