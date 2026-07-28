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

                    ssh ubuntu@172.31.2.33 << EOF

                    cd flask-app

                    git pull

                    pkill -f app.py || true

                    nohup python3 app.py > output.log 2>&1 &

                    EOF

                    '''
                }
            }
        }
    }
}
