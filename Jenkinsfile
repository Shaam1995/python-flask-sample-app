pipeline {
    agent any

    environment {
        APP_SERVER = "172.31.2.33"
        APP_USER   = "ubuntu"
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

cd /home/ubuntu/flask-app

echo "Pull latest code..."
git config --global --add safe.directory /home/ubuntu/flask-app
git pull origin master

echo "Activate virtual environment..."
source devenv/bin/activate

echo "Install dependencies..."
pip install -r requirements.txt

echo "Stop old Flask process..."
pkill -f flask || true

echo "Start Flask application..."
export FLASK_APP=flaskr
nohup flask run --host=0.0.0.0 --port=5000 > output.log 2>&1 &

echo "Deployment completed."

EOF
"""
                }
            }
        }

    }

    post {
        success {
            echo "Deployment to EC2 completed successfully."
        }

        failure {
            echo "Deployment failed."
        }
    }
}
