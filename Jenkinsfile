pipeline {
    agent any

    tools {
        nodejs 'nodebuilder' // Jenkins এ NodeJS version configure করে নিতে হবে
    }

    environment {
        REMOTE_USER = "kausar"
        REMOTE_HOST = "192.168.0.11"
        DEPLOY_DIR_REACT = "/var/www/react-demo"
       
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/kausar29/react-demo.git'
            }
        }

        stage('Build React App') {
            steps {
                dir('react-app') {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

       
        stage('Deploy to Remote Server') {
            steps {
                sshagent (credentials: ['remote_ssh']) {
                    // React App Deploy
                    sh """
                        ssh -o StrictHostKeyChecking=no $REMOTE_USER@$REMOTE_HOST 'mkdir -p $DEPLOY_DIR_REACT'
                        rsync -avz --delete react-app/build/ $REMOTE_USER@$REMOTE_HOST:$DEPLOY_DIR_REACT/
                    """
             
                }
            }
        }

        stage('Post-Deployment Check') {
            steps {
                sshagent (credentials: ['remote_ssh']) {
                    sh """
                        ssh $REMOTE_USER@$REMOTE_HOST 'systemctl is-active myapp.service'
                        curl -I http://$REMOTE_HOST || true
                    """
                }
            }
        }
    }

    post {
        failure {
            echo "❌ Deployment Failed! Initiating Rollback..."
            sshagent (credentials: ['remote_ssh']) {
                sh """
                    ssh $REMOTE_USER@$REMOTE_HOST 'cp -r ${DEPLOY_DIR_REACT}_backup/* $DEPLOY_DIR_REACT/ || true'
                    ssh $REMOTE_USER@$REMOTE_HOST 'cp -r ${DEPLOY_DIR_NODE}_backup/* $DEPLOY_DIR_NODE/ || true'
                    ssh $REMOTE_USER@$REMOTE_HOST 'cp -r ${DEPLOY_DIR_LARAVEL}_backup/* $DEPLOY_DIR_LARAVEL/ || true'
                    ssh $REMOTE_USER@$REMOTE_HOST 'sudo systemctl restart myapp.service || true'
                """
            }
        }
        success {
            echo "✅ Deployment Successful!"
        }
    }
}
