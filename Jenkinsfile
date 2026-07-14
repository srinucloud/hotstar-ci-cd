// pipeline {
//     agent any

//     environment {
//         SONARQUBE_SCANNER_HOME = tool 'sonar-scanner'
//     }

//     stages {

//         stage('Git Checkout') {
//             steps {
//                 checkout scm
//             }
//         }

//         stage('Install Dependencies') {
//             steps {
//                 sh 'npm ci'
//             }
//         }

//         stage('Unit Test') {
//             steps {
//                 sh 'npm test -- --watchAll=false --passWithNoTests'
//             }
//         }

//         stage('OWASP Dependency Check') {
//             steps {
//                 dependencyCheck(
//                     odcInstallation: 'dependency-check',
//                     nvdCredentialsId: 'NVDAPIKey',
//                     additionalArguments: '--scan . --format XML --out dependency-check-report'
//                 )
//             }
//         }

//         stage('Publish OWASP Report') {
//             steps {
//                 dependencyCheckPublisher(
//                     pattern: '**/dependency-check-report/*.xml',
//                     stopBuild: false,
//                     skipNoReportFiles: true
//                 )
//             }
//         }

//         stage('SonarQube Analysis') {
//             steps {
//                 withSonarQubeEnv('SonarQube') {
//                     sh """
//                         ${SONARQUBE_SCANNER_HOME}/bin/sonar-scanner \
//                         -Dsonar.projectKey=hotstar \
//                         -Dsonar.projectName=hotstar \
//                         -Dsonar.sources=src
//                     """
//                 }
//             }
//         }

//         stage('Trivy file scan') {
//             steps {
//                 sh 'trivy fs --exit-code 1 --severity HIGH,CRITICAL --no-progress . > trivy-report.txt || true'
//             }
//         }

//         stage('docker build & Push') {
//             steps {
//                 withDockerRegistry(credentialsId: 'docker_creds', url: 'https://index.docker.io/v1/') {
//                     sh '''
//                     docker build -t hotstar:latest .
//                     docker tag hotstar:latest srinu0930/hotstar:latest
//                     docker push srinu0930/hotstar:latest
//                     '''
//                 }
//             }
//         }

//         stage('trivy image scan') {
//             steps {
//                 sh 'trivy image --exit-code 1 --severity HIGH,CRITICAL --no-progress srinu0930/hotstar:latest > trivy-image-report.txt || true'
//             }
//         }
//     }
// }



pipeline {
    agent any

    options {
        disableConcurrentBuilds()
    }

    environment {
        DOCKER_IMAGE = "srinu0930/hotstar"
        GIT_USERNAME = "srinucloud"
        GIT_EMAIL = "srinuengr@gmail.com"
    }

    stages {

        stage('Git Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            when {
                branch 'main'
            }

            steps {
                script {
                    env.IMAGE_TAG = "build-${BUILD_NUMBER}"

                    withDockerRegistry(
                        credentialsId: 'docker_creds',
                        url: 'https://index.docker.io/v1/'
                    ) {
                        sh """
                            docker build -t ${DOCKER_IMAGE}:${IMAGE_TAG} .
                            docker push ${DOCKER_IMAGE}:${IMAGE_TAG}
                        """
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                branch 'main'
            }

            steps {
                withCredentials([string(credentialsId: 'git-creds', variable: 'GIT_TOKEN')]) {

                    sh """
                        set -e

                        git config --global user.name "${GIT_USERNAME}"
                        git config --global user.email "${GIT_EMAIL}"

                        git fetch origin
                        git checkout -B main origin/main

                        sed -i 's|image: .*|image: ${DOCKER_IMAGE}:${IMAGE_TAG}|g' k8s/deployment.yaml

                        git add k8s/deployment.yaml

                        if ! git diff --cached --quiet; then
                            git commit -m "Update deployment image to ${DOCKER_IMAGE}:${IMAGE_TAG}"
                            git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/srinucloud/hotstar-ci-cd.git main
                        else
                            echo "No changes to commit."
                        fi
                    """
                }
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully."
        }

        failure {
            echo "Pipeline failed."
        }

        always {
            cleanWs()
        }
    }
}