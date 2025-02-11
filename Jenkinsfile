pipeline {
    agent {
        label 'oaaaqa-jenkins-agent'
    }

    environment {
        DOCKER_IMAGE = 'mwadod/spring-boot-docker'
        MAVEN_HOME = '/usr/share/maven'
    }

    stages {
        /* stage('Build with Maven') {
            steps {
                script {
                    sh 'mvn clean install -DskipTests'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh """
                        docker build -t ${DOCKER_IMAGE}:latest .
                    """
                }
            }
        } */

        stage('Check Docker') {
            steps {
                sh 'docker info' // Check if Docker is accessible
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: "Dockerhub", url: "https://index.docker.io/v1/"]) {
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}