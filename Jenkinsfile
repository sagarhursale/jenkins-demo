```groovy
pipeline {
    agent any

    stages {

        stage('Test') {
            steps {
                echo 'Checking application files...'
                sh 'test -f index.html'
                echo 'Test successful'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'

                sshagent(['linux-server-ssh']) {
                    sh '''
                        scp -o StrictHostKeyChecking=no index.html jenkins@15.206.174.214:/tmp/index.html

                        ssh -o StrictHostKeyChecking=no jenkins@15.206.174.214 \
                        "sudo cp /tmp/index.html /usr/share/nginx/html/index.html"
                    '''
                }

                echo 'Deployment successful'
            }
        }

        stage('Verify') {
            steps {
                echo 'Verifying deployment...'

                sshagent(['linux-server-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no jenkins@15.206.174.214 \
                        "curl -s http://localhost"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'DEPLOYMENT SUCCESSFUL'
        }

        failure {
            echo 'DEPLOYMENT FAILED'
        }
    }
}
```
