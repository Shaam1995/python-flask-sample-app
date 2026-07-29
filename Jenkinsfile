pipeline {
    agent any

    environment {
        APP_SERVER = "172.31.2.33"
        APP_USER   = "ubuntu"
        APP_DIR    = "/home/ubuntu/flask-app"

        IMAGE_NAME = "flaskr"
        CONTAINER  = "flask-container"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['github-ssh']) {
                    sh """
ssh -o StrictHostKeyChecking=no ${APP_USER}@${APP_SERVER} << 'EOF'

set -e

cd ${APP_DIR}

echo "===== Current Directory ====="
pwd

echo "===== Pull Latest Code ====="
git config --global --add safe.directory ${APP_DIR}
git pull origin master

echo "===== Docker Version ====="
docker --version

echo "===== Build Docker Image ====="
docker build -t ${IMAGE_NAME}:latest .

echo "===== Stop Old Container ====="
docker stop ${CONTAINER} || true

echo "===== Remove Old Container ====="
docker rm ${CONTAINER} || true

echo "===== Run New Container ====="
docker run -d \
  --name ${CONTAINER} \
  -p 5000:5000 \
  --restart unless-stopped \
  ${IMAGE_NAME}:latest

echo "===== Running Containers ====="
docker ps

EOF
"""
                }
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully."
        }

        failure {
            echo "Deployment failed."
        }
    }
}
