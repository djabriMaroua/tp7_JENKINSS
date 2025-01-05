pipeline {
    agent any

    environment {
        SONAR_HOST_URL = 'http://197.140.142.82:9000'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/djabriMaroua/TP_7_OGL.git'
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                script {
                    try {
                        bat './gradlew test'
                        junit '**/build/test-results/test/*.xml'
                    } catch (Exception e) {
                        echo "Test stage failed: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        error("Test stage failed")
                    }
                }
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Running SonarQube analysis...'
                script {
                    try {
                        bat "./gradlew sonarqube -Dsonar.host.url=${SONAR_HOST_URL}"
                    } catch (Exception e) {
                        echo "SonarQube analysis failed: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        error("SonarQube analysis failed")
                    }
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Building the project...'
                script {
                    try {
                        bat './gradlew build'
                        archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                    } catch (Exception e) {
                        echo "Build stage failed: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        error("Build stage failed")
                    }
                }
            }
        }

        stage('Deployy') {
            steps {
                echo 'Deploying to MyMavenRepo...'
                bat "./gradlew publish"
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution finished.'
        }

        success {
            echo 'Pipeline succeeded!'
            script {
                // Send Slack notification on success
                slackSend channel: '#jenkins', color: 'good', message: "Build #${env.BUILD_NUMBER} succeeded! \nCheck it out: ${env.BUILD_URL}"

                // Send Email notification on success
                mail to: 'lm_djabri@esi.dz',
                     subject: "Jenkins Build #${env.BUILD_NUMBER} Success",
                     body: "The build #${env.BUILD_NUMBER} was successful.\n\nCheck it out: ${env.BUILD_URL}"
            }
        }

        failure {
            echo 'Pipeline failed!'
            script {
                // Send Slack notification on failure
                slackSend channel: '#jenkins', color: 'danger', message: "Build #${env.BUILD_NUMBER} failed! \nCheck it out: ${env.BUILD_URL}"

                // Send Email notification on failure
                mail to: 'lm_djabri@esi.dz',
                     subject: "Jenkins Build #${env.BUILD_NUMBER} Failure",
                     body: "The build #${env.BUILD_NUMBER} failed.\n\nCheck it out: ${env.BUILD_URL}"
            }
        }
    }
}