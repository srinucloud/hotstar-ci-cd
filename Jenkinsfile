pipeline {
    agent any
    stages {
        stage('clear Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/srinucloud/hotstar-ci-cd.git'
            }
        }

        stage('Install') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Test & Quality') {
            steps {
                sh '''
                    npm run lint
                    npm test
                    npm run coverage
                '''
            }
        }

        stage('Build') {
            steps {
                    sh 'npm run build'
            }
        }
    }
}