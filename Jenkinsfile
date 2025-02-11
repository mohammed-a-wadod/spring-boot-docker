pipeline {
    agent {
        label 'oaaaqa-jenkins-agent'
    }

    environment {
        DOCKER_IMAGE = 'mwadod/spring-boot-docker'
        DOCKER_TAG = "1.${BUILD_NUMBER}" // Increment tag using Jenkins build number
    }

    stages {
        stage('Build with Maven') {
            steps {
                script {
                    sh 'mvn clean install -DskipTests'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker context use default"
                    sh """
                        docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: "Dockerhub", url: "https://index.docker.io/v1/"]) {
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
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
