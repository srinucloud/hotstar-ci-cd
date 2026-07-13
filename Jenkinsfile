@Library('jenkins-shared-library@main') _

pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
                gitCheckout(
                    branch: 'main',
                    url: 'https://github.com/srinucloud/hotstar-ci-cd.git'
                )
            }
        }
    }
}