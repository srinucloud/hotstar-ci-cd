pipeline {
    agent any

    stages {

        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'npm test -- --watchAll=false --passWithNoTests'
            }
        }

        stage('Static Code Analysis') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Security Scan') {
            steps {
                sh 'npm audit --audit-level=critical'
            }
        }

        stage('Build Application') {
            steps {
                sh 'npm run build'
            }
        }
    }
}