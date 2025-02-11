pipeline {
    agent any  // Run on any available agent (host)

    environment {
        DOCKER_IMAGE = 'mwadod/spring-boot-docker'
        MAVEN_HOME = '/usr/share/maven'
    }

    stages {
        stage('Build with Maven') {
            agent {
                docker { image 'maven:3.8.6-openjdk-11' }  // Maven Docker image
            }
            steps {
                sh 'mvn --version'  // Print Maven version
                sh 'mvn clean install'  // Build the project inside the container
            }
        }

        stage('Copy .jar to Host') {
            steps {
                script {
                    // Copy demo-0.0.1-SNAPSHOT.jar file from the container's workspace to the host system
                    sh 'cp target/demo-0.0.1-SNAPSHOT.jar /tmp/demo-0.0.1-SNAPSHOT.jar'
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

/*         stage('Build Docker Image') {
            steps {
                script {
                    // Run Docker build command on the host system (not inside the container)
                    sh 'docker build -t my-app-image /tmp'
                }
            }
        } */
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
