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
        sshagent(['github-ssh']) {
            sh '''
ssh -o StrictHostKeyChecking=no ubuntu@172.31.2.33 <<EOF
cd /home/ubuntu/flask-app
git config --global --add safe.directory /home/ubuntu/flask-app
git pull origin master
pkill -f setup.py || true
nohup python3 setup.py > output.log 2>&1 &
exit
EOF
'''
        }
    }
}
