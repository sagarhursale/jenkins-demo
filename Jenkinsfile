pipeline {
    agent any

    environment {
        TARGET_SERVER = '15.206.174.214'
        TARGET_USER   = 'jenkins'
        TARGET_DIR    = '/var/www/html'
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
                sh 'ls -la'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing website files'

                sh '''
                    test -f index.html
                    echo "index.html found - TEST PASSED"
                '''
            }
        }

        stage('Deploy to Nginx') {
            steps {
                echo 'Copying website to Nginx server'

                sshagent(credentials: ['linux-server-ssh']) {
                    sh '''
                        scp -o StrictHostKeyChecking=no \
                            index.html \
                            ${TARGET_USER}@${TARGET_SERVER}:${TARGET_DIR}/index.html
                    '''
                }
            }
        }

        stage('Restart Nginx') {
            steps {
                echo 'Restarting Nginx'

                sshagent(credentials: ['linux-server-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            ${TARGET_USER}@${TARGET_SERVER} \
                            "sudo systemctl restart nginx"
                    '''
                }
            }
        }

        stage('Verify') {
            steps {
                echo 'Checking Nginx'

                sshagent(credentials: ['linux-server-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            ${TARGET_USER}@${TARGET_SERVER} \
                            "systemctl is-active nginx"

                        curl -I http://${TARGET_SERVER}
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
