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
    }
}