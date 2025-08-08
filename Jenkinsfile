pipeline {
    agent any

    tools {
        nodejs 'nodebuilder'
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
                        configName: 'ubuntu3', // Jenkins SSH config name
                        transfers: [
                            sshTransfer(
                                sourceFiles: 'build/**',
                                removePrefix: 'build',
                                remoteDirectory: '/var/www/react-demo',
                                execCommand: ''
                            )
                        ],
                        verbose: true
                    )
                ])
            }
        }
    }
}
