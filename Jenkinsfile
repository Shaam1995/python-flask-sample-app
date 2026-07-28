pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

       stage('Deploy') {
    steps {
        sshagent(['git']) {
            sh '''
ssh -o StrictHostKeyChecking=no ubuntu@172.31.2.33 <<EOF
cd /home/ubuntu/flask-app
git config --global --add safe.directory /home/ubuntu/flask-app
git pull origin master
pkill -f app.py || true
nohup python3 app.py > output.log 2>&1 &
exit
EOF
'''
        }
    }
}
