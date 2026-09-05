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
                echo 'Checking out code from GitHub...'
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'
                sh '''
                    echo "Files in workspace:"
                    ls -la
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Testing application...'
                sh '''
                    test -f index.html
                    echo "index.html exists - Test PASSED"
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying files to target server...'

                sshagent(credentials: ['linux-server-ssh']) {
                    sh '''
                        scp -o StrictHostKeyChecking=no \
                            index.html \
                            ${TARGET_USER}@${TARGET_SERVER}:${TARGET_DIR}/

                        echo "Files copied successfully!"
                    '''
                }
            }
        }

        stage('Verify') {
            steps {
                echo 'Verifying deployment...'

                sshagent(credentials: ['linux-server-ssh']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                            ${TARGET_USER}@${TARGET_SERVER} \
                            "ls -l ${TARGET_DIR}/index.html && curl -I http://localhost"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '===================================='
            echo ' DEPLOYMENT SUCCESSFUL ✅'
            echo '===================================='
        }

        failure {
            echo '===================================='
            echo ' DEPLOYMENT FAILED ❌'
            echo '===================================='
        }
    }
}
