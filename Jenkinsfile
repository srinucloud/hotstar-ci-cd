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

        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck(
                    odcInstallation: 'dependency-check',
                    nvdCredentialsId: 'NVDAPIKey',
                    additionalArguments: '--scan . --format XML --out dependency-check-report'
                )
            }
        }

        stage('Publish OWASP Report') {
            steps {
                dependencyCheckPublisher(
                    pattern: '**/dependency-check-report/*.xml',
                    stopBuild: false,
                    skipNoReportFiles: true
                )
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh """
                        ${SONARQUBE_SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=hotstar \
                        -Dsonar.projectName=hotstar \
                        -Dsonar.sources=src
                    """
                }
            }
        }

        stage('Trivy file scan') {
            steps {
                sh 'trivy fs --exit-code 1 --severity HIGH,CRITICAL --no-progress . > trivy-report.txt || true'
            }
        }
    }
}