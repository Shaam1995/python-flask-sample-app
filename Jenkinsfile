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

        stage('Deploy to EC2') {
            steps {
                sshagent(['github-ssh']) {
                    sh """
ssh -o StrictHostKeyChecking=no ${APP_USER}@${APP_SERVER} <<EOF

set -e

# Clone repository if it does not exist
if [ ! -d "${APP_DIR}/.git" ]; then
    rm -rf ${APP_DIR}
    git clone ${REPO_URL} ${APP_DIR}
else
    cd ${APP_DIR}
    git pull origin master
fi

cd ${APP_DIR}

# Create Python virtual environment if needed
if [ ! -d "venv" ]; then
    python3 -m venv venv
fi

source venv/bin/activate

pip install --upgrade pip

pip install -r requirements.txt

# Stop old Flask process
pkill -f "python3.*flask" || true
pkill -f "python3.*app.py" || true

# Start application
nohup python3 -m flask --app flaskr run --host=0.0.0.0 --port=5000 > output.log 2>&1 &

sleep 5

echo "Application Started"

EOF
"""
                }
            }
        }
    }

    post {
        success {
            echo "Deployment Successful"
        }

        failure {
            echo "Deployment Failed"
        }
    }
}
