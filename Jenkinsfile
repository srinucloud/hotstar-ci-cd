pipeline {
    agent any

    environment {
        SONARQUBE_SCANNER_HOME = tool 'sonar-scanner'
    }

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

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(credentialsId: 'sonarqube-api') {
                    sh '$SONARQUBE_SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=hotstar -Dsonar.projectKey=hotstar -Dsonar.sources=.'
                }
            }
        }

    }
}