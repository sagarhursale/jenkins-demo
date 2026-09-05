```groovy
pipeline {

    agent {
        label 'jenkins-jenkins-agent'
    }

    environment {
        TARGET_SERVER = '15.206.174.214'
        TARGET_USER   = 'jenkins'
        NGINX_PATH    = '/var/www/html/index.html'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checkout code from GitHub'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Build stage'

                sh '''
                    echo "Files in workspace:"
                    ls -la
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Testing website files'

                sh '''
                    test -f index.html
                    echo "index.html found - TEST PASSED"

                    test -s index.html
                    echo "index.html is not empty - TEST PASSED"
                '''
            }
        }

        stage('Deploy to Nginx') {
            steps {
                echo 'Deploying website to Nginx server'

                sshagent(['linux-server-ssh']) {
                    sh '''
                        echo "Copying index.html to target server..."

                        scp -o StrictHostKeyChecking=no \
                            index.html \
                            ${TARGET_USER}@${TARGET_SERVER}:/tmp/index.html

                        echo "Moving file to Nginx document root..."

                        ssh -o StrictHostKeyChecking=no \
                            ${TARGET_USER}@${TARGET_SERVER} \
                            "sudo -n mv /tmp/index.html ${NGINX_PATH}"

                        echo "Deployment completed successfully"
                    '''
                }
            }
        }

        stage('Restart Nginx') {
            steps {
                echo 'Restarting Nginx'

                sshagent(['linux-server-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            ${TARGET_USER}@${TARGET_SERVER} \
                            "sudo -n systemctl restart nginx"

                        echo "Nginx restarted successfully"
                    '''
                }
            }
        }

        stage('Verify') {
            steps {
                echo 'Verifying Nginx deployment'

                sshagent(['linux-server-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            ${TARGET_USER}@${TARGET_SERVER} \
                            "sudo -n test -f ${NGINX_PATH}"

                        echo "Testing website..."

                        ssh -o StrictHostKeyChecking=no \
                            ${TARGET_USER}@${TARGET_SERVER} \
                            "curl -fsS http://localhost"

                        echo ""
                        echo "======================================"
                        echo " NGINX DEPLOYMENT SUCCESSFUL"
                        echo "======================================"
                    '''
                }
            }
        }
    }

    post {

        success {
            echo '======================================'
            echo ' NGINX DEPLOYMENT SUCCESSFUL ✅'
            echo '======================================'
        }

        failure {
            echo '======================================'
            echo ' NGINX DEPLOYMENT FAILED ❌'
            echo '======================================'
        }
    }
}
```
