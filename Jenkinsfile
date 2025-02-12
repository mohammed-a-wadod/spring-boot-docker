pipeline {
    agent {
        label 'oaaaqa-jenkins-agent'
    }

    environment {
        TESTING_IP = credentials('TESTING_IP')
        PROD_IP = credentials('PROD_IP')
        DOCKER_IMAGE = 'mwadod/spring-boot-docker'
        DOCKER_TAG = "1.0.${BUILD_NUMBER}"
        SERVER_IP = "151.104.133.129"
        SERVER_USER = "webtest"
        TARGET_PATH = "/home/webtest/test"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: env.BRANCH_NAME, url: 'git@your-repo.git'
            }
        }
        stage('Set Deployment IP') {
            steps {
                script {
                    echo "branchName: ${env.BRANCH_NAME}"
                    if (env.BRANCH_NAME == 'testing') {
                        env.DEPLOY_IP = TESTING_IP
                    } else if (env.BRANCH_NAME == 'main') {
                        env.DEPLOY_IP = PROD_IP
                    }
                    echo "Deploying to: ${env.DEPLOY_IP}"
                }
            }
        }

        stage('Build with Maven') {
            steps {
                script {
                    sh 'mvn clean install -DskipTests'
                }
            }
        }

        stage('Move File') {
            steps {
                sh "mv target/demo-0.0.1-SNAPSHOT.jar ${env.DEPLOY_IP}"
            }
        }

        /*
        stage('Transfer JAR to Server') {
            steps {
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: 'jumping_server_user', keyFileVariable: 'SSH_KEY')]) {
                        sh """
                            ssh -i "$SSH_KEY" -o StrictHostKeyChecking=no $SERVER_USER@${SERVER_IP} "mkdir -p ${TARGET_PATH}"
                            scp -i "$SSH_KEY" -o StrictHostKeyChecking=no ./target/demo-0.0.1-SNAPSHOT.jar $SERVER_USER@${SERVER_IP}:${TARGET_PATH}/demo-0.0.1-SNAPSHOT.jar
                        """
                    }
                }
            }
        }
        /*

        /* stage('Build Docker Image') {
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
        } */
    }

    post {
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
        always {
            cleanWs()
        }
    }
}
