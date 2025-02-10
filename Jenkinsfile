pipeline {
    agent {
        label 'oaaaqa_jenkins-agent' // Runs the pipeline on the agent
    }

    environment {
        GIT_REPO = 'https://github.com/mohammed-a-wadod/spring-boot-docker'
        BRANCH = 'main'
        DOCKER_IMAGE = 'mwadod/spring-boot-docker'
        MAVEN_HOME = '/usr/share/maven'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: "${BRANCH}", credentialsId: 'GitHub', url: "${GIT_REPO}"
            }
        }

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
                        docker build -t ${DOCKER_IMAGE}:latest .
                    """
                }
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
