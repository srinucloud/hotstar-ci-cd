@Library('my-shared-libraries') _

pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
                gitCheckout(
                    branch: 'main',
                    repoUrl: 'https://github.com/srinucloud/hotstar-ci-cd.git'
                )
            }
        }

        stage('Unit Testing') {
            steps {
                unitTesting()
            }
        }
        stage('SonarQube Analysis') {
            steps {
                sonarCodeAnalysis(
                    projectKey: 'hotstar',
                    projectName: 'hotstarapp',
                    sources: 'src'
                )
            }
        }

    }
}