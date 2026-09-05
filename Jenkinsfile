pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out code from GitHub'
            }
        }

        stage('Build') {
            steps {
                echo 'Building application'
                sh 'ls -la'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing application'
                sh 'test -f index.html'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying to Nginx'

                sh '''
                    sudo cp index.html /var/www/html/index.html
                    sudo systemctl restart nginx
                '''
            }
        }

        stage('Verify') {
            steps {
                echo 'Checking deployment'

                sh '''
                    curl -I http://localhost
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment SUCCESSFUL'
        }

        failure {
            echo 'Deployment FAILED'
        }
    }
}
