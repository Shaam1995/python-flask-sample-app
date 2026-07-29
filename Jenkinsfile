pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }
stage('Run Docker Container') {
    steps {
        sh '''
        docker stop flask-container || true
        docker rm flask-container || true

        docker run -d \
            --name flask-container \
            -p 5000:5000 \
            flaskr:latest
        '''
    }
}
        stage('Deploy') {
            steps {
                sshagent(['github-ssh']) {
                    sh '''
ssh -o StrictHostKeyChecking=no ubuntu@172.31.2.33 <<EOF
cd /home/ubuntu/flask-app
git config --global --add safe.directory /home/ubuntu/flask-app
git pull origin master
pkill -f setup.py || true
nohup python3 app.py > output.log 2>&1 &
exit
EOF
'''
                }
            }
        }

    }
}
