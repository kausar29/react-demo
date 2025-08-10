pipeline {
    agent any

    tools {
        nodejs 'nodebuilder'
    }

    environment {
        REMOTE_USER = "kausar"
        REMOTE_HOST = "192.168.0.11"
        DEPLOY_DIR = "/var/www/react-demo"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/kausar29/react-demo.git'
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }

        stage('Deploy') {
            steps {
                sshagent(credentials: ['remote_ssh']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST "mkdir -p $DEPLOY_DIR"
                        rsync -avz --delete build/ $REMOTE_USER@$REMOTE_HOST:$DEPLOY_DIR/
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}
