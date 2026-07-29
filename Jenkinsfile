pipeline {
    agent any

    environment {
        APP_SERVER = "172.31.2.33"
        APP_USER   = "ubuntu"
        APP_DIR    = "/home/ubuntu/python-flask-sample-app"
        REPO_URL   = "https://github.com/Shaam1995/python-flask-sample-app.git"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy Docker Container') {
            steps {
                sshagent(['github-ssh']) {

                    sh """
ssh -o StrictHostKeyChecking=no ${APP_USER}@${APP_SERVER} << 'EOF'

set -e

# Clone or update repository
if [ ! -d "${APP_DIR}/.git" ]; then
    git clone ${REPO_URL} ${APP_DIR}
else
    cd ${APP_DIR}
    git pull origin master
fi

cd ${APP_DIR}

echo "Building Docker image..."

docker build -t flaskr .

echo "Stopping old container..."

docker stop flask-app || true
docker rm flask-app || true

echo "Starting new container..."

docker run -d \
    --name flask-app \
    -p 5000:5000 \
    --restart unless-stopped \
    flaskr

docker ps

EOF
"""
                }
            }
        }
    }

    post {
        success {
            echo "Docker Deployment Successful"
        }

        failure {
            echo "Docker Deployment Failed"
        }
    }
}
