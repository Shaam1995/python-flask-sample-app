pipeline {
    agent any

    environment {
        IMAGE_NAME = "flask-my-app"
        IMAGE_TAG  = "v1.0.1"
    }

    stages {

        stage('Checkout') {
            // Jenkins Server
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            // Jenkins Server (Docker must be installed on the agent)
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} -f Dockerfile .'
            }
        }

        stage('Run Unit Tests') {
            // Jenkins Server, runs commands inside the built image
            steps {
                sh 'docker run --rm ${IMAGE_NAME}:${IMAGE_TAG} sh -c "pip install pytest && pytest"'
            }
        }

        stage('Push Docker Image') {
            // Optional - only runs on main branch. Update registry/credentials as needed.
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker tag ${IMAGE_NAME}:${IMAGE_TAG} $DOCKER_USER/${IMAGE_NAME}:${IMAGE_TAG}
                        docker push $DOCKER_USER/${IMAGE_NAME}:${IMAGE_TAG}
                    '''
                }
            }
        }
    }

    post {
        always {
            sh 'docker image prune -f'
        }
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
