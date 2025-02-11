pipeline {
    agent {
        label 'oaaaqa-jenkins-agent'
    }

    environment {
        DOCKER_IMAGE = 'mwadod/spring-boot-docker'
        DOCKER_TAG = "1.0.${BUILD_NUMBER}" // Using BUILD_NUMBER for auto-incrementing version
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
                    sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
                    sh "docker tag ${DOCKER_IMAGE}:${DOCKER_TAG} ${DOCKER_IMAGE}:latest"  // Tag latest build as 'latest'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: "Dockerhub", url: "https://index.docker.io/v1/"]) {
                    sh "docker push ${DOCKER_IMAGE}:${DOCKER_TAG}"
                    sh "docker push ${DOCKER_IMAGE}:latest"  // Push latest tag
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