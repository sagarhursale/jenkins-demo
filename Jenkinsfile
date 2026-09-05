pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'YOUR_GITHUB_REPO_URL'
            }
        }

        stage('Test') {
            steps {
                sh 'test -f index.html'
                echo 'Test successful'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    scp -o StrictHostKeyChecking=no index.html \
                    jenkins@YOUR_SERVER_IP:/tmp/index.html

                    ssh -o StrictHostKeyChecking=no \
                    jenkins@YOUR_SERVER_IP \
                    "sudo cp /tmp/index.html /usr/share/nginx/html/index.html"
                '''
            }
        }
    }
}
