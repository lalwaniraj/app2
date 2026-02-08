pipeline {
    agent any

    environment {
        IMAGE_NAME = "rajkumar179/myapp2"
        IMAGE_TAG  = "latest"
        EC2_HOST   = "3.109.60.232"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/lalwaniraj/app2.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                      echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                      docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'node-key',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'
                )]) {
                    sh """
ssh -i \$SSH_KEY -o StrictHostKeyChecking=no \$SSH_USER@${EC2_HOST} << EOF
docker pull ${IMAGE_NAME}:${IMAGE_TAG}
docker stop test_app || true
docker rm test_app || true
docker run -d --name test_app -p 80:3000 ${IMAGE_NAME}:${IMAGE_TAG}
EOF
                    """
                }
            }
        }
    }
}
