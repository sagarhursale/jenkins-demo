pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/sagarhursale/jenkins-demo.git'
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
                    jenkins@15.206.174.214:/tmp/index.html

                    ssh -o StrictHostKeyChecking=no \
                    jenkins@15.206.174.214 \
                    "sudo cp /tmp/index.html /usr/share/nginx/html/index.html"
                '''
            }
        }
    }
}
