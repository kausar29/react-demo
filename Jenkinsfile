pipeline {
    agent any

    tools {
        nodejs 'nodebuilder'  // Make sure Node.js is configured in Jenkins tools
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
        sshPublisher(publishers: [
            sshPublisherDesc(
                configName: 'ubuntu3', // The name you set in Jenkins Publish over SSH config
                transfers: [
                    sshTransfer(
                        sourceFiles: 'dist/**',
                        removePrefix: 'dist',
                        remoteDirectory: '/var/www/react-demo',
                        execCommand: ''
                    )
                ],
                verbose: true
            )
        ])
    }
}
