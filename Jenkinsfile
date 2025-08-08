pipeline {
    agent any

    tools {
        nodejs 'Node_18'  // Make sure Node.js is configured in Jenkins tools
    }

    environment {
        REMOTE_DIR = "/var/www/react-demo"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/kausar29/react-demo.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build React App') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy via SSH Plugin') {
            steps {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'ubuntu3',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: "build/**",
                                    removePrefix: "build",
                                    remoteDirectory: "${REMOTE_DIR}"
                                )
                            ],
                            usePromotionTimestamp: false
                        )
                    ]
                )
            }
        }
    }

    post {
        success {
            echo "✅ React app deployed successfully to ${REMOTE_DIR} on 192.168.0.11"
        }
        failure {
            echo "❌ Deployment failed."
        }
    }
}
