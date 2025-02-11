pipeline {
    agent {
        label 'oaaaqa-jenkins-agent'
    }

    environment {
        DOCKER_IMAGE = 'mwadod/spring-boot-docker'
    }

    stages {

        stage('Build with Maven') {
            steps {
                script {
                    sh 'mvn clean install -DskipTests'
                }
            }
        }

        stage('Get Latest Image Version') {
            steps {
                script {
//                     def latestTag = sh(script: "curl -s https://registry.hub.docker.com/v2/repositories/${DOCKER_IMAGE}/tags | jq -r '[.results[].name | select(test(\"^[0-9]+\\\\.[0-9]+$\"))] | sort | last'", returnStdout: true).trim()

                    def latestTag = sh(script: "curl -s https://registry.hub.docker.com/v2/repositories/\$DOCKER_IMAGE/tags | jq -r '[.results[].name | select(test(\"^[0-9]+\\\\.[0-9]+$\"))] | sort | last'", returnStdout: true).trim()

                    if (!latestTag || latestTag == "null") {
                        latestTag = "1.0" // Default to 1.0 if no tags exist
                    } else {
                        def parts = latestTag.tokenize('.')
                        latestTag = "${parts[0]}.${parts[1].toInteger() + 1}"
                    }

                    env.NEW_VERSION = latestTag
                    echo "New Docker image version: ${env.NEW_VERSION}"
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker context use default"
                    sh """
                        docker build -t ${DOCKER_IMAGE}:${NEW_VERSION} -t ${DOCKER_IMAGE}:latest .
                    """
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withDockerRegistry([credentialsId: "Dockerhub", url: "https://index.docker.io/v1/"]) {
                    sh "docker push ${DOCKER_IMAGE}:${NEW_VERSION}"
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully! New image version: ${NEW_VERSION}"
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
