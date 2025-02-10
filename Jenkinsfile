pipeline {
    agent {
        label 'oaaaqa-jenkins-agent'
    }

    environment {
        GIT_REPO = 'https://github.com/mohammed-a-wadod/spring-boot-docker.git'
        BRANCH = 'main'
        DOCKER_IMAGE = 'mwadod/spring-boot-docker'
        MAVEN_HOME = '/usr/share/maven'
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
                    sh """
                        /usr/bin/docker build -t ${DOCKER_IMAGE}:latest .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: "Dockerhub", url: "https://index.docker.io/v1/"]) {
                    sh "/usr/bin/docker push ${DOCKER_IMAGE}:latest"
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
